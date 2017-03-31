---
layout: post
cover: 'assets/images/james-webb-space-telescope.jpg'
title: 'Go: Testing Approaches'
date:   2017-03-31 05:00:00
tags: technologies golang
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/logo.png'
---

## Software Testing Basics

Wikipedia says software testing is, _"an investigation conducted to provide stakeholders with information about the quality of the product or service under test. Software testing can also provide an objective, independent view of the software to allow the business to appreciate and understand the risks of software implementation."_ In layman's terms, you can think of it as a solid foundation for why people should trust using your software. It also gives the developers a sense of what "should" work when it comes to the software working the way it's meant to.

### Testing + Me

I've come from all sorts of different testing approaches during my short engineering life. I was first introduced to software testing in college. We were exposed to the test driven development style. I would recommend all software engineers to learn testing and the fundamentals of why testing is key to the success of the software we write. While working in industry I've done basic "by hand" testing -- with no automated testing suites. Don't be that person that says testing is enough when you just get out `curl` and hit your APIs! 

On the opposite end of the spectrum -- I've had full out exposure on setting up and managing a Jenkins build system on AWS for a team. On that we could run our test suites, run on multiple environments, all managed using Jenkins build agents. I've also used other CI/CD tools to do the same thing, basically they all do similar things. 

I've developed and tested in many different languages (NodeJS, Python, Java, PHP, Racket, and others). The ease of use in some native testing frameworks are great for some languages but none have even come close to Go's standard [testing](https://golang.org/pkg/testing/) package. I cannot emphasize more on how happy I am to develop and **TEST** with Go. Testing is made so easy with Go -- there should be no excuse why you can't have a solid amount of tests for your packages or applications.

![vader](./../assets/images/vader-unit-tests.jpg)

### Testing Things In Go

Some of the tips I can give when writing tests in Go are use `slices` of `structs` to model your test cases. This keeps everything tidy and very easy to just write one loop to handle all the cases for a function. _Note_ I've done this plenty before, but I see a lot of people trying to just do a crazy amount of `asserts` with new variables and `structs` made and honestly it's just so time consuming and ugly. 

Given this function we can show some example tests.

~~~
func stringer(in string) error {
    if in == "" {
        return errors.New("empty string")
    } else if in == "hi" {
        return errors.New("bye")
    }
    return nil
}
~~~
{: .language-go}

The better way to test cases -- you `name` your test case, `args` structures your inbound arguments, `want<Anything>` are the types you expect from those test cases. You can model your tests so easily with this approach! :)

~~~
type args struct {
    in string
}
tests := []struct {
    name    string
    args    args
    wantErr bool
    // more wants can be added here ...
}{
    {
        name: "test not empty string",
        args: args{
            in: "test string",
        },
        wantErr: false,
    },
    {
        name: "test hi string",
        args: args{
            in: "hi",
        },
        wantErr: true,
    },
    {
        name: "test string transformation",
        args: args{
            in: "test empty string",
        },
        wantErr: true,
    },
}
~~~
{: .language-go}

After you generate your test cases you can run through a `for` loop and do some simple checks. You can also add customizable test cases based on conditionals you expect to have in your functions.

~~~
for _, tt := range tests {
    t.Run(tt.name, func(t *testing.T) {
        if err := stringer(tt.args.in); (err != nil) != tt.wantErr {
            t.Errorf("stringer() error = %v, wantErr %v", err, tt.wantErr)
        }
    })
}
~~~
{: .language-go}

### Other Testing Advantages

While I am sure there are arguments that you can do this same thing in other languages just as easy. I take it as a _"cherry on top"_ -- Go is already easy to use and extremely powerful.

#### Third Party Packages

- Notable packages:
- [testify](https://github.com/stretchr/testify)
- [httpexpect](https://github.com/gavv/httpexpect)
- [goconvey](https://github.com/smartystreets/goconvey/) -- browser UI testing
- [gomock](https://github.com/golang/mock) -- mocking framework
- [httptest](https://golang.org/pkg/net/http/httptest/) -- Go's native `httptest` package
- Awesome-go's [testing](https://awesome-go.com/#testing) section

#### Editor Integrations

There are some extremely powerful and robust editor tools surrounding the Go language. These all give the power to the users -- doing auto linting, generation for test cases, auto building, etc.! I cannot be more thankful for all of the abstractions automatically for my development process. 

Here are a few I use or hear good things about.

- [vscode-go**](https://github.com/Microsoft/vscode-go)
- [vim-go**](https://github.com/fatih/vim-go)
- [GoSublime](https://github.com/DisposaBoy/GoSublime)
- Awesome-go's [editor-plugins](https://awesome-go.com/#editor-plugins) section

** ones I use regularly
