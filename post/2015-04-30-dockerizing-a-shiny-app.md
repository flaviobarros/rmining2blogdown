---
title: Dockerizing a Shiny App
author: Flavio Barros
date: '2015-04-30'
categories:
  - Digital Ocean
  - Docker
  - English
  - R e RStudio
  - r-bloggers
  - Shiny
tags:
  - deployment
  - shiny
  - web
slug: dockerizing-a-shiny-app
---

After a long pause of more than four months, I am finally back to post here. Unfortunately, many commitments prevented me keep posting, but coming back, i changed the deployment (now this blog runs entirely within a docker container with some other cool things I intend to post more forward) and wrote this post.

## 1. R e apps Shiny

If you are reading this post here, you probably know what Shiny is. OK, but in the case you don't, you can see it in action! This is the App that i dockerized. Soon you will able to run it at any computer with docker installed.

## 2. Docker

If you somehow accompanies the open source world news then you probably have heard of [Docker](http://www.docker.com/). Docker is a fantastic tool for creating software containers, totally isolated from the host operating system. It works like a virtual machine, but it's much lighter.

The idea behind docker is that the developer creates a container with all dependencies that he wants, make sure that everything works and done. The staff responsible for software deployment does not need to know what is inside the container, it just needs to be able to run container on the server. While this feature could be achieved with virtual machines, they ended up coming with much more than necessary, so the VM files are too large and the host system becomes very slow.

On the other hand, docker does not use a full OS, it shares the same host kernel (yes, it needs to run on Linux) but is a completely isolated environment. So run a docker containers is much lighter than run a virtual machine. Docker features do not stop there, it also allows a kind of versioning and has a kind of github for containers, the [Docker Hub](https://hub.docker.com/account/signup/), where the user can download and use ready images  for various software, such as MySQL, Postgres , LAMP, WordPress, RStudio, among others. If you want to better understand what is Docker, watch this video.

## 3. Dockerizing a Shiny app

I just showed you an example of a Shiny app running on RStudio locally. For development it's ok, but if I want to make it available to anyone? One solution is send the project files. For a basic shiny application just two files are needed (ui.R and server.R).

But what if I want to put on the web? There are two alternatives:

1) A [Shiny Server](http://www.rstudio.com/products/shiny/shiny-server/)

2) The PaaS [shinyapps.io](http://www.shinyapps.io/)

Option 1) can be very complicated for some users, sometimes not workable, due to the need to install and configure a server.

Option 2) is more interesting, however you it can be expensive, since the free plan can be very limited for some needs.

How docker can help? Initially with the docker you can create a shiny server  using one command. This greatly simplifies deployment of a server. See this short video:

You just need:

    docker run --rm -p 3838:3838 rocker/shiny

** WINDOWS AND MAC USERS:** You will need [boot2docker](http://boot2docker.io/) to reproduce this.

This solution seems to solve the problem. However you can still find several problems such as:

1) How can i put my apps on the server?

2) How can i get a url directly to my app?

3) And this 3838, how can i change it?

4) How can i create an image for my app?

To solve these problems I created a sample container, with a sample app, which appears in the browser as the image is running. Its available at Docker Hub, and it's ready to test and use. The source code is on [Github](https://github.com/flaviobarros/shiny-wordcloud).

At the following videos i show you how to deploy this app locally and on [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6). First local:

And on Digital Ocean:

Note that when using the aforementioned command, you do not returns straight to the terminal and you will need a Ctrl + C to close the container. So, to keep the container running and return to the terminal, you should use &.

    docker run --rm -p 80:80 flaviobarros/shiny &

You can run this app on [Amazon Web Services](http://aws.amazon.com/pt/), [Google Cloud](https://cloud.google.com/) and [Microsoft Azure](http://azure.microsoft.com/pt-br/), as all of them have support for docker. However, my suggestion is [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) that is a lot easier to use.

IMPORTANT: through any link to [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) in this post,  you will earn U$10.00 credit without commitment to keep up the service. With this credit you can keep a simple VPS with 512MB RAM, for two months for free!
