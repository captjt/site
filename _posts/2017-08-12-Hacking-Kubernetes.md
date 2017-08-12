---
layout: post
cover: 'assets/images/kubernetes-header.png'
title: 'Hacking Kubernetes'
date: 2017-08-12 05:00:00
tags: technologies golang kubernetes
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/logo.png'
---

## What is Kubernetes?

Most people in tech have at least heard of Kubernetes. If you haven't here's an excerpt from [Kubernetes.io](https://kubernetes.io/) -- *"Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications"*.

To understand what this article is about you'll need to know what [Kubernetes objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) are.

## Why Hack Against k8s?

The biggest reason for hacking against the Kubernetes' API is purely for custom automated actions to build on top of the already automated infrastructure it provides.

I wanted to build a manager that can create/delete/update deployments and services within a specific namespace in Kubernetes. You can think of a web service that run a very small portion of what `kubectl` does.

*Note: There are **so** many things you can do with Kubernetes' API -- what I am going to walk through is only scratching the surface. These code excerpts are also implemented in Go and import the [client-go](https://github.com/kubernetes/client-go) library.*

## The Controller

I decided my Kubernetes *Manager* needed to implement 3 basic methods to receive, update and delete deployments/services in Kubernetes. So I made 4 interfaces.

~~~
// A Controller is the interface to manage Kubernetes resources within a
// Kubernetes clusters.
type Controller interface {
  Deleter
  Deployer
  Getter
}

// Deleter represents a Kubernetes delete operation.
type Deleter interface {
  Delete(name string) error
}

// Deployer represents a Kubernetes deploy operation.
type Deployer interface {
  Deploy(image, name string, port int) error
}

// Getter represents a Kubernetes get operation.
type Getter interface {
  Get(name string) error
}

// Manager models the Kubernetes required client settings to interact with a
// specific cluster. This Manager will implement our Controller interface.
type Manager struct {
  clientSet       *kubernetes.Clientset
  namespace       string
  imagePullSecret string
}
~~~
{: .language-go}

## Deployer

When deploying an application onto our Kubernetes cluster I decided to make it simple enough to just take an image's name, a unique application's name and what port the image needs to be exposed on.

~~~
// Modeling the deployment operation we need.
type deployOperation struct {
  image    string
  name     string
  port     int
  replicas int
}

// Deploy will deploy a service and a deployment with a specific image, name and
// port specified for deployment.
func (m *Manager) Deploy(image, name string, port int) error {

  op := &deployOperation{
    image: image,
    name:  name,
	  port:  port,
    replicas: 2,
  }

  if err := m.doDeployment(op); err != nil {
    return err
  }

  if err := m.doService(op); err != nil {
	return err
  }

  return nil
}

// Create/update the new Deployment within the namespace. The resources defined
// for the deployment are very minimal with CPU limited at 100m and CPU memory
// limited at 256Mi.
func (m *Manager) doDeployment(op *deployOperation) error {

  appName := op.name

  // Define the Deployments spec.
  deploySpec := &v1beta1.Deployment{
    TypeMeta: unversioned.TypeMeta{
    Kind:       "Deployment",
      APIVersion: "extensions/v1beta1",
    },
    ObjectMeta: v1.ObjectMeta{
      Name: appName,
    },
    Spec: v1beta1.DeploymentSpec{
      Replicas: int32p(op.replicas),
      Strategy: v1beta1.DeploymentStrategy{
        Type: v1beta1.RollingUpdateDeploymentStrategyType,
        RollingUpdate: &v1beta1.RollingUpdateDeployment{
          MaxUnavailable: &intstr.IntOrString{
            Type:   intstr.Int,
            IntVal: int32(0),
          },
          MaxSurge: &intstr.IntOrString{
            Type:   intstr.Int,
            IntVal: int32(1),
          },
        },
      },
      RevisionHistoryLimit: int32p(10),
      Template: v1.PodTemplateSpec{
        ObjectMeta: v1.ObjectMeta{
          Name:   appName,
          Labels: map[string]string{"app": appName},
        },
        Spec: v1.PodSpec{
          ImagePullSecrets: []v1.LocalObjectReference{
            {
              Name: m.imagePullSecret,
            },
          },
          Containers: []v1.Container{
            v1.Container{
              Name:  op.name,
              Image: op.image,
              Ports: []v1.ContainerPort{
                v1.ContainerPort{ContainerPort: int32(op.port), Protocol: v1.ProtocolTCP},
              },
              Resources: v1.ResourceRequirements{
                Limits: v1.ResourceList{
                  v1.ResourceCPU:    resource.MustParse("100m"),
                  v1.ResourceMemory: resource.MustParse("256Mi"),
                },
              },
              ImagePullPolicy: v1.PullIfNotPresent,
            },
          },
          RestartPolicy: v1.RestartPolicyAlways,
          DNSPolicy:     v1.DNSClusterFirst,
        },
      },
    },
  }

  deploy := m.clientSet.Extensions().Deployments(m.namespace)
  _, err := deploy.Update(deploySpec)
  switch {
  case err == nil:
    log.Println("deployment controller updated")
  case !errors.IsNotFound(err):
    return fmt.Errorf("could not update deployment controller: %s", err)
  default:
    _, err = deploy.Create(deploySpec)
    if err != nil {
      return fmt.Errorf("could not create deployment controller: %s", err)
    }
    log.Println("deployment controller created")
  }

  return nil
}

// Create/update the service within a namespace. It will be exposed on port 80,
// but will be using the target port of what was specified.
func (m *Manager) doService(op *deployOperation) error {

    appName := op.name

	// Define service spec.
	serviceSpec := &v1.Service{
    TypeMeta: unversioned.TypeMeta{
      Kind:       "Service",
      APIVersion: "v1",
    },
    ObjectMeta: v1.ObjectMeta{
      Name: appName,
    },
    Spec: v1.ServiceSpec{
      Type:     v1.ServiceTypeClusterIP,
      Selector: map[string]string{"app": appName},
      Ports: []v1.ServicePort{
        v1.ServicePort{
          Protocol: v1.ProtocolTCP,
          Port:     80,
          TargetPort: intstr.IntOrString{
            Type:   intstr.Int,
            IntVal: int32(op.port),
          },
        },
      },
    },
  }

  service := m.clientSet.Core().Services(m.namespace)
  svc, err := service.Get(appName)
  switch {
  case err == nil:
    serviceSpec.ObjectMeta.ResourceVersion = svc.ObjectMeta.ResourceVersion
    serviceSpec.Spec.ClusterIP = svc.Spec.ClusterIP
    _, err = service.Update(serviceSpec)
    if err != nil {
      return fmt.Errorf("failed to update service: %s", err)
    }
    log.Println("service updated")
  case errors.IsNotFound(err):
    _, err = service.Create(serviceSpec)
    if err != nil {
      return fmt.Errorf("failed to create service: %s", err)
    }
    log.Println("service created")
  default:
    return fmt.Errorf("unexpected error: %s", err)
  }

  return nil
}

// Util function to return an int32 pointer (Kubernetes API specific functionality)
func int32p(i int32) *int32 {
  return &i
}
~~~
{: .language-go}

## Deleter

The `Deleter` interface is super simple, just like an unqiue delete operation on any kind of system.

*Note: there is one nuance that when deleting a deployment you must first scale down the replica set to 0 replicas. This issue is noted here in [Github](https://github.com/kubernetes/client-go/issues/50).*

~~~
// Model of a delete operation. Must take the unique application name to select
// the correct application we created already in the cluster.
type deleteOperation struct {
  name       string
  deleteOpts *v1.DeleteOptions
}

// Delete will delete a service and a deployment with a unique name for the
// deployment and service.
func (m *Manager) Delete(name string) error {
  op := &deleteOperation{
    name: name,
    deleteOpts: &v1.DeleteOptions{
      GracePeriodSeconds: int64p(0),
      OrphanDependents:   boolp(true),
    },
  }

  if err := m.doDeleteDeployment(op); err != nil {
    return err
  }

  if err := m.doDeleteService(op); err != nil {
    return err
  }

  return nil
}

// Delete a deployment within a namespace.
func (m *Manager) doDeleteDeployment(op *deleteOperation) error {

  appName := op.name

  deploy, err := m.clientSet.Extensions().Deployments(m.namespace).Get(appName)
  if err == nil {
    // scale deployment to 0
    // issue: https://github.com/kubernetes/client-go/issues/50
    replicas := int32p(0)
    deploy.Spec.Replicas = replicas
    _, _ = m.clientSet.Extensions().Deployments(m.namespace).Update(deploy)
  }

  err = m.clientSet.Extensions().Deployments(m.namespace).Delete(appName, op.deleteOpts)
  switch {
  case err == nil:
    log.Printf("%s deployment deleted\n", appName)
  case !errors.IsNotFound(err):
    return fmt.Errorf("could not delete deployment controller: %s", err)
  default:
    return nil
  }
  return nil
}

// Delete a service within a namespace.
func (m *Manager) doDeleteService(op *deleteOperation) error {

  appName := op.name

  err := m.clientSet.Core().Services(m.namespace).Delete(appName, op.deleteOpts)
  switch {
  case err == nil:
    log.Printf("%s service deleted\n", appName)
  case !errors.IsNotFound(err):
    return fmt.Errorf("could not delete service: %s", err)
  default:
    return nil
  }

  return nil
}

// Util function to return an int64 pointer.
func int64p(i int64) *int64 {
  return &i
}

// Util function to return a boolean pointer.
func boolp(b bool) *bool {
  return &b
}
~~~
{: .language-go}

## Getter

After we create and delete -- we might want at least see what sort of applications we have deployed with our Manager. We can implement a basic `Get` method to select an application and see if it's there in the cluster running. We can do a lot more with this kind of idea later, but for now I am just making sure the deployment is there.

~~~
// Model of a get deployment operation. The name is a unique identifier for an
// application.
type getOperation struct {
  name string
}

// Get will get a deployment for a specific deployment/service pair. If it is not
// present then return with an error.
func (m *Manager) Get(name string) error {

  op := &getOperation{
    name: name,
  }

  if err := m.doGetDeployment(op); err != nil {
    return err
  }

  return nil
}

// Get a deployment method.
func (m *Manager) doGetDeployment(op *getOperation) error {

  appName := op.name

  _, err := m.clientSet.Extensions().Deployments(m.namespace).Get(appName)
  switch {
  case err == nil:
    log.Printf("%s deployment found\n", appName)
  case !errors.IsNotFound(err):
    return fmt.Errorf("could not get deployment controller: %s", err)
  default:
    return nil
  }
  return nil
}
~~~
{: .language-go}

---

![super-happy](./../assets/images/super-happy.jpg)

## Conclusion

We should have a very basic Manager that implements the Controller interface we defined. This can be super robust later on, but for now we have a very simple to understand way to create, delete and retrieve information about our applications in a Kubernetes' namespace.
