Title: Online course 5: Monitoring Plugins
Date: 2014-08-07 
Category: Talk
Tags: shinken, online course, monitoring-plugins, development
Slug: online-course-5-monitoring-plugins
Author: Coavoux Sébastien
AuthorLogin: Seb-Solon
Summary: This course is intended to explain what are monitoring plugins and how they work with Shinken.  


Hi guys!

This is another online course. I say another because I have no idea about the number it will get ;). This time it's about monitoring plugins! 

First of all, I have to say that I will stick to the generic word `monitoring` during this course because the plugins can be used with other software than Shinken. But I cannot guarantee that I'll manage to do it the whole video :)


The video is a lightening talk about the subject, I suggest you to watch the video with the course. I always talk a LOT (well maybe that's not a good point):

<iframe width="800" height="600" src="//www.youtube.com/embed/uuZB3ONF5ZE" frameborder="0" allowfullscreen></iframe>

### What do you mean by plugin?

Well, from a very technical point of view a plugin is only a small (or not) piece of code. The most common one is `ping`. A simple ping can give you informations about a device (unless ICMP response is turned off)

I have carefully chosen the words "piece of code". There is no specific programming language involved in it. This means that a plugin can be written in the language you want. This, is a really cool thing :)


### Plugin API

The main function of a plugin is to do the "dirty" job. It collects the wanted data, in a specfic way (ssh, http, snmp) and returns it to Shinken. Why dirty? Because this is usually the tricky part in monitoring. It always involves credentials, firewall configuration, computation... which is a huge source of trouble. 

This leads to the following question : How can I give the data I have about my server to my monitoring software?
The software does not need to know specifically why I did not gather data. But the software needs to know what does it mean! 

That's why a `common Api` for all monitoring plugin was created. Every plugin is talking the same language and all the specific code (for checking status) is separated from the sofware code!

This is the main reason why Shinken's developers may not be familiar with your super old printer plugins. They don't care about how you get the data. 


So, what is this specific language plugins are using?

    | Return Code |   State   | Meaning                                               |
    | ------------|-----------| ------------------------------------------------------|
    | 0           | OK        | This is the expected state. Everything looks fine     |
    | 1           | WARNING   | Something is not correct, but it's not very important |
    | 2           | CRITICAL  | There is a problem and this is serious.               |
    | 3           | UNKNOWN   | The plugin is unable to tell you what's going on      |

Shinken will use this to know the state of a service or an host. 

This is the basic part of the API. 

But what if you also want to return some metrics? How output the data so that Shinken will understand it ?

Well, that a part of the Api, you also have a standard defined for the output. 

Basically an ouput looks like this :

     [STATUS] : Output fisrt part | 'label'=value[UOM];[warn];[crit];[min];[max]

The second part is called : perfdata output, it's used to give metrics. 

-  STATUS is one of the above state
-  label is the name of the metric
-  UOM refers to the metric's unit
-  warn, crit are threshold to trigger WARNING or CRITICAL State (can be None)
-  min, max are the minimum / maximum the value can get (can be None)

With this, you can send all you need to Shinken. If you need more details please visit the [monitoring-plugins website](https://www.monitoring-plugins.org/doc/)

### Let's code!

We have now, a API and a programming language (our favorite one of course). You may think the next step is coding, but IT IS NOT. (Un)Fortunately, we are not alone, a lot of monitoring plugins exist. Some of them are already packaged in almost every Linux distribution! 

So, if you have just bough a new router/server or you are about to do it, just check for existing plugins! That's the first step before thinking to create a new one. 


### Let's code for real now!
Ok, let's assume you've found nothing for your device. There is no other option : you have to create it. But keep in mind something before starting : is my plugin will be used passevily or actively? In a case you you to use passive checking you have two choices :

*  Implement passive result submimission
*  Use existing tools to submit passive result from monitored host


Passively submitting result by it's own have advantages : 

*  Shinken Arbiter accepts http passive results : http post are easy to implement. 
*  Nothing else to setup on the host site (such as nrpe/nsca client)

But it has also drawbacks :

*  What if you have several passive plugins? Code duplicity is bad
*  What if data get lost? Add some cache file? You are now implementing a daemon, not a plugin.

Well, I think you know more or less everything you should know about monitoring plugins. Time for example


### Python example

This example is very basic, but tries to be real. Because returning random does not really make sense :)

I don't want to handle options an such thing in this example to keep it easy to understand.

    #!/usr/bin/python

    import urllib
    import sys

    # Constants
    OK = 0
    WARNING = 1
    CRITICAL = 2
    UNKNOWN = 3


    # Should be options
    url="http://www.google.com"


    try:
        output = urllib.urlopen(url)
    except:
        print "UNKNOWN : Cannot open url"
        sys.exit(UNKNOWN)

    # HTTP code not in this range is not good for me
    if 200 <= output.code < 300:
        print "OK : code returned withing range"
        sys.exit(OK)

    elif 400 <= output.code < 500:
        print "CRITICAL : bad code returned. Client error"
        sys.exit(CRITICAL)

    else:
        print "WARNING : bad code returned. Redirection spotted or server error"
        sys.exit(WARNING)


Here is the sample. This opens the url and only check the output code. This is basically what we need to check a website.

Launch it with :
  python check_url.py

See the output :
   OK : code returned withing range

Of course we can improve this a lot : perfdata, check content, add warning and critical threashold (on response time), add option to follow redirect, etc...


### Next course

Well, maybe object presentation. 
