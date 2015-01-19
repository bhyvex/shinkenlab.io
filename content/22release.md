Title: 2.2 release
Date: 2015-01-19 10:20
Category: Talk
Tags: shinken
Slug: release-2-2
Author: Gabès Jean
AuthorLogin: naparuba
Summary: Here is the 2.2 version ^^




Hi :)


After some weeks of tests of the [RC1 version](/news-and-announcements-2015), it's time to release the 2.2 ^^

Thanks to everyone that help us to get this version out, by coding or reporting bugs :)

As always, you can have a look at the full [changelog](https://github.com/naparuba/shinken/blob/master/Changelog) but I'll try to explain youthe main new features you can expect in this version :)


### Snapshots

The main new feature of this version are the **snapshots**. 

Snapshots are quite simple in fact. I did have lot of case at work where my boss asked me "why there was such a big load at 3AM, this did crash XXX batch". And the problem is that I did have the load average and other performances values, but no other analysis clues.

What did I need on this time? Just a dumb "ps aux" launched at 3AM during the load average warning/critical phase. And that's **spanshots** are about: they allow you to launch some commands based on some criteria like "go into warning state" and with large ouput (like a full ps output). This output will be exported to the broker that will be able to save it somewhere (file, mongo, ...). Then it's quite easy for an UI to go query it and display it aside your logs when you try to find what did ask so much load :)

A snapshot will ask for a classic command:




    define command {
      command_name    dump_processes
      command_line    /usr/bin/ssh -H $HOSTADDRESS$ "ps ax -o user,vsz,rss,pcpu,command"
    }



Then you can call it from a service definition, here a load average one :


    define service{
       service_description    Load Average
       use                    linux-ssh-service
	   register               0
	   host_name              linux-ssh
	   check_command          check_ssh_linux_load_average
	 
	   snapshot_command    dump_processes
	   snapshot_enabled    1
       snapshot_criteria   c
       snapshot_period     night
       snapshot_interval   15
    }

The parameters are quite simple:

  * snapshot_command: what to launch. Beware: it will be lauched by a reactionner daemon.
  * snapshot_enabled: 0 by default. Must have a command and be 1 to launch the snapshot
  * snapshot_criteria: on which element state to launch the command (w=warning, c=critical, etc etc)
  * snapshot_period: only launch during this period. Mainly the night when admins are not here :)
  * snapshot_interval: do not hummer the host with such commands, so only launch every 15min the snapshot :)

The scheduler will scheduler the snapshot, that will be launched by the reactionner daemon. then the information is grok by the broker nad it can send it to a module. I wrote a module to manage this, feel free to write your own if you don't like mongodb :)

      shinken install snapshot-mongodb

Enable this module as usual:

    define module {
       module_name     snapshot-mongodb
       module_type     snapshot_mongodb
       uri             mongodb://localhost/?safe=false
       database        shinken
    }

And in your brokers objects:
				
    define broker {
       broker_name     broker-master
       [...]
       modules  webui, snapshot-mongodb
    }

It's now up to the UIs or your reporting tools to use these historical data and show them to your users :)


### Exporting internal performance data
On key point about Shinken is its speed. But how to see it and monitor it? In the previous version (2.0) there was a first attempt with an HTTP API to get some data about performances. But now it will even easier as the daemons can automatically export theses data to metrology tools :)

#### To statsd
The first metrology tool to be manage is [statsd](https://github.com/etsy/statsd/). It's really easy to setup one. When it's up, all you have to do is to enable it on your shnken.cfg file:


    statsd_host=my-statsd-server
    statsd_port=8125
    statsd_prefix=shinken
    statsd_enabled=1

You only need to set it on your shinken.cfg file (so the arbtier daemon). It will be automatically send to all the others daemons :)

#### To kernel.shinken.io
If you don't want to start a statsd daemon, you can use the [kernel.shinken.io](http://kernel.shinken.io) service. It's totally free and it can accept metrology data from your daemon and it will show you the last 7 days metrics of your daemons. It's currently in Beta phase and should be in stable phase quite soon :)

To enable it, it's very easy. All you need is to have a [shinken.io](http://shinken.io) account and look at your [profile](http://shinken.io/~) page for your **api_key** and your **secret**. Then add them to the shinken.cfg file, like for statsd:

    api_key=ABCDEFGHIJ
    secret=KLMOPQRST

If you need a proxy, just add it too:

    http_proxy=http://monproxy:3128

No password, or hsotname, or contact name will be send to this API. Only metrics. And of course the data are crypted before being send ;)



### About old webui and livestatus versions

If you are using webui or livestatus module, please update the module too when you are upgrading the shinken framework. I'm updating the shinken.io versions, but to be sure you may need to take the version directly from github for theses two modules:

  * https://github.com/shinken-monitoring/mod-livestatus
  * https://github.com/shinken-monitoring/mod-webui

We did try to not break compatibility with the old moduels version, but for theses two ones it was not possible without droping some bug fixes. So we did break compatibility with previous module versions.


### What next?

I already present the new release cycle on my [last post](news-and-announcements-2015). We will focus on the IT elastic parts, like for my part the addition of hosts without restarting the arbiter daemon ^^

I'll send more regulary news about this, and also about my new side project about service discovery named [kunai](https://github.com/naparuba/kunai) because it will be a perfect match with the hot host insersion feature :)

See you soon and have a good monitoring :)


