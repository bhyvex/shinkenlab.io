Title: Online course 3: 1.4 to 2.0 migration
Date: 2014-03-11 10:20
Category: Talk
Tags: shinken, online course, migration
Slug: online-course-3-migration-to-20
Author: Gabès Jean
AuthorLogin: naparuba
Summary: This is our third online course, we are now looking at the migration to the 2.0 from the 1.4 one :)



Hi,

This is our third online course, we are now looking at the migration to the 2.0 from the 1.4 one :)

We will install a 1.4.1 version and we will migrate to a 2.0 one. So let's start :)

You can follow this tutorial with this video:

<iframe width="800" height="600" src="//www.youtube.com/embed/Qywg19zRWKI" frameborder="0" allowfullscreen></iframe>

(You can also have a [fr version of the video](http://www.youtube.com/watch?v=XzZQp5upCFU))


### Install the 1.4 version

In the 1.4 version, there was an install script. We already talk about why we deprecated it in the [2.0 blog entry](/2-0-and-the-project/). We will use it in order to install our 1.4 version on a debian box.

    root@debian$ wget http://www.shinken-monitoring.org/pub/shinken-1.4.1.tar.gz
    root@debian$ tar xvfz shinken-1.4.1.tar.gz
	root@debian$ cd shinken-1.4.1/
	root@debian$ ./install -i
	
And here we got a lot of magic. In fact it will do quite the same as you did in the [first course](/online-course-1-installation/) but without you know it. Like we said on the blog post we prefer that you trully understand the install phase.

After the installation, the `shinken`daemons are alive and all is working:

<img src='/images/course3/course3-14runwell.png'>

    root@debian$ ps -fu shinken
	UID        PID  PPID  C STIME TTY          TIME CMD
	shinken  19876     1  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-scheduler -d -c /usr/local/shinken/etc/schedulerd.ini
	shinken  19882 19876  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-scheduler -d -c /usr/local/shinken/etc/schedulerd.ini
	shinken  19888     1  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-poller -d -c /usr/local/shinken/etc/pollerd.ini
	shinken  19895 19888  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-poller -d -c /usr/local/shinken/etc/pollerd.ini
	shinken  19903     1  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-reactionner -d -c /usr/local/shinken/etc/reactionnerd.ini
	shinken  19909 19903  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-reactionner -d -c /usr/local/shinken/etc/reactionnerd.ini
	shinken  19918     1  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-broker -d -c /usr/local/shinken/etc/brokerd.ini
	shinken  19928 19918  4 15:51 ?        00:00:05 python /usr/local/shinken/bin/shinken-broker -d -c /usr/local/shinken/etc/brokerd.ini
	shinken  19930     1  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-receiver -d -c /usr/local/shinken/etc/receiverd.ini
	shinken  19937 19930  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-receiver -d -c /usr/local/shinken/etc/receiverd.ini
	shinken  19948     1  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-arbiter -d -c /usr/local/shinken/etc/nagios.cfg -c /usr/local/shinken/etc/shin
	shinken  19952 19948  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-arbiter -d -c /usr/local/shinken/etc/nagios.cfg -c /usr/local/shinken/etc/shin
	shinken  19965 19948  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-arbiter -d -c /usr/local/shinken/etc/nagios.cfg -c /usr/local/shinken/etc/shin
	shinken  20008 19918  2 15:51 ?        00:00:02 python /usr/local/shinken/bin/shinken-broker -d -c /usr/local/shinken/etc/brokerd.ini
	shinken  20017 19918  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-broker -d -c /usr/local/shinken/etc/brokerd.ini
	shinken  20055 19888  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-poller -d -c /usr/local/shinken/etc/pollerd.ini
	shinken  20058 19903  0 15:51 ?        00:00:00 python /usr/local/shinken/bin/shinken-reactionner -d -c /usr/local/shinken/etc/reactionnerd.ini
	

The important thing to search is the place of the 1.4 configuration files:

  * `/usr/local/shinken/etc/nagios.cfg`: main configuration file, for hosts, commands and such nagios objets
  * `/usr/local/shinken/etc/shinken-specific.cfg`: side configuration file, mainly about daemons (scehdulers, brokers, ...) and the modules

In the 2.0 version, we only got the `/etc/shinken/shinken.cfg` that is loading configuration directories, so we got only one main file. But hopefully the `/etc/init.d/shinken` init.d script is still able to load two file, especially for this migration phase :)

Please mind to stop the shinken daemons before install the 2.0 :

    root@debian$ /etc/init.d/shinken stop

And then we will be able to install the 2.0 version :)

### Install the 2.0 version

Here it's like the [first course](/online-course-1-installation/) so please refer to this last one for the prereqs.

    root@debian:$ pip install shinken
	[...]
	Successfully installed shinken

We now got a ready to run 2.0 version, but currently with its own configuration files:

    root@debian:$ ls -thor /etc/shinken/
	-rw-r--r-- 1 shinken 8.4K Mar 10 15:59 time_templates.cfg
	-rw-r--r-- 1 shinken  255 Mar 10 15:59 sample.cfg
	-rw-r--r-- 1 shinken   84 Mar 10 15:59 dev.cfg
	-rw-r--r-- 1 shinken 3.6K Mar 10 15:59 shinken.cfg
	drwxr-xr-x 5 shinken 4.0K Mar 10 15:59 sample
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 schedulers
	drwxr-xr-x 3 shinken 4.0K Mar 10 15:59 certs
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 commands
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 services
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 servicegroups
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 reactionners
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 contactgroups
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 arbiters
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 receivers
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 notificationways
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 modules
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 pollers
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 packs
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 hosts
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 discovery
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 contacts
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 templates
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 realms
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 hostgroups
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 dependencies
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 resource.d
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 brokers
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 timeperiods
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 escalations
	drwxr-xr-x 2 shinken 4.0K Mar 10 15:59 daemons
	
But we want to have our 1.4 configuration. So we will just link the init.d configuration to our old configration that run well since long. We just need to edit the `/etc/default/shinken` file to change the `SHINKENCFG` and the `SHINKENSPECIFICCFG` path to link to our previous `nagios.cfg` and `shinken-specific.cfg` files.

    root@debian:$ vi /etc/default/shinken
	SHINKENCFG="/usr/local/shinken/etc/nagios.cfg"
	SHINKENSPECIFICCFG="/usr/local/shinken/etc/shinken-specific.cfg"

Warning: the `SHINKENSPECIFICCFG` is by default commented, remind to uncomment it ;)

Then we can give a try to a check:

    root@debian:~# /etc/init.d/shinken check
	Doing config check
	. ok
	
Looks great :)

So we can restart it :

    root@debian$ /etc/init.d/shinken start
	
If we look at the processes, the arbiter load the `nagios.cfg` and the `shinken-specific.cfg`:

    root@debian$ ps -fu shinken
	UID        PID  PPID  C STIME TTY          TIME CMD
	shinken  22147     1  1 16:10 ?        00:00:00 python /usr/bin//shinken-scheduler -d -c /etc/shinken/daemons/schedulerd.ini
	shinken  22150 22147  0 16:10 ?        00:00:00 python /usr/bin//shinken-scheduler -d -c /etc/shinken/daemons/schedulerd.ini
	shinken  22189     1  0 16:10 ?        00:00:00 python /usr/bin//shinken-poller -d -c /etc/shinken/daemons/pollerd.ini
	shinken  22192 22189  0 16:10 ?        00:00:00 python /usr/bin//shinken-poller -d -c /etc/shinken/daemons/pollerd.ini
	shinken  22234     1  0 16:10 ?        00:00:00 python /usr/bin//shinken-reactionner -d -c /etc/shinken/daemons/reactionnerd.ini
	shinken  22237 22234  0 16:10 ?        00:00:00 python /usr/bin//shinken-reactionner -d -c /etc/shinken/daemons/reactionnerd.ini
	shinken  22279     1  0 16:10 ?        00:00:00 python /usr/bin//shinken-broker -d -c /etc/shinken/daemons/brokerd.ini
	shinken  22282 22279  0 16:10 ?        00:00:00 python /usr/bin//shinken-broker -d -c /etc/shinken/daemons/brokerd.ini
	shinken  22325     1  0 16:10 ?        00:00:00 python /usr/bin//shinken-receiver -d -c /etc/shinken/daemons/receiverd.ini
	shinken  22328 22325  0 16:10 ?        00:00:00 python /usr/bin//shinken-receiver -d -c /etc/shinken/daemons/receiverd.ini
	shinken  22363     1  0 16:10 ?        00:00:00 python /usr/bin//shinken-arbiter -d -c /usr/local/shinken/etc/nagios.cfg -c /usr/local/shinken/etc/shinken-specific.
	shinken  22365 22363  0 16:10 ?        00:00:00 python /usr/bin//shinken-arbiter -d -c /usr/local/shinken/etc/nagios.cfg -c /usr/local/shinken/etc/shinken-specific.
	shinken  22394 22189  0 16:10 ?        00:00:00 python /usr/bin//shinken-poller -d -c /etc/shinken/daemons/pollerd.ini
	shinken  22402 22234  0 16:10 ?        00:00:00 python /usr/bin//shinken-reactionner -d -c /etc/shinken/daemons/reactionnerd.ini
	
But the WebUI page is in 404, why? Let's look at the broker daemon logs:

    root@debian$ less /var/log/shinken/brokerd.log
	2014-03-10 16:10:21,211 [1394464221] Warning : [broker-1] The module type livestatus for Livestatus was not found in modules!
	2014-03-10 16:10:21,212 [1394464221] Warning : [broker-1] The module type simple-log for Simple-log was not found in modules!
	2014-03-10 16:10:21,213 [1394464221] Warning : [broker-1] The module type webui for WebUI was not found in modules!
	
Why? Becase like we said in the [2.0 blog entry](/2-0-and-the-project/), the 2.0 came with no modules, you need to install them manually. In fact all we need at the module codes (python) because we alrady got the configuration part in the `/usr/local/shinken/etc/shinken-specific.cfg` file :)

All the modules you need are in the [shinken.io](http://shinken.io) website. But first let's give a try to which modules are need by just greping the `shinken-specific.cfg` file:

    root@debian$ grep modules /usr/local/shinken/etc/shinken-specific.cfg
    modules Apache_passwd, ActiveDir_UI, Cfg_password, PNP_UI, Mongodb, Glances_UI
    modules logsqlite
    modules
    modules
    modules Livestatus, Simple-log, WebUI
    modules CommandFile
    modules PickleRetention
    modules

I only kept the modules loaded from the daemons configurations or the modules (like Livestatus or WebUI). In fact we got some groups :

  * `WebUI`: `Apache_passwd, ActiveDir_UI, Cfg_password, PNP_UI, Mongodb, Glances_UI` are modules about authentification, graphs and user preference.
  * `Livestatus`: `logsqlite` is about how to save and read logs into the livestatus module
  * `Broker` : `Livestatus, Simple-log, WebUI` are about loading the agregated log file, the livestatus api and launching WebUI by default
  * `Arbiter` : `CommandFile` is about open and read the `nagios.cmd` file for the external commands
  * `Scheduler`: `PickleRetention` is about the retention module. We will analyse them in another course :)

All we need now is just to download and install theses modules. It will install the module code into the `/var/lib/shinken/modules` and the configuration into `/etc/shinken.modules`. But we don't mind about the last one, because `/etc/shinken` is no longer read for the configuration.

Here is a mapping about the modules names and their packages names into [shinken.io](http://shinken.io):

<table>
<thead>
<th>Module name</th>
<th></th>
<th>Package name</th>
</thead>
<tbody>
<tr><td>Apache_passwd</td><td>-></td><td>auth-htpasswd</td></tr>
<tr><td>ActiveDir_UI</td><td>-></td><td>auth-active-directory</td></tr>
<tr><td>Cfg_password</td><td>-></td><td>auth-cfg-password</td></tr>
<tr><td>PNP_UI</td><td>-></td><td>ui-pnp</td></tr>
<tr><td>Mongodb</td><td>-></td><td>mod-mongodb</td></tr>
<tr><td>logsqlite</td><td>-></td><td>logstore-sqlite</td></tr>
<tr><td>mongologs</td><td>-></td><td>logstore-mongodb</td></tr>
<tr><td>nulllogs</td><td>-></td><td>logstore-null</td></tr>
<tr><td>Livestatus</td><td>-></td><td>livestatus</td></tr>
<tr><td>Simple-log</td><td>-></td><td>simple-log</td></tr>
<tr><td>WebUI</td><td>-></td><td>webui</td></tr>
<tr><td>CommandFile</td><td>-></td><td>named-pipe</td></tr>
<tr><td>PickleRetention</td><td>-></td><td>pickle-retention-file-generic</td></tr>
</tbody>
</table>

 
Here we go, let's give a try to the modules installs :)

Beware: this must be done under the `shinken` user, and don't forget to launch the `shinken --init` on your first launch of the shinkexn CLI

    shinken@debian# 
	shinken install auth-htpasswd
    shinken install auth-active-directory
    shinken install auth-cfg-password
    shinken install ui-pnp
    shinken install mod-mongodb
    shinken install logstore-sqlite
    shinken install logstore-mongodb
    shinken install logstore-null
    shinken install livestatus
    shinken install simple-log
    shinken install webui
    shinken install named-pipe
    shinken install pickle-retention-file-generic
	
Now we can restart our daemons. for this first launch launch it under the debug mode so you will see if there are still some errors:

    root@debian$ /etc/init.d/shinken stop
    root@debian$ /etc/init.d/shinken -d start
	
And try to look if the modules are load for the daemons :
   
    root@debian$ grep correctly /var/log/shinken/*-debug.log
	/var/log/shinken/broker-debug.log:[1394465689] Info :    [broker-1] I correctly loaded the modules: [Livestatus,Simple-log,WebUI]
	/var/log/shinken/broker-debug.log:[1394465689] Info :    I correctly loaded the modules: [Apache_passwd,ActiveDir_UI,Mongodb,PNP_UI,Cfg_password]
	/var/log/shinken/broker-debug.log:[1394465690] Info :    I correctly loaded the modules: [logsqlite]
	/var/log/shinken/poller-debug.log:[1394465689] Info :    I correctly loaded the modules: []
	/var/log/shinken/reactionner-debug.log:[1394465689] Info :    I correctly loaded the modules: []
	/var/log/shinken/receiver-debug.log:[1394465689] Info :    [receiver-1] I correctly loaded the modules: []
	/var/log/shinken/scheduler-debug.log:[1394465828] Info :    I correctly loaded the modules: [PickleRetention,PickleRetention]
	
All the daemons with modules are now loading them, your migration is complete, you can enjoy your WebUI now :)

<img src='/images/course3/course3-migrationdone.png'>

### The next course
The next course will be about our first distributed setup: the high availability, with shinken spare daemons :)


