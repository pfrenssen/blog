---
layout: post
title: Install VLMC video editor in Debian and Ubuntu
created: 1261671957
categories:
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob bash
- !ruby/string:Sequel::SQL::Blob bash
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob bash
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob bash
- !ruby/string:Sequel::SQL::Blob linux
---
# Update

<blockquote>This article is outdated. VLMC can now easily be installed on Ubuntu using this PPA: <a href="https://launchpad.net/~webupd8team/+archive/vlmc">VLMC Daily Build PPA for Ubuntu</a>.</blockquote>

I was very excited to learn that the developers of the VLC player are working on a cross platform non-linear video editor. I spent some time getting it to compile and  made a script that will install it automatically.

I've tested the script on clean installs of Ubuntu Karmic, Ubuntu Lucid and Debian Squeeze to verify it pulls in all required dependencies.

VLMC does not work in Debian Lenny, for it needs Qt 4.5. If you use Debian you need to <a href="http://www.pendrivelinux.com/how-to-add-a-user-to-the-sudoers-list/">enable sudo for your user account</a>.

Mind that these are large software packages and compiling them can take a long time: from 15 minutes up to more than an hour depending on the speed of your machine.

```shell
#!/bin/bash
#remove VLC and clean up
sudo apt-get remove vlc
sudo apt-get autoremove

#install required packages
sudo apt-get build-dep vlc
sudo apt-get install libtool build-essential automake1.10 git-core libqt4-dev qt4-qmake liblua5.1-dev libswscale-dev cmake

#download the source repositories of VLC and VLMC
sudo chmod 777 /usr/local/src
cd /usr/local/src/
git clone git://git.videolan.org/vlc.git
git clone git://git.videolan.org/vlmc.git

#build VLC
cd vlc/
./bootstrap
mkdir build;cd build/
../configure --disable-xcb --disable-nls --disable-mozilla --disable-live555
make
sudo make install
make clean

#update library bindings
sudo ldconfig

#build VLMC
cd /usr/local/src/vlmc/
echo "LIBS = -L/usr/local/lib -lvlc" >> vlmc.pro
cmake .
make

#run VLMC
./vlmc
```

## References:

* http://wiki.videolan.org/UnixCompile
* http://wiki.videolan.org/Building_VLMC
