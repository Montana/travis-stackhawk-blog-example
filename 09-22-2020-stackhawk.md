---
title: StackHawk + Travis CI 
layout: post
created_at: Tue Sep 22 10:00:00 EDT 2020
Permalink: 09-22-2020-stackhawk.md
current: blog
layout: post
author: Montana Mendy
excerpt_separator: <!--more-->
categories:
- news
tags:
- news
---

For us here at Travis, security is a big deal. Most importantly you want to catch bugs before they hit production, but also in that same process - classify and indentify security vulnerabilities. 

<!--more-->

I saw StackHawk in action personally, and one of the first things I pondered was, this is a great alternative to say ```docker inspect --format='{{.Config.Image}}' $INSTANCE_ID```. I also thought what about doing in-house Docker commands for analysis in advance (at the image level). Both are great ideas but as I personally know sometimes things can get a little messy. Here at Travis CI we like quick, simple and efficient. Let's talk about how we would set StackHawk up with Travis CI and see how simple it is. 

## Getting started

First, let's secure some API keys. So, let's sign up for StackHawk once you signed up for StackHawk you created an API key. To keep it a secret, copy it to the ```env var``` for your project. In the Travis CI user interface, find the repository in question, select More options –> Settings. From here, find the Environment Variables section. Add your StackHawk API key as a variable called ```HAWK_API_KEY```.

## Let's configure our job 

In your ```.travis.yml``` file - to enable HawkScan, make sure the ```script``` section looks something similar to this: 

```yaml
language: shell
services:
  - docker
script:
  - >
    docker run --volume $(pwd):/hawk:rw --tty
    --env API_KEY="${HAWK_API_KEY}"
    --env NO_COLOR=true
    stackhawk/hawkscan
```
This configuration tells Travis CI to run a single script, which runs HawkScan as a Docker container. The StackHawk API key injected from the secure environment variable, ```HAWK_API_KEY```. The ```NO_COLOR``` environment variable suppresses colorized text so that HawkScan’s output is more readable in the Travis CI console.

## Finally let's configure HawkScan

You want to create a file called ```stackhawk.yml``` this will be needed to scan your application. For this example, we will create a minimal config pointing to our ```Development``` environment API endpoint. So here's what our current ```stackhawk.yml``` file looks like: 

```yaml
app:
  applicationId: xxXXXXXX-xXXX-xxXX-XXxX-xXXxxXXXXxXX
  host: http://example.com
  env: Development
```
You'll want to replace the host entry with your test endpoint, and replace ```applicationId``` with your App ID from StackHawk.

## Let's run it

Check those two files into your source control, and head over to the Travis CI console to watch your job run. When your job is complete, check your account at StackHawk to review your scan details!

## More options and flexibility on monitoring builds 

StackHawk has tons of features, which we recommend you check out. This example above is just one of many ways you can implement StackHawk in your project. To see other ways you can employ StackHawk in your project that's using Travis CI, checkout this really helpful guide from StackHawk [here](https://docs.stackhawk.com/continuous-integration/travisci.html).

We hope you find StackHawk as useful as we do, it's a simple yet effective way to catch vulnerabilities before you deploy to production. 

Not a Travis CI user yet? [Sign up for a free account](https://travis-ci.com/signup)!

Looking for something more bespoke for your builds? [Try a free trial of Travis CI Enterprise](https://landing.travis-ci.com/enterprise/).







