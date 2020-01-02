---
title: DNS Performance Test
date: 2018-06-14 14:05:08
tags: Network
---
 <!-- more --> 


##### Using dnsperftest

[Dnsperftest](https://github.com/cleanbrowsing/dnsperftest) is a Shell script to test the performance of the most popular DNS resolvers from your location. 

First install dc and dig.

```bash
$ sudo apt-get install bc dnsutils
```

Usage

```bash
$ git clone --depth=1 https://github.com/cleanbrowsing/dnsperftest/
$ cd dnsperftest
$ bash ./dnstest.sh 
```

