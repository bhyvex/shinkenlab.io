Title: Online course 2: installing WebUI
Date: 2014-03-07 10:20
Category: Talk
Tags: shinken, online course, module, webui
Slug: online-course-2-webui
Author: Gabès Jean
AuthorLogin: naparuba
Summary: This is our second online course, we are now installating the default shinken web interface, WebUI



Hi,

This is our second online course, and we now installing the default shinken ui: WebUI.

You need a shinken 2.0 installation. For this just follow the [first course](/online-course-1-installation/) :)


You can follow this tutorial with this video:

<iframe width="800" height="600" src="//www.youtube.com/embed/fno4btMmtcQ" frameborder="0" allowfullscreen></iframe>

(You can also have a [fr version of the video](http://www.youtube.com/watch?v=pHV9pah1MzI))


The WebUI interface is a broker daemon module. This daemon role is to grab and expose the monitoring data to the outside world with modules. One of this module is the WebUI interface.

### Install the WebUI

We will use the shinken cli command to grab and install the webui module from [shinken.io](http://shinken.io/package/webui). It's quite easy, and remember, launch it as the `shinken` user.

    shinken@debian# shinken search webui
	auth-cfg-password (naparuba) [module,auth,authentification,webui] : Shinken module for UI authentification from simple password for configuration file
	auth-htpasswd (naparuba) [module,webui,auth,authentification] : Shinken module for UI authentification from Apache passwd files
	mod-mongodb (naparuba) [module,webui,arbiter,mongodb,mongo] : Module used for all mongodb connections, like for saving UI data, or loading configuration inside the Arbiter
	sqlitedb (naparuba) [module,broker,sqlite,webui] : Generic module for others to export data into a Sqlite database
	ui-graphite (naparuba) [module,broker,webui,graphite,graphs] : Enable Graphite graphs inside the WebUI
	ui-pnp (naparuba) [module,broker,webui,pnp4nagios,graphs] : Enable PNP4Nagios graphs inside the WebUI
	webui (naparuba) [module,broker,webui,interface] : Main Web interface for Shinken
	
We got numerous modules related to the webui one. First we install this last one :)

    shinken@debian# shinken install webui	
	Grabbing : webui
	OK webui
	
This will download and untar some files:

  * `/var/lib/shinken/modules/webui/` : the module code (in Python), the pages and http ressources
  * `/etc/shinken/modules/webui.cfg`: module configuration file

The WebUI configuration looks like:
    
	define module {
	  module_name     WebUI
	  module_type     webui
	  host            0.0.0.0
	  port            7767
	  auth_secret     CHANGE_ME
	  allow_html_output   0
	  max_output_length   100
	  manage_acl          1
	  play_sound          0
	  login_text          Welcome on Shinken WebUI
	  modules
	  #http_backend        auto
	  #remote_user_enable      1
	  #remote_user_variable
	}
	
Let's have a look at the parameters:

  * `module_name` : the name as a daemon should call it. Must be uniq
  * `module_type` : the type of the module. Used by the daemon to know which python code to call :)
  * `host` : the address to listen, 0.0.0.0 mans all interfaces
  * `port` : port to listen. As this is launched by the `shinken` user you can't put 0 there sorry
  * `auth_secret` : private key to build cookie. MUST be changed :)
  * `allow_html_output` : by default the webui strip all HTML from the outputs, if you want to put colors or things like this in your outputs, just enable it. But beware of the security risks! (javascript)
  * `max_output_length` : by default outputs are limited to 100 characters
  * `manage_acl` : should user only see elements that they are contacts of? :)
  * `play_sound` : try to guess what it is :)
  * `login_text` : little text that is shown above the login prompt
  * `modules` : which modules does the webui must load. We will saw this later :)
  * `http_backend` : which python http backend to load. By default wsgiref from vanilla python, but cherrypy is better if available :)
  * `remote_user_enable` and `remote_user_variable` : if you set webui begind a reverse proxy that does the authentification

Let's just give a try if we add this `WebUI` module to the broker daemon. All we need to do is to is to add it into the `modules` of the broker configuration :

    shinken@debian# cat /etc/shinken/brokers/broker-master.cfg
	[...]
	modules     WebUI
	[...]

We can restart shinken and connect to the WebUI that will be availalbe on the `7767` port.

    root@debian# /etc/init.d/shinken restart
	
We try to identify with the `admin/admin` account from our contact defined at :

    shinken@debian# cat /etc/shinken/contacts/admin.cfg
	define contact{
	    use             generic-contact
        contact_name    admin
	    email           shinken@localhost
	    pager           0600000000   ; contact phone number
	    password        admin
	    is_admin        1
	}


And we go... a big #fail :D

<img src='/images/course2/course2-fail.png'>

### WebUI authentification

Why? It's because the WebUI authentification phase is managed by its auth modules, and we add none, so the webUI can't authentify us. Let's look at which auth modules are currently available:

    shinken@debian# shinken search webui auth
	auth-cfg-password (naparuba) [module,auth,authentification,webui] : Shinken module for UI authentification from simple password for configuration file
	auth-htpasswd (naparuba) [module,webui,auth,authentification] : Shinken module for UI authentification from Apache passwd files
	auth-active-directory (naparuba) [module,webui,auth,ldap] : Active directory auth for WebUI

One (`cfg-password`) is a simple auth by looking at the contact `password` value, another (`htpasswd`) is about checking the password in an apache htpawd file, and the last one (`active-directory`) is about checking the password with active directory (or openldap). We will take the first one for this course.

    shinken@debian# shinken install auth-cfg-password

There is nothing to configure on the module (`/etc/shinken/modules/auth_cfg_password.cfg`) but we need to declare it on the WebUI configuration :

    shinken@debian# grep modules /etc/shinken/modules/webui.cfg
    modules             Cfg_password
	
Now we can restart shinken so this module wil be load by webui:

    root@debian# /etc/init.d/shinken restart
	
This time the connexion is OK :)

<img src='/images/course2/course2-connectok.png'>

In the All view you will get your `localhost` checks:
   
<img src='/images/course2/course2-all.png'>   

You will got nothing in the `/impacts` view, and it's normal, because this view is done to show you the important apps information, but we currently didn't defined one.

In the `/dashboard` we got a new error, with a BIG red screen:

<img src='/images/course2/course2-dashboard.png'>   

### User preference backend

The dasboard is specific to each user, and so WebUI must save your user preference (widgets and co). But this task should be managed by the modules (like if you want flat file save or in a distant database). You can choose to export all data into mongodb, but we will look at the simpliest one that save the data into a local `sqlite` file.

    shinken@debian# shinken install sqlitedb
	shinken@debian# cat /etc/shinken/modules/sqlitedb.cfg
	define module {
	    module_name     SQLitedb
	    module_type     sqlitedb
        uri             /var/lib/shinken/webui.db
	}
	
The default path should be ok, let's add this to the WebUI modules list:

    shinken@debian# grep modules /etc/shinken/modules/webui.cfg
	modules             Cfg_password,SQLitedb
	
And restart shinken:
  
    root@debian# /etc/init.d/shinken restart
	
Now the `/dashboard` view is OK :)

<img src='/images/course2/course2-dashboardok.png'>

After adding some `widgets` you will have such a screen:

<img src='/images/course2/course2-dasboardfilled.png'>

That's all about today, now you can add new hosts and saw them in your WebUI :)

Note: if you don't modify modules you don't need to restart all the shinken daemons, you only need to restart the arbiter :

    root@debian# /etc/init.d/shinken-arbiter restart


### The next course

The next course will be about the 1.4 migration, and the next one about a first distributed installation :)


