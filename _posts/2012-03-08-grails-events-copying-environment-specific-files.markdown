---
layout: post
title:  "Grails Events: Copying Environment Specific Files"
date:   2013-03-08 20:46:49
categories: grails
---
We're using Terracotta on a new project.  We have Terracotta installed on our staging and production servers.  For it to work there needs to be a context.xml in the META-INF folder of the WAR with something defined like so:

{% gist 3008516 %}

This is fine for staging because the Terracotta server is on the same server as Tomcat.  But for production, we have two (2) Terracotta servers on their own machines.  In addition to that, we don't want this file during local development or on our testing server since Terracotta isn't running on either.  

I created a folder in our app structure called "assets" and I placed 2 files there: context-staging.xml and context-prod.xml.  I then created a file in the Grails scripts folder called _Events.groovy.  Below is the code for that file which should be self explanitory:

{% gist 3008527 %}

And that's it.  Now, every time the app is ran or packaged we get the appropriate context.xml file.
