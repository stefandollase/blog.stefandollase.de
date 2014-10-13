---
layout: post
title:  "Shortcut to publish Screenshots"
description: >
  This is an addition to the post "Shortcut to publish Files from Local Folder".
  I added another shortcut which helps me to quickly publish screenshots.
tags: [ guide, linux, gnome-screenshot, shell, xclip ]
---
## Taking a Screenshot

First, we take a screenshot. The tools `gnome-screenshot` can do this for us:

{% highlight bash %}
$ gnome-screenshot -a -f "$localfolder/screenshot.png";
{% endhighlight %}

This command:

 * takes a screenshot of a user defined area `-a`
 * saves it in the specified file `-f`

## Publishing the Screenshot

In this post, we use the publishing mechanism described in [this post](/2014/10/07/shortcut-to-publish-files-from-local-folder.html), so we can publish files by executing the script `push-to-server`.

## Writing the Link to the Clipboard

We write the link of the screenshot to the clipboard, so the only thing left to do is to paste the link to a mail, chat window, etc.

{% highlight bash %}
$ echo -n "http://example.com/hdd/screenshot.png" | xclip -selection c
{% endhighlight %}

## Creating the Keyboard Shortcut

Finally, we create the keyboard shortcut. In Ubuntu we can do this via

 * `System Settings > Keyboard > Shortcuts > +`

Here, you can put in any title and the following command:

{% highlight bash %}
$ /bin/bash -c 'gnome-screenshot -a -f "/home/stefan/server-hdd/screenshot.png";gnome-terminal -e "push-to-server";echo -n "http://example.com/hdd/screenshot.png"|xclip -selection c'
{% endhighlight %}

This command:

 * takes a screenshot and saves it in the folder `/home/stefan/server-hdd/`
 * publishes this folder to the webserver
 * displays the progress of the upload
 * writes the link of the screenshot to the clipboard