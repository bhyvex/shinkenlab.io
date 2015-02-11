Title: kernel.shinken.io
Date: 2015-02-12 10:20
Category: Talk
Tags: shinken, kernel.shinken.io, support
Slug: kernel-shinken-io
Author: Joaquim Roy
AuthorLogin: joaquim
Summary: Here is the announce of the kernel.shinken.io platform to analyse your running shinken


Greetings,


Jean did announce in the [2.2 announce](/release-2-2/) that since this version the shinken framework is now able to send its inner metrology data to the outside, and especially the [kernel.shinken.io](http://kernel.shinken.io) platform. The account for kernel is the [shinken.io](http://shinken.io) one.

Like said in the announce post, configure your Shinken framework so it start to send data is very easy. All you need is to look at your [shinken.io](http://shinken.io) account and look at your [profile](http://shinken.io/~) page for your **api_key** and your **secret**. Then add them to the shinken.cfg file:

    api_key=ABCDEFGHIJ
    secret=KLMOPQRST

If you need a proxy, just add it too:

    http_proxy=http://myproxy:3128

Like we already said, no password, or hostname, or contact name will be send to this API. Only metrics. And of course the data are crypted before being send ;)



## What does it looks like?

When you go to [kernel.shinken.io](http://kernel.shinken.io) you will have the login prompt. Just put your shinken.io login :)
<center><img src='/images/kernelio/login.png'></center>

Then you will see the **hive**. Where you will have all your daemons. Here is a simple shinken framework vanilla, mono server installation.

<center><img src='/images/kernelio/hive.png'></center>

When you click on a daemon, like here the arbiter, you will see its last data, for the daemon and its modules.

<center><img src='/images/kernelio/arbiter.png'></center>

And more important, on the daemon metrics tab, you will have an access to all the daemon metrics. We are currently workong on making all theses data the more clear with the more help to understand them ^^

<center><img src='/images/kernelio/graph.png'></center>

For each metric you will have the data for the last hour. We are working at also show the last day data. For each metric you will have the average value, but also the min and max value (computed by the shinken framework).


## Why propose this?

At Shinken Solutions we can see every day on our customers IT that the framework is really powerful and can manage a lot of checks. We know at what to look at to see and check this. We want to share this and made it easy for everyone to look at its monitoring framework.

We build it also to propose to our support customers proactive alerts about their shinken installation. So it's easier for us to spot at server crash or configuration misfit for our customers. But if everyone can use it for their own setup, it's even better :D


## Will it will stay free?

We want this current usage to be free for all framework users. Some advanced features in the future will be reserved to our [framework support customers](http://www.shinken-solutions.com), because they will consume far more space and computation on our servers (like "what is the most cpu intensive check plugin on my pollers?"). But the free usage of kernel will still be enahanced in the next months.



So ready to check your shinken? Let's [look at what is inside your powerful framework now](http://kernel.shinken.io) :D



