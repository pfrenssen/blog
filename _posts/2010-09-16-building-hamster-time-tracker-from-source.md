---
layout: post
title: Building Hamster time tracker from source
created: 1284640390
categories:
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob bash
- !ruby/string:Sequel::SQL::Blob bash
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob bash
- !ruby/string:Sequel::SQL::Blob linux
---
<a href="http://projecthamster.wordpress.com/about/">Hamster</a> is a very handy time tracking tool for Gnome. It is an applet which lives in the Gnome panel which is great as it is always readily available. You can switch tasks and projects effortlessly, add tags to tasks, see pretty graphs and print out time sheets for your boss or clients.

Hamster is improving steadily, and I like to keep up with the development. I wrote a little script that pulls in the latest changes from the repository, and builds it using the correct tools. After building it opens the applet in windowed mode so it can be tested.

First you will need to install the required packages:

```shell
$ sudo apt-get install git-core gettext intltool gnome-control-center-dev gnome-common libglib2.0-dev libgconf2-dev python-dev python-gtk2-dev python-gnome2-desktop-dev python-gnome2-dev
```

Copy the script below and change the VERSION to the one you wish to use:
<ul><li>"master": bleeding edge</li>
<li>"origin/gnome-2-32": development branch</li>
<li>"origin/gnome-2-30": stable branch</li></ul>

```shell
#!/bin/bash
#build_hamster.sh

#set the version you want to use, eg "master", ""origin/gnome-2-32" or "origin/gnome-2-30"
VERSION="origin/gnome-2-32"

#kill hamster
kill $(ps aux | grep "python /usr/lib/hamster-applet/hamster-applet" | awk '{ print $2 }')

#backup database
cp -i ~/.local/share/hamster-applet/hamster.db ~/.local/share/hamster-applet/hamster.db.bak

#download the repository
if [ ! -d "hamster-applet" ] ; then
  git clone git://git.gnome.org/hamster-applet
fi

cd hamster-applet

git checkout master
git pull
git checkout $VERSION

if [ $VERSION == "origin/gnome-2-30" ] ; then
  ./autogen.sh --prefix=/usr/ --with-gconf-schema-file-dir=/usr/share/gconf/schemas/
  make
  sudo make install
else
  ./waf configure build --prefix=/usr
  sudo ./waf install
fi

#launch hamster in windowed mode for testing
/usr/lib/hamster-applet/hamster-applet -w
```

Save the script as "build_hamster.sh" in /usr/local/src (or somewhere in your home directory). Change the permissions to executable and run it:

```shell
$ chmod u+x build_hamster.sh
$ ./build_hamster.sh
```

The script will download and build Hamster now, this doesn't take more than a minute. It will ask for your root password to install Hamster after compiling. When it is done a windowed version of Hamster appears so you can do some testing. It is very small so you might have to hunt around on your screen to find it.

You can add it to the Gnome panel permanently by right-clicking in the panel, choosing "Add to panel" and clicking on "Time Tracker". If this does not appear in the list you need to restart the panel:

```shell
$ sudo killall gnome-panel
```


References:

* http://projecthamster.wordpress.com/building-and-running/
* http://git.gnome.org/browse/hamster-applet
