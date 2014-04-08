Title: 2.0 RC: last patch to test :)
Date: 2014-04-08
Category: Talk
Tags: shinken, 2.0, release, patch
Slug: 20-rc-last-patch-before-release
Author: Gabès Jean
AuthorLogin: naparuba
Summary: We got a patch about the last bug we did have on the RC version on debian 7, it's time to test this new RC version so we can release the 2.0 :D



Hi,

We got a patch about the last bug we did have on the RC version on debian 7, it's time to test this new RC version so we can release the 2.0 :D

In the last RC dwe did have a bug on the `reactionner` daemon on the `debian 7` systems (maybe on others but we saw them on the debian 7 one). This bug was related to a low level bug on the python [multiprocessing lib](https://docs.python.org/2/library/multiprocessing.html) (that is used to manage worker process communication). If you saw such thing on your logs:


    Traceback (most recent call last):
      File "/usr/bin/shinken-reactionner", line 97, in <module>
      daemon.main()
	  ebug :   HTTP: calling lock for get_broks
	  File "/usr/local/lib/python2.7/dist-packages/shinken/satellite.py", line 991, in main
	  self.do_mainloop()
	  File "/usr/local/lib/python2.7/dist-packages/shinken/daemon.py", line 261, in do_mainloop
	  self.do_loop_turn()
	  File "/usr/local/lib/python2.7/dist-packages/shinken/satellite.py", line 783, in do_loop_turn
	  self.adjust_worker_number_by_load()
	  File "/usr/local/lib/python2.7/dist-packages/shinken/satellite.py", line 544, in adjust_worker_number_by_load
	  self.create_and_launch_worker(module_name=mod)
	  File "/usr/local/lib/python2.7/dist-packages/shinken/satellite.py", line 403, in create_and_launch_worker
	  q = self.manager.Queue()
	  File "/usr/lib/python2.7/multiprocessing/managers.py", line 670, in temp
	  authkey=self._authkey, exposed=exp
	  File "/usr/lib/python2.7/multiprocessing/managers.py", line 927, in AutoProxy
	  incref=incref)
	  File "/usr/lib/python2.7/multiprocessing/managers.py", line 733, in __init__
	  self._incref()
	  File "/usr/lib/python2.7/multiprocessing/managers.py", line 783, in _incref
	  dispatch(conn, None, 'incref', (self._id,))
	  File "/usr/lib/python2.7/multiprocessing/managers.py", line 105, in dispatch
	  raise convert_to_error(kind, result)
	  multiprocessing.managers.RemoteError: 
	  ---------------------------------------------------------------------------
	  Traceback (most recent call last):
	  File "/usr/lib/python2.7/multiprocessing/managers.py", line 207, in handle_request
	    result = func(c, *args, **kwds)
	    File "/usr/lib/python2.7/multiprocessing/managers.py", line 428, in incref
	    self.id_to_refcount[ident] += 1
	      KeyError: 'd506c8'
	  ---------------------------------------------------------------------------


Then you got the `reactionner` bug. What is quite strange is that :

* it only hapen on the `reactionner` daemon, but `poller` and `reactionner` got the exact same code
* it's on the lower level of the `multiprocessing lib` (goot luck to catch this....)

The main difference between the reactionner and the poller daemons is that the reactionner have less activity than the poller. If it have very low activity, the worker process can kill themselve to get back some few Mb of memory to the system (was the `Harakiri` entries on the 1.4 logs).

In the [patch I propose this morning](https://github.com/naparuba/shinken/commit/d001d1f6fa7e5dcdb2cbb5590c9e341c49ee95e4) I did two changes:

* I remove the harakiri phase. The master daemon was launching new workers just after the previous one was killing itself, so it's was quite useless
* I switch the communication layer for the inter-process. It was based on `unix socket` (named pipe on the `/tmp` with numerous `pymp-` directories). I switch to a localhost tcp communicaton instead (random high port, so you don't need to touch your iptables rules ;) )

I think this will solve this case (no more call on this part of the code, and even if it does, this won't lead to the same bug). So please give a test. The bug did happen something like once a day, so if in 3 days we got no more crash like this, we will be OK for 2.0 release! :D

So please test it, I'l publish a `RC3` version on pypi (for the pip intall). All you will need to do is to launch:

    $ root@debian:  pip install shinken --update

And check your version with :

    $ root@debian:shinken --version
	shinken 2.0-RC3

Good test :D

<img src='/images/20-prerelase-patch/need-you.jpg'>
