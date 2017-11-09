---
title: Git pushing Shiny Apps with Docker & Dokku
author: Flavio Barros
date: '2015-05-11'
categories:
  - Digital Ocean
  - Docker
  - English
  - R e RStudio
  - r-bloggers
  - Shiny
tags:
  - deploy
  - git
  - web
slug: git-pushing-shiny-apps-with-docker-dokku
---

At this post i will show you how to deploy Shiny Apps easily with a simple [git push](http://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes#Pushing-to-Your-Remotes). But, what's a git push? I'm referring to the git command used with [remote repositories](http://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes). With this command you can deploy apps easily with a [PaaS](http://en.wikipedia.org/wiki/Platform_as_a_service) (Platform as a Service) like [Heroku](https://www.heroku.com/). If you never heard about Heroku or know nothing about PaaS, i will show you what is it and how can we use a similar resource to easily deploy a Shiny App on [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) with [Docker](http://www.docker.com/).

## 1.Heroku

Anyone who have worked with web know, or at least heard about Heroku. Heroku is a PaaS, an acronym for Platform as a Service. The idea behind Heroku is that the developer does not need to worry about the problems related to the implementation of its software, it simply develops, adds some files in the project, gives a git push to Heroku and he takes care of the rest.

Heroku is an excellent service, can scale to large apps, is easy to learn and use, BUT it can be very [expensive](https://www.heroku.com/pricing)! Just to understand how easy is work with Heroku, i will deploy an example web app made with [Django](https://www.djangoproject.com/); the code can be found [here](http://tutorial.djangogirls.org/en/index.html) and you can visit the app here: <http://flavio-django-blog.herokuapp.com/>

## 2.Docker and Dokku

I wrote some time ago about Dockerizing a Shiny App: [Dockerizing a Shiny App](http://www.flaviobarros.net/2015/04/30/dockerizing-a-shiny-app/) (read before continue). In fact, i did that because i was researching about a method to easily deploy Shiny Apps just like any regular web app, (ex. Node or Django). Some months ago, i found [this project](https://github.com/btubbs/heroku-buildpack-shiny), where the author claims to be able to run Shiny Apps on Heroku. I tried, but never was able to make it work. Another problem, was that i was searching something that i could host on my own server.

At the end, after discovering Docker, i started to use [Dokku](https://github.com/progrium/dokku) instead of Heroku. Dokku in turn, is a kind of Heroku clone, made from Docker. It works the same way as Heroku, such that for web apps, like those made with Django, deployment is identical. Just so you see how it works, I installed Dokku on [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) and implemented the same previous application made in Django. You can check the app here: <http://djangogirls.flaviobarros.net/>

In my opinian Dokku is one of the best apps made with Docker. With it we can deploy multiple technologies at the very same server. In fact, you can build a cheaper version of Heroku, for about U$5,00 as is the case of [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6). Right now, i'm running a [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) Server, hosting this Wordpress installation, two Django Web Apps and a Shiny App and there is plenty of space for much more, all for a 10 bucks VPS on Digital Ocean ;-)

## 3.Git pushing the Wordcloud Shiny App

Some time ago, Dokku featured [Dockerfile](https://docs.docker.com/reference/builder/) build support. With this feature becomes possible to git push any app that could be builded from a Dockerfile.  A Dockerfile, is nothing more then a recipe to build Docker Images, something like the [Word Cloud](https://registry.hub.docker.com/u/flaviobarros/shiny-wordcloud/) image that i released at [Docker Hub](https://hub.docker.com/).

With this in mind, i thought: why don't change the official [Shiny Server docker image](https://registry.hub.docker.com/u/rocker/shiny/) to host a single Shiny App? I just needed an image that:

1) Exposes 80 PORT;

2) Serves just an app;

3) Could be builded from a Dockerfile;

In fact, to build [shiny-wordcloud](https://github.com/flaviobarros/shiny-wordcloud), i forked [rocker/shiny](https://github.com/rocker-org/shiny) and implemented this features by means of some modifications on the Dockerfile (commits: [1](https://github.com/flaviobarros/shiny-wordcloud/commit/451346b4c806cef84b214661e6caf664a57abc44), [2](https://github.com/flaviobarros/shiny-wordcloud/commit/ef16a049b8b32943f3ed568ada61e1def148b505) and [3](https://github.com/flaviobarros/shiny-wordcloud/commit/ab394841c30fd9bfe90fbeb0a89f012f216679c4)) and a [conf file](https://github.com/flaviobarros/shiny-wordcloud/blob/master/shiny-server.conf). Now i have fully working Dockerfile that i can use to git push Shiny Apps to Dokku! From now on, i will show you how you can install Dokku at [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) and how i deployed the Wordcloud Shiny App at my server with a simple git push!

### 3.1 Git pushing to my server

Just follow the video. You can visit the app here: <http://wordcloud.flaviobarros.net/>

### 3.2 Installing Dokku on Digital Ocean

Follow this screencast. In the end you will have your server available at an IP. If you want a domain, will have to register it (ex. Godaddy) and point it to [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) DNS. You can follow [this tutorial](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean) to setup a DNS on [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6). If you have any problem let me know.

https://www.youtube.com/watch?v=pOaZ7QwuxKU&feature=youtu.be

### 3.3 Important details

I have used a SSH key that i have stored at [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6). Usually, when you spin up your [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) droplets, you get an email as soon as the process completes, letting you know the droplet's IP address and password. Although this email is convenient, there is a more secure (and faster) way of gaining access to your server _without the need for email_. This can be done by setting up SSH keys. Follow this [tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets) to get this done.

## Conclusion

Recently i saw two interesting blog posts about Shiny App deployment:

1) [Run Shiny app on a Ubuntu server on the Amazon Cloud](http://www.r-bloggers.com/run-shiny-app-on-a-ubuntu-server-on-the-amazon-cloud/)

2) [How to get your very own RStudio Server and Shiny Server with DigitalOcean](http://www.r-bloggers.com/how-to-get-your-very-own-rstudio-server-and-shiny-server-with-digitalocean/)

In both scenarios this approach has several advantages:

- You can replicate this dokku installation on Amazon and have the same functionality.

- When you are running multiple Shiny Apps on the same Shiny Server, you are using a [single R instance](http://www.r-bloggers.com/shiny-server-open-source-edition-solution-for-cpu-bound-apps/). SO, if you have more then one app deployed your server can slow down. With this solution, each app is isolated with your own Shiny Server instance, which is more reliable.

- The deployment process is easier. Once dokku is installed you don't need to connect to the server to deploy an app. Just use git push!

- You can deploy multiple Shiny Apps, and multiple Web Apps. You can have Shiny, Wordpress, Django and etc, on the same server.

- With [dokku-alt](https://github.com/dokku-alt/dokku-alt) (an improved fork of Dokku) you can [setup passwords](https://github.com/dokku-alt/dokku-alt#http-basic-auth-support) to access your Shiny Apps, something that is only available on Shiny Server Pro.

IMPORTANT: through any link to [Digital Ocean](https://www.digitalocean.com/?refcode=c3521662d5e6) in this post,  you will earn U$10.00 credit without commitment to keep up the service. With this credit you can keep a simple VPS with 512MB RAM, for two months for free!
