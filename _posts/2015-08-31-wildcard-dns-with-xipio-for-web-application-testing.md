---
layout: post
title: Wildcard DNS with xip.io for web application testing
date: 2015-08-31 15:55:04
categories: [ops]
tags: [ops, DNS, testing, tablet, phone, apache, nginx]
---

If you need to test a web application on smart phones and tablets,
xip.io's wildcard DNS can help. Using **[xip.io](http://xip.io/)** you can access an application
running on a local IP address, say: `192.168.1.10` with `http://192.168.1.10.xip.io` (or any
subdomain variant) from any other device which is on the same network.

So if you are running a web server such as [nginx](http://wiki.nginx.org/Main) or
an old [Apache](http://httpd.apache.org/), you can set up subdomain prefixes to access
different web applications.

If you use nginx and are serving a sub directory: `foo` from your www directory, you can access
`foo` with `http://foo.192.168.1.10.xip.io` by updating your `nginx.conf` with this gist:

{% gist oldgit/7de7492a3942fe9f631c nginx.xip.io.conf %}

If you are using Apache, you can make an entry to your `httpd-vhosts.conf` file:
{% highlight apache %}
ServerAlias foo.*.xip.io
{% endhighlight %}

**Note**: This is just a *quick* way of connecting devices to a local IP web server.
It avoids edits to `/etc/hosts` or using `dnsmasq`.
If you want a more complete discussion of **xip.io**, see: [Hacker News](https://news.ycombinator.com/item?id=7732457)
