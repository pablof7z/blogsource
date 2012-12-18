---
layout: post
title: "XBMC issues with smb"
date: 2010-05-11 23:19
comments: true
categories:
  - xbmc
  - media
---
I’ve been having issues with XBMC playing files served through samba, I don’t recall when this started, but for sometime I couldn’t even browse the collection of files in my own server. Nothing changed, well, I did upgrade Ubuntu to 10.04 on my server, but I think this was happening before that.

Anyway, I browsed the internet and didn’t find anything related to this error or how to fix it.

After looking around in the configuration I’ve found something which caught my eye and I got it working.

The error I was getting was:

```
23:09:42 T:3077850992 M:1831804928   ERROR: SMBDirectory->GetDirectory: Unable to open directory : 'smb://'
unix_err:'2' error : 'No such file or directory'
23:09:42 T:3077850992 M:1831804928   ERROR: GetDirectory - Error getting smb://
```

And what I had to do is remove the XML path in `$HOME/.xbmc/userdata/guisettings.xml` that started with the tag `<smb>`. Removed that, restarted XBMC and things started to work again.