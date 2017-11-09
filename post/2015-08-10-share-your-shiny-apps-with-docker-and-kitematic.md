---
title: Share your Shiny Apps with Docker and Kitematic!
author: Flavio Barros
date: '2015-08-10'
categories:
  - Docker
  - Linux
  - R e RStudio
  - r-bloggers
  - Shiny
tags:
  - web
slug: share-your-shiny-apps-with-docker-and-kitematic
---

Some time ago i wrote on this blog about how to dockerize a Shiny App. If you don't know what Docker is and what i mean by "dockerizing a Shiny App" i advise you to read this post before:

[Dockerizing a Shiny App](http://www.flaviobarros.net/2015/04/30/dockerizing-a-shiny-app/)

I used this solution to create an easy way to deploy Shiny Apps on web servers, once that, if you want to share your apps, you have to choose between three possible solutions:

1) Share your ui.R and server.R files with someone who is able to run your app on RStudio.

2) Share your app online with [shinyapps.io](http://www.shinyapps.io/).

3) Share your app with your own server (Ex: at AWS).

4) [Git push you app on your own server](http://www.flaviobarros.net/2015/05/11/git-pushing-shiny-apps-with-docker-dokku/).

The solution 1) isn't viable to non technical users. The solution 2) is easier, but can be expensive. The solution 3) can involve a lot of technicalities but can be cheaper then solution 3). The solution 4) is compromise between solutions 2) and 3), and is my preference.

HOWEVER, recently i knew about a project from Docker Inc. with a good potential: the Kitematic! With Kitematic the user can have a GUI where he can download and use softwares on Docker images. So, the [Docker Hub](https://hub.docker.com/) becomes something like an App Store where he can simply download and use apps installed inside docker containers. See this video to understand what Kitematic can do:

https://www.youtube.com/watch?v=QEr340gjV1Q

This is Huge because, once you dockerize your Shiny App, doesn't matter how complex it is, you can share it on Docker Hub just like any app. Kitematic can be used at Windows, Mac and [experimentaly on Linux](https://github.com/kitematic/kitematic/issues/49). I recorded this video (in Linux) to show you how to install my dockerized shiny app with some clicks:

https://youtu.be/7MH_bCjoFT4

Remember, before you share your app you will need to dockerize it and upload to Docker Hub.
