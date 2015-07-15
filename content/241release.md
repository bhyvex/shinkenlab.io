Title: 2.4.1 release
Date: 2015-07-15 10:20
Category: Talk
Tags: shinken
Slug: release-2-4-1
Author: Gabès Jean
AuthorLogin: naparuba
Summary: Here is the 2.4.1 version, Debian Jessie edition and a security fix ^^




Hi :)


Here is a maintenance version, the 2.4.1, mainly for managing debian Jessie 8, but also a security fix.


This debian version did change the curl lib version that we are using for inter-process communication, and seems that there is a bug in it that can "lock" process. There is also a fix to manage the /var/run clean at debian startup.


There is also a security fix on the inter-daemon communication. In the previous versions, if someone did took over your poller server, it can use the poller->scheduler communication to  take over the scheduler. This is a known issue with the python pickle lib, and so we are cleaning the paquets while loading them now :)


Like always you will find the release on [github](https://github.com/naparuba/shinken/releases)


For the next version, it will be mainly about large IT performance monitoring. I'll post soon (next week) about what we did change to have a better "smooth" daemon behavior ^^

See you soon and have a good monitoring :)


