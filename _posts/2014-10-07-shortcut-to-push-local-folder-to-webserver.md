---
layout: post
title:  "Shortcut to push Local Folder to Webserver"
description: >
  Often enough I am annoyed when I want to send someone a file,
  so I decided to write a script which helps me to synchronize
  a folder on my local laptop to a folder, which is publicly available on the internet.
tags: [ "guide", "rsync", "nginx", "shell", "ssh" ]
hidden: true
---
## Setting up ssh

To be able to push files to the server, we need to somehow get access to it.
In this guide, I will use a ssh connection, so we need to install and configure an ssh server on the webserver.
There are many guides for this out there, for example
[this Ubuntu guide](https://help.ubuntu.com/community/SSH/OpenSSH/Configuring) and
[this Mac OS X and Windows guide](http://lifehacker.com/205090/geek-to-live--set-up-a-personal-home-ssh-server).

## Setting up nginx

We use nginx to serve our files via the http protocol.

{% highlight bash %}
$ sudo apt-get install nginx
{% endhighlight %}

After installing, we need to configure nginx. We create the
config file `/etc/nginx/sites-available/example.com` with the following content:

{% highlight bash %}
server {
    listen 80;
    server_name example.com;
    root /usr/share/nginx/www/example.com;
}
{% endhighlight %}

Next, we need to enable this site:

{% highlight bash %}
$ cd /etc/nginx/sites-enabled/
$ sudo ln -s ../sites-available/example.com .
{% endhighlight %}

Next, we need to create the folder which holds our served files and link to it:

{% highlight bash %}
$ cd ~
$ mkdir hdd
$ cd /usr/share/nginx/www/
$ sudo ln -s /home/remoteuser/hdd/ example.com
{% endhighlight %}

Finally, we start the webserver:

{% highlight bash %}
$ sudo service nginx restart
{% endhighlight %}

## Setting up rsync

Now that we have access to a running webserver, we can use rsync to push the files to the public:

{% highlight bash %}
$ rsync -aiP --del -e ssh "$localfolder" remoteuser@server:hdd/
sending incremental file list
.d..t...... ./
*deleting   top-secret-document.pdf
<f+++++++++ file1
     10,878,976 100%    2.77MB/s    0:00:03 (xfr#1, to-chk=3/5)
<f+++++++++ screenshot.png
     11,206,656 100%    2.00MB/s    0:00:05 (xfr#2, to-chk=2/5)
{% endhighlight %}

This command:

 * recursively copies the content of the local folder to the server (`-a`)
 * displays a change-summary (`-iP`)
 * deletes files from the server which have been deleted from the local folder (`--del`)

## Getting a Link List

We uploaded the files. Next we need to send the link to the uploaded files to our friend.
For that reason, I made an addition to the script, which outputs a list of links, one for each file on the server.
Hence, the only thing left to do for us is to copy the link and send it to our friend. Here you see the complete script:

{% highlight bash %}
#!/bin/bash
localfolder="/home/stefan/my-local-folder/"
cd "$localfolder"
find . -type f -exec bash -c 'echo "http://example.com/hdd/${0:2}"' {} \;
echo
rsync -aiP --del -e ssh "$localfolder" remoteuser@server:hdd/
{% endhighlight %}

After saving it in a file called `~/bin/push-to-server` we need to make this file executable:

{% highlight bash %}
$ chmod u+x ~/bin/push-to-server
{% endhighlight %}

Make sure to put the file in the `~/bin/` folder, so it is in your `$PATH` variable.

When we execute the file, we get some output similar to this:

{% highlight bash %}
$ push-to-server
http://example.com/hdd/file1
http://example.com/hdd/screenshot.png

sending incremental file list
.d..t...... ./
*deleting   top-secret-document.pdf
<f+++++++++ file1
     10,878,976 100%    2.77MB/s    0:00:03 (xfr#1, to-chk=3/5)
<f+++++++++ screenshot.png
     11,206,656 100%    2.00MB/s    0:00:05 (xfr#2, to-chk=2/5)
{% endhighlight %}

## Creating a Keyboard Shortcut for the Script

Finally, we need to create the keyboard shortcut, to make the process of publishing as convenient as possible.
In Ubuntu you can do this via `System Settings > Keyboard > Shortcuts > +`. Here, you can put in any title and the following command:

{% highlight bash %}
$ gnome-terminal -e "/bin/bash -c 'plh;read'"
{% endhighlight %}

It ensures that we see the output of the script, so we are able to copy the needed links.

Happy publishing!