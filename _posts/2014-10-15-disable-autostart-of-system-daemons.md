---
layout: post
title:  "Disable Autostart of System Daemons"
description: How to prevent a system daemon from automatic start at boot-up.
tags: [ guide, linux, shell, upstart ]
---
If we install a system daemon, like an ssh server on Ubuntu, this daemon is always started at system boot-up.
However, on a laptop or desktop PC we do not need an ssh server that is always running.
Here is the command to disable the autostart of such daemons.

{% highlight bash %}
$ echo manual | sudo cat /etc/init/SERVICE.override
{% endhighlight %}

## Further Reading
 * [How to enable or disable services](http://askubuntu.com/a/19324)
 * [Upstart Intro, Cookbook and Best Practises](http://upstart.ubuntu.com/cookbook/#manual)