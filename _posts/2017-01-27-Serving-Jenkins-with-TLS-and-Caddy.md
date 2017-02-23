---
layout: post
cover: 'assets/images/encryption.jpg'
title: Serving Jenkins with TLS-Encryption Using Caddy
date:   2017-01-27 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/logo.png'
---

## What is Caddy?

Caddy, or as others may know Caddyserver, is a web server similar to nginx or Apache that serves files over HTTP. HTTP is the basic and standard way people are served/distributed website content. Caddy's main purpose is to streamline how developers authenticate their web deployment process. It is the only web server software that will automatically encrypt your site with SSL/TLS encryption. This means that without too much knowledge behind web security and how to secure your server - developers can receive and authenticate TLS certifications automatically. We are in the age of many, many technologies throughout the full stack of web development it is hard to keep up with everything outside of development processes. Caddy is here to save us from the cumbersome process of deploying our applications properly AND with encryption included.

## Caddy Has Options

Other than the automatic TLS encryption, one of the coolest things the community around Caddy has been developing is "Directives". These can be thought of as plugins or extensions to make developer's lives even more easy. Things like file JWT authentication, CORS (Cross Origin Resource Sharing), AWS Lambda features, and others right out of these easily configured extensions.

## Serving Jenkins With Caddy

Because Caddy is such an amazing software product I tend to lean more towards using it everywhere I can. With that being said, I did find it a bit quirky when settings it up to serve a CI/CD application -- Jenkins.

Jenkins is a fantastic open source product that has been a leader in the CI/CD community ever since being released as the first one of it's kind. It's a Java based project that when configured properly can do some pretty robust pipelining and custom build processes. I will not go over how to setup Jenkins on your machine there are some fantastic resources already out there on this kind of thing -- but [here](https://trycrmr.github.io/hubpress.io/2017/01/20/Automated-Testing-Using-Jenkins-on-AWS.html) is a great walk through on how to setup Jenkins on CentOS 7 in your AWS environment.

## Caddy

Caddy comes out with releases on a regular basis this download version might not be up to date, but I will try to keep this as up to date as possible with newer things that change with Caddy and/or Jenkins when configuring the two together.

## Initial Install

Download the tarball for the release of caddy you want. **NOTE:** You can also do this from their site @ [caddyserver](https://www.caddyserver.com) and hand jam a custom built Caddy + directives over to your server.

~~~
wget https://caddyserver.com/download/builds/172401104141966/caddy_linux_amd64_custom.tar.gz
tar xf caddy_linux_amd64_custom.tar.gz
~~~

We are going to need to copy our `caddy` binary to our local/bin to be in our executable PATH. After that we will need to change the owner of the binary to `root` so that we can enable a *system service* to start/stop/restart/etc. our caddy server.

~~~
sudo cp caddy /usr/local/bin/
sudo chown root:root /usr/local/bin/caddy
sudo chmod 755 /usr/local/bin/caddy
~~~
{: .language-bash}

## Configuring Caddy and Groups/Users

This will setup Caddy to be able to bind to our HTTP and SSL ports without being `root`. **Don't forget to configure the firewalls on your server to allow for traffic through both ports 80 and 433.** This is easily done in AWS through security groups and on the CentOS server through _firewalld_.

~~~
sudo setcap 'cap_net_bind_service=+ep' /usr/local/bin/caddy
~~~
{: .language-bash}

We then need to setup the group, user and directories that Caddy will need to have.

~~~
sudo groupadd -g 33 www-data
sudo useradd \
  -g www-data --no-user-group \
  --home-dir /var/www --no-create-home \
  --shell /usr/sbin/nologin \
  --system --uid 33 www-data

sudo mkdir /etc/caddy
sudo chown -R root:www-data /etc/caddy
sudo mkdir /etc/ssl/caddy
sudo chown -R www-data:root /etc/ssl/caddy
sudo chmod 0770 /etc/ssl/caddy
~~~
{: .language-bash}

## The Caddyfile

Sample Caddyfile with subdomain wildcard (*.jt.codes)

This will proxy all of the root traffic from port 80 to port 8080 where our Jenkins application is running -- while passing the host information that most backend applications would expect when we specify `transparent`.

~~~
jenkins.jt.codes {
	proxy / :8080 {
		transparent
	}
}
~~~

We need to next move our Caddyfile and give it proper ownership to be used. **NOTE:** I like to keep my Caddyfile in my `$HOME` directory so it's easily found/changed in the future - just make sure you copy the Caddyfile into the `/etc/caddy/` directory if you alter it again and restart the service.

~~~
sudo cp /path/to/Caddyfile /etc/caddy/
sudo chown www-data:www-data /etc/caddy/Caddyfile
sudo chmod 444 /etc/caddy/Caddyfile
~~~
{: .language-bash}

## Note Serving from **/var/www/**

If we wanted to host our sites inside of a home directory similar to how *nginx and Apache* do we can also configure an `/var/www/` site as well. Although because we are just going to reverse proxy to our running Jenkins application on **port 8080** we will not have to serve content through that configuration.

## Caddy as a Service

One thing we wanted to make sure we had is a Caddy systemd service configuration -- so next we will configure a `caddy.service` file. Similar to our Caddyfile I like to keep a reference file in my $HOME directory to easily change and configure anything again.

~~~
$ cd $HOME
$ vim caddy.service
~~~
{: .language-bash}

and inside of our `caddy.service` file we will paste this ...

~~~
[Unit]
Description=Caddy HTTP/2 web server
Documentation=https://caddyserver.com/docs
After=network-online.target
Wants=network-online.target systemd-networkd-wait-online.service

[Service]
Restart=on-failure
StartLimitInterval=86400
StartLimitBurst=5

; User and group the process will run as.
User=www-data
Group=www-data

; Letsencrypt-issued certificates will be written to this directory.
Environment=CADDYPATH=/etc/ssl/caddy

; Always set "-root" to something safe in case it gets forgotten in the Caddyfile.
ExecStart=/usr/local/bin/caddy -log stdout -agree=true -conf=/etc/caddy/Caddyfile -root=/var/tmp
ExecReload=/bin/kill -USR1 $MAINPID

; Limit the number of file descriptors; see `man systemd.exec` for more limit settings.
LimitNOFILE=1048576
; Unmodified caddy is not expected to use more than that.
LimitNPROC=64

; Use private /tmp and /var/tmp, which are discarded after caddy stops.
PrivateTmp=true
; Use a minimal /dev
PrivateDevices=true
; Hide /home, /root, and /run/user. Nobody will steal your SSH-keys.
ProtectHome=true
; Make /usr, /boot, /etc and possibly some more folders read-only.
ProtectSystem=full
; … except /etc/ssl/caddy, because we want Letsencrypt-certificates there.
;   This merely retains r/w access rights, it does not add any new. Must still be writable on the host!
ReadWriteDirectories=/etc/ssl/caddy

; The following additional security directives only work with systemd v229 or later.
; They further retrict privileges that can be gained by caddy. Uncomment if you like.
; Note that you may have to add capabilities required by any plugins in use.
;CapabilityBoundingSet=CAP_NET_BIND_SERVICE
;AmbientCapabilities=CAP_NET_BIND_SERVICE
;NoNewPrivileges=true

[Install]
WantedBy=multi-user.target
~~~

A couple things to note when we are looking at that configuration file - we are going to store our SSL certificates inside of the `/etc/ssl/caddy` directory. **Make sure you keep these certificates secure and backed up.**

Caddy will auto detect when our certificates need to be renewed but we must make sure we do keep them secure from people.

## Onto the Magic

**A quick note:** make sure inside of your **Jenkins > Configure System** you setup your `Jenkins URL` to the "*https*" version of your domains. So for us it will be "*https://jenkins.jt.codes*".

**NOTE:** make sure to add the "s" after "http" -- this will through reverse proxy issues with the Jenkins application.

We are going to copy our `caddy.service` file to the `system` directory, establish proper ownership/permissions and start out Caddy service.

~~~
sudo cp caddy.service /etc/systemd/system/
sudo chown root:root /etc/systemd/system/caddy.service
sudo chmod 744 /etc/systemd/system/caddy.service
sudo systemctl daemon-reload
sudo systemctl start caddy.service
~~~
{: .language-bash}

To allow for the Caddy service to start on reboot we can `enable` it.

~~~
sudo systemctl enable caddy.service
~~~
{: .language-bash}

The service works just like any other `systemd` service and we can **start, restart, and stop** - along with seeing the status of the running service with **status**.

## Going Forward

Once you verified and established that your Jenkins server is now SSL encrypted - you can dance around for a second. I must say Caddy has been a huge savior when it comes to getting an application out there and secured properly. You can find Caddy on github @ [Caddy](https://github.com/mholt/caddy) or [caddyserver](https://github.com/caddyserver)'s Organization.

HUGE S/o to [Matt Holt](https://github.com/mholt) for doing some amazing things and creating this awesome tech.

I will put out more examples and resources on Caddy, the ACME protocol and how to use Caddy with other types of applications.
