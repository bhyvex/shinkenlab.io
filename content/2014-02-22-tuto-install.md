Title: Online course 1: installing shinken 2.0
Date: 2014-03-04 10:20
Category: Talk
Tags: shinken, online course, installation
Slug: online-course-1-installation
Author: Gabès Jean
AuthorLogin: naparuba
Summary: This is our online course, and of course we are starting for the very start: how to install shinken :D



Hi,

This is our first online course, and we are starting for the very start: how to install shinken :D

We want to have a running shinken installation on a simple linux box. We will take a debian 7 but also provide information for centos6.

You can follow this tutorial with this video:

<iframe width="800" height="600" src="//www.youtube.com/embed/jtUytVXtw14" frameborder="0" allowfullscreen></iframe>

Your shinken daemons will need a user/group to run. Its name is quite obvious, it's just `shinken`, and as a simple user.

    root@debian# adduser shinken
    root@debian# id shinken
	uid=1000(shinken) gid=1000(shinken)

Now your user/group is ok, you need to install some dependencies for shinken installation :

  * `python-pycurl` : (great and powerfull) lib for http connexions between the shinken daemons
  * `python-setuptools` and `python-pip`: installation tool for python lib


Just launch:

    root@debian# apt-get install python-pycurl python-setuptools python-pip

And for Centos6:

    root@centos# wget http://mirror-fpt-telecom.fpt.net/fedora/epel/6/i386/epel-release-6-8.noarch.rpm && rpm -Uvh epel*rpm
    root@centos# yum install python-pycurl python-setuptools python-pip
    root@centos# echo 'alias pip="/usr/bin/pip-python"' >> $HOME/.bashrc
    root@centos# . $HOME/.bashrc

	
As our dependencies are ok, we can now get and install our shinken daemons:

    root@debian# pip install shinken
	[...]
	Shinken setup done

Let's have a look at the paths that are used by shinken:

* `/etc/shinken`: all your configuration will be there
* `/usr/bin/shinken-*`: the shinken daemons scripts
* `/var/lib/shinken`: where you will find the shinken modules or your check plugins
* `/var/log/shinken`: the logs (what did you expect? :) )

In order to start your new daemons, you just need to use the init.d script:

    root@debian# /etc/init.d/shinken start
	
Your can look at your daemons by checking your shinken user, and even query a daemon:

    root@debian# ps -fu shinken
    root@debian# curl http://localhost:7770/
    OK

We can look at the only host that is checkd by default: the localhost :) Like all hosts, it's configuration is in a file in `/etc/shinken/hosts/`:

    root@debian# cat /etc/shinken/hosts/localhost.cfg

By default this host is a dummy one, with no real checks (`generic-host` is a default `template` that give default values for an host). We want some basic linux monitoring (disks, load, memory, I/O and stuff like that). That what monitoring `packs` are done for. 

Lof of them are available on the [shinken.io](http://shinken.io) website. Let's look what is available for our linux host, by using the `shinken` command that allow to query the `shinken.io` site. 

From now we will switch to the user shinken so the packages installations will be done by this user, and so there won't be problem for it to use them :)

    root@debian# su - shinken

The shinken cli command need to be initialized, in order to generate the `~/.shinken.ini` file with all the paths to the shinken directories:

    shinken@debian# shinken --init

Then you can start to search for linux pack:

    shinken@debian# shinken search linux
    glances (david-guenault) [pack,system,linux,glances] : Standard check through checkglances.py and glances server
    linux-snmp (naparuba) [pack,linux,snmp] : Linux checks based on SNMP
    linux-ssh (dessaiimrane) [pack,linux,ssh] : Linux checks based on SSH without any script on distant server
    pack-glances (david-guenault) [pack,system,linux,glances] : Standard check through checkglances.py and glances server

We got some options for monitor our linux. I'm a big fan of agentless monitoring, so I'm choosing the `linux-ssh` one that allow us to check a linux with just a ssh key on it. We just need to install it:

    shinken@debian# shinken install linux-ssh

This package came with included plugins and put them into `/var/lib/shinken/libexec`, but currently not all are like this.

    shinken@debian# ls -la /var/lib/shinken/libexec/*py
	check_net_stats_by_ssh.py
	check_nfs_stats_by_ssh.py
	[...]

Theses plugins need the `python-paramiko` lib for the ssh connexion, so we must install it:

    shinken@debian# su -
	root@debian# apt-get install python-paramiko
	root@debian# exit
	shinken@debian#

As theses plugins are launching commands by `ssh` we need to login to the distant server of course :)
The good way to manage this is to authentify with a ssh key. One important note here: the shinken daemon run under the `shinken` user, so it must the the key of this user.

    shinken@debian$ ssh-keygen
	
And we must deploy this key on the monitored host too, with the `shinken` user (by default, you can change it on the hosts configurations):

    shinken@debian$ ssh-copy-id -i ~/.ssh/id_rsa shinken@localhost

Let's give a try at this plugin to see if all is OK:

    shinken@debian$ /var/lib/shinken/libexec/check_load_average_by_ssh.py -H localhost -i ~/.ssh/id_rsa
	Ok: load average is good 0.00,0.00,0.00 |  load1=0.00;1.00;2.00;; load5=0.00;1.00;2.00;; load15=0.00;1.00;2.00;;

We can now add the linux-ssh tag to the localhost definition:

    shinken@debian$ cat /etc/shinken/hosts/localhost.cfg
    define host{
        use                     linux-ssh,generic-host
        contact_groups          admins
        host_name               localhost
        address                 localhost
        }

Great :) Now you can restart shinken and tail your shinken logs to wait for some alerts:

    root@debian# /etc/init.d/shinken restart
    shinken@debian$ tail -f /var/log/shinken/schedulerd.log


To go further you will maybe need the shinken documentation. You can look at it online on [readthedocs](https://shinken.readthedocs.org/) but you can also got it locally on your shinken server (like on an offline network). All you need to do is:

    shinken@debian$ shinken doc-serve

will open an http server on the `8080` port with the documentation :)


The next course will be about installating and configure the default shinken web interface: `webui`. See you soon for this new course, and let me know in the comments if you like this first course (or not) :)


