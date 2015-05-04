Title: 2.4 release
Date: 2015-05-04 10:20
Category: Talk
Tags: shinken
Slug: release-2-4
Author: Gabès Jean
AuthorLogin: naparuba
Summary: Here is the 2.4 version ^^




Hi :)


After more than 320 commits and 4 release candidate versions, it's time to release the [2.4 version](https://github.com/naparuba/shinken/archive/2.4.tar.gz) ^^

Thanks to everyone that participate to this release, by sending us patchs or report bugs :)

As always, you can have a look at the full [changelog](https://github.com/naparuba/shinken/blob/master/Changelog). This version was the first with the new [3 months release cycle](/news-and-announcements-2015/) and we did it in ... 4 months ^^ 

We did drifted because the RC phase was very long. This version was really focus about technical debt reduction and bug hunting. That's why the new feature list is reduced (even if advanved users will like the service_includes new property for the hosts :) ).

We did try to reduce the compatibility breaks, but some modules are known to ask an update before run in 2.4 version ([ip-tag](https://github.com/shinken-monitoring/mod-ip-tag), [livestatus](https://github.com/shinken-monitoring/mod-livestatus) and [webui](https://github.com/shinken-monitoring/mod-webui) are known to be in this case). So give a test with the modules you are using currently before update your production :) If modules are launched, it will be ok, it's mainly the mdoule bot method and some internal objects that did changed.

The next release cycles will be focus on large environnement performance (50K+ services) boosting, and especially the boot time and the livestatus modules (that you are using if you have [Thruk](http://www.thruk.org/) or [MK/Livestatus](https://mathias-kettner.de/checkmk_multisite.html) UIs :) ).

See you soon and have a good monitoring :)


