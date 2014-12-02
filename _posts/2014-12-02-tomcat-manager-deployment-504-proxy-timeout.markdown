---
layout: post
title:  "Tomcat Manager Deployment: 504 Proxy Timeout"
date:   2014-12-02 14:00:00
---
I recently needed to setup deployments to Tomcat 7 via Jenkins for a client. This particular Tomcat 7 configuration uses mod_jk to proxy requests from Apache HTTP to Tomcat and back. I generally use mod_proxy for this so I had a bit of a learning curve when it came to solving this problem.

The problem was that every time I would deploy a WAR through Tomcat's Manager, Tomcat would seem to timeout with the following exception:

{% highlight java %}
Caused by: java.io.IOException: Server returned HTTP response code: 504 for URL: http://xx.xxx.xx.x/manager/text/deploy?path=%2F
{% endhighlight %}

At first, I thought Tomcat was timing out because the deployment was taking too long. This proved to be futile as any changes to Tomcat's connection timeouts didn't solve the problem. Then I looked deeper into the actual HTTP Status 504.

<blockquote>
The server, while acting as a gateway or proxy, did not receive a timely response from the upstream server specified by the URI (e.g. HTTP, FTP, LDAP) or some other auxiliary server (e.g. DNS) it needed to access in attempting to complete the request.
</blockquote>

Aha! So it wasn't Tomcat so much as some proxy that was being used. This is when I learned that the administrator who setup the stack used mod_jk. Googling around for answers I found out that you specify settings for mod_jk in the `workers.properties` file, but first I had to find that file.

I learned that the `jk.conf` file specified the path to the `workers.properties` file. On my server, the `jk.conf` file is located at `/etc/apache2/mods-enabled/jk.conf`. This file told me that the `workers.properties` file was located at `/etc/libapache2-mod-jk/workers.properties`.

In this file is a line with the following:

{% highlight python %}
worker.escotest01.reply_timeout=20000
{% endhighlight %}

Increasing that timeout and then restarting both Apache and Tomcat solved my problem.




