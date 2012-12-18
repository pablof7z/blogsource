---
layout: post
title: "A brave new Media Center revisited"
date: 2010-03-06 23:49
comments: true
categories:
  - d510
  - linux
  - media
  - xbmc
---
I recently wrote a post about my new media center, running off my laptop, well, while that setup was sweet and all, there was some sort of hole in it, that is, my laptop went belly up…

That was quite unfortunate, but it gave me the possibility to play with some new stuff, namely ITX setups.

I have finished yesterday building my ITX box, an Intel D510mo with an Atom D510 processor (dual with hyperthreading so 4 threads), 2 GB of RAM at 800 Mhz, WiFi PCI card on a Vitsuba Blackbox case, with a generic DVD-RW.

The setup is very sweet, I had lots of troubles building the system, at one point I thought my motherboard was faulty and I had to take it back to the store, but it seems the motherboard was ok, it was simply a matter of the mother producing a short while sitting in the case because of a mislocated screw in the case.

After the case was done I proceeded to install Ubuntu Karmic 9.10, to my surprise I had lots of troubles installing it, I had to perform the installation booting the netinstall through PXE (since I was out of CDs and all stores were closed). Once I was able to install Ubuntu I found that Ubuntu didn’t support the video card in the motherboard, and during the boot sequence the screen would go blank and there was pretty much nothing I could do about it.

Googling around I found other users with the same problem and determined to go with the same solution others had used… saying goodbye to Ubuntu and the beautiful Debian and APT world and totally selling out to the Fedora and Red Hat town (notice my strong bias :p).

I went through the horrible Fedora installation experience which is like getting hit on the ballsack while driving on a bike with no seat, configured GDM to autostart (which amazingly is not supported through gdm-config) and then I proceeded to build XBMC (since there is no XBMC build for non debian distributions (well done, and no, I’m not being ironic)).

It turns out building something like XBMC with support for many different codecs is not as straightforward as imaginable, who would have thought?! (now I am being ironic).

Here are some tips for others:

* Add extra repositories (Fedora sucks)
``` bash
su -c 'rpm -Uvh http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-stable.noarch.rpm http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-stable.noarch.rpm'
su -c "rpm -ivh http://rpm.livna.org/livna-release.rpm"
```

* Install extra packages (Fedora blows)
``` bash
yum -y install ccache subversion git gcc-c++ boost-devel mesa-libGL-devel glew-devel libmad-devel libjpeg-devel libsamplerate-devel libogg-devel libvorbis-devel freetype-devel fontconfig-devel bzip2-devel fribidi-devel sqlite-devel mysql-devel libpng-devel pcre-devel lzo-devel libcdio-devel libsmbclient-devel SDL-devel SDL_image-devel SDL_mixer-devel enca-devel jasper-devel libmms-devel libXt-devel libXtst-devel libXmu-devel libXinerama-devel libcurl-devel dbus-devel hal-devel pulseaudio-libs-devel pulseaudio-libs-zeroconf faac-devel avahi-devel ffmpeg-devel a52dec-devel libdca-devel faad2-devel mpeg2dec-devel libass-devel libmpcdec-devel flac-devel wavpack-devel python-devel cmake gperf unzip make libtool flex bison libtiff-devel avbin-devel libmicrohttpd-devel libdvdcss-devel faad2-devel
```

* Fix paths (Fedora reeks)
``` bash
ln -s /usr/lib/mysql/libmysqlclient.so.16.*.* /usr/lib/libmysqlclient.so
```

* Get XBMC
``` bash
svn co https://xbmc.svn.sourceforge.net/svnroot/xbmc/trunk/
```

* Build XBMC
``` bash
./bootstrap
./configure --disable-debug --enable-external-libfaad --enable-external-libdts --enable-mid --enable-profiling --enable-external-libmad --enable-external-liba52 --enable-external-libmpeg2 --enable-external-libass --enable-external-libvorbis --enable-external-libogg --enable-external-libmpcdec --enable-external-libflac --enable-external-libwavpack --enable-external-python
make -j 4
make install
```

Note that the `make -j 4` command might succeed but not finish, I have found a few times that when using make in parallel even with multiple cores it will skip files, you want to use `make -j 4` a few times to accelerate the process but do make sure its actually finished running make.