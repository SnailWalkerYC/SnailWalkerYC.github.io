---
layout: post
title: Kernel Update
date: 2020-01-20
categories: [technology]
tags: [tech]
comments: false
---



**See Current Kernel Version**

```
lsb_release -a
```

or 

```
uname -mrs
```

or

```
uname -a
```



**Research Kernel Version**

```
apt-cache search linux-generic
```



**Install New Kernel With Command**

```
sudo apt install linux-image-4.10.0-27-generic 
```

```
sudo apt-get install linux-image-4.10.0-27-generic
```

After the install, reboot the system.



**Install New Kernel With Download Deb**

```
Download from https://kernel.ubuntu.com/~kernel-ppa/mainline/

And install with it: sudo dpkg -i *.deb 
```



**Remove Kernel**

``` 
sudo dpkg --purge linux-image-5.3.0-050300-generic linux-image-unsigned-5.3.0-050300-generic
```



Ref:

1. Kernel Download: https://kernel.ubuntu.com/~kernel-ppa/mainline/ 
2. See Current Kernel Version: https://www.cyberciti.biz/faq/installing-latest-stable-mainline-linux-kernel-on-ubuntu-with-apt-get/ 
3.  Install kernel: http://www.theubuntumaniac.com/2019/03/linux-kernel-501-stable-is-release.html 
4. Install Kernel 2: http://ubuntuhandbook.org/index.php/2019/09/install-linux-kernel-5-3-ubuntu-linux-mint/