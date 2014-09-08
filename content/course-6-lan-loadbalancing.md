Title: Online course 6: LAN scalability, adding pollers
Date: 2014-09-08 10:20
Category: Talk
Tags: shinken, online course, scalability
Slug: online-course-6-lan-scalability
Author: Gabès Jean
AuthorLogin: naparuba
Summary: This is our 6th online course, we are looking at how you can add new pollers in order to launch even more checks :)



Hi,

This is our 6th online course. After the 5th from [Seb](https://twitter.com/Seb_Cvx) that was about creating checks, you got numerous ideas about what to check and make your IT even stronger. But more checks means more load, and maybe one poller is not enough now :)

We will look in this course about one #shinken core feature that will help you there: adding pollers is just tooooo easy :)

Let's start with two standard 2.0 installation in two linux box. We will disable all the daemons on the second box but not the poller.

You will be able to follow this tutorial on video soon.


### Our setup with 2 linux boxes

We will have 2 linux and we want the second to be just a poller available for the first box shinken. The box courses-a will be our primary one, and courses-b the extended poller one. We just installed shinken in the 2 boxes. Both got a valid dns entry :

    root@courses-a$ cat /etc/hosts
	[...]
	192.168.56.104  courses-a
	192.168.56.105  courses-b
	root@courses-b$ cat /etc/hosts
    [...]
    192.168.56.104  courses-a
    192.168.56.105  courses-b


We want to have something like this :

<img src='/images/course6/courses-6-global.png'>


### Preparing the first box (master)

Let's first focus on the `courses-a` box. This will be quite quick. The poller we want to enable on the `courses-b` box will have to connect to the `scheduler` on the `courses-a` to get its job. It will have the `scheduler` address and port from a configuration send by the `arbiter`.

But the default address of the `scheduler-master` is with the good port (7768), but with the `localhost` address. This won't be a problem for the arbiter to reach it, but if the poller on the `courses-b` try, it won't have a change to reach the `courses-a scheduler` :p

So all we need to do is to setup the LAN address on the `scheduler` definition object:

	root@courses-a$ vi /etc/shinken/schedulers/scheduler-master.cfg
	define scheduler {
	    scheduler_name      scheduler-master ; Just the name
	    address             courses-a   ; IP or DNS address of the daemon
	    port                7768        ; TCP port of the daemon
		spare               0   ; 1 = is a spare, 0 = is not a spare
        
        weight              1   ; Some schedulers can manage more hosts than others
		timeout             3   ; Ping timeout
		data_timeout        120 ; Data send timeout
		max_check_attempts  3   ; If ping fails N or more, then the node is dead
		check_interval      60  ; Ping node every N seconds
		modules
		realm   All
		skip_initial_broks  0
		use_ssl          0
	}


### Now the second box :)

All we need now it to look at the `courses-b` box, and let's disable LOT of daemons ^^

You must set `daemon_enabled=0`to the following files:

    root@courses-b$ grep daemon_enabled /etc/shinken/daemons/*ini
	/etc/shinken/daemons/brokerd.ini:daemon_enabled=0
	/etc/shinken/daemons/pollerd.ini:daemon_enabled=1
	/etc/shinken/daemons/reactionnerd.ini:daemon_enabled=0
	/etc/shinken/daemons/receiverd.ini:daemon_enabled=0
	/etc/shinken/daemons/schedulerd.ini:daemon_enabled=0

And also on the arbiter that is installed by default on the `courses-b`:

    root@courses-b$ grep daemon_enabled /etc/shinken/shinken.cfg
    daemon_enabled=0

Now you can restart your shinken daemons on the `courses-b` and look if only the poller is really started:

    root@courses-b$ /etc/init.d/shinken -d restart
    root@courses-b$ /etc/init.d/shinken status
	Checking status of scheduler
	scheduler NOT RUNNING
	     failed!
	Checking status of poller
	poller RUNNING (pid 25504)
	. ok
	Checking status of reactionner
	reactionner NOT RUNNING
	     failed!
	Checking status of broker
	broker NOT RUNNING
	 failed!
	 Checking status of receiver
	 receiver NOT RUNNING
	     failed!
	 Checking status of arbiter
	 arbiter NOT 
	     failed!

We temporary put the poller in debug mode so we will have all it's logs available to see the good connexions and internal data.

So now that our `courses-b` poller is ok and no other daemons will annoy it, we can declare it to the arbiter side on the `courses-a` server:

	root@courses-a$ cp /etc/shinken/pollers/poller-master.cfg /etc/shinken/pollers/poller-courses-b.cfg	
	root@courses-a$ vi /etc/shinken/pollers/poller-courses-b.cfg
	define poller {
	    poller_name     poller-courses-b
	    address         courses-b
	    port            7771
		spare           0
				
		manage_sub_realms   0   ; Does it take jobs from schedulers of sub-Realms?
		min_workers         0   ; Starts with N processes (0 = 1 per CPU)
		max_workers         0   ; No more than N processes (0 = 1 per CPU)
		processes_by_worker 256 ; Each worker manages N checks
		polling_interval    1   ; Get jobs from schedulers each N minutes
		timeout             3   ; Ping timeout
		data_timeout        120 ; Data send timeout
		max_check_attempts  3   ; If ping fails N or more, then the node is dead
		check_interval      60  ; Ping node every N seconds
		modules
		use_ssl          0
		realm   All
	}

So the only modification you need are the `poller_name` and `address` parameters. You can keep the others by default.

### Restart and look at your success :)

Then restart the arbiter `shinken` on the `ourses-a` server:

	root@courses-a$ /etc/init.d/shinken-arbiter restart

	
The `arbiter-master` will load the new poller and will give it some new job, like taking checks from the `courses-a's scheduler` :)

If we look at the `courses-b` logs, we can see the connexion:

	root@courses-b$ grep 'scheduler-master' /var/log/shinken/pollerd.log
	[1410170010] INFO: [Shinken] [poller-courses-b] Init connection with scheduler-master at http://courses-b:7768/ (3s,120s)
	[1410170010] WARNING: [Shinken] [poller-courses-b] Scheduler scheduler-master is not initialized or has network problem: Connexion error to http://courses-a:7768/ : couldn't connect to host
	[1410170012] INFO: [Shinken] [poller-courses-b] Connection OK with scheduler scheduler-master
	[1410170012] INFO: [Shinken] [poller-courses-b] Init connection with scheduler-master at http://courses-a:7768/ (3s,120s)
	[1410170012] INFO: [Shinken] [poller-courses-b] Connection OK with scheduler scheduler-master

Don't worry about the WARNING here. It's just the poller is too fast for the scheduler to be fully running with a configuration. If you got some few seconds after a `Connection OK` it means you are ok :)

So now you can look at this poller and look if it really get new checks from the scheduler:

    root@courses-b$ tail -f /var/log/shinken/pollerd.log
	...
    [1410170033] DEBUG: [Shinken] [0][scheduler-master][fork] Stats: Workers:1 (Queued:1 TotalReturnWait:1)
	...


Here the poller ask the scheduler and get one check and one other result to return to this scheduler. You will have one line by worker and by scheduler. So this poller is working great :)

### Did we forgot something?

But we forgot some things: pollers are launching check plugins, located in the `/var/lib/shinken/libexec/` directory or in the `/usr/lib/nagios/plugins/` directory for standard nagios plugins ones. And here you poller got none of them :)

So for each pollers you need to add the same plugins, so for example here you can just :
    
	root@courses-b$ apt-get install nagios-plugins
	
for the nagios plugins, and:
    
	root@courses-a$ scp -rp /var/lib/shinken/libexec/* root@courses-b:/var/lib/shinken/libexec/
	
And you will have all you need on the `courses-b` poller.



### The next course
The next course will be an extension of this one. Here all checks are distributed in all our pollers, in a round robin way. But maybe we want to dedicate one box for a specific checks or hosts? We will see this in the next course, about the poller tags, and one classic usage for this: pollers in DMZ :)


