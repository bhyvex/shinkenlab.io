Title: Some news and announcements for 2015
Date: 2015-01-13 10:20
Category: Talk
Tags: shinken kunai
Slug: news-and-announcements-2015
Author: Gabès Jean
AuthorLogin: naparuba
Summary: some news after the post of the 2.2rc1 version about a new release cycle, a forum migration, new websites, professionnal support offers & a new side project about service discovery ^^




Hi :)


We did tag the 2.2RC1 some weeks ago.

Thanks to some testers we did find some few bugs in it (mainly about configuration regression and notification problems). I just find that one of my customer configuration is valid on the 2.0.3 version but no more on the master one, so I'm looking why. But I think we are on the verge to a solid 2.2 release this week (maybe the 15 or 16)

We already did talk about the Changelog in the RC1 email, but I'll post it again in the 2.2 release post :)


### New release cycle
We did talk about the current release cycle with Savoir Faire Linux guys (SebSolon, Titilambert & gst). It's related to the [github ticket](https://github.com/naparuba/shinken/issues/1394)

The main idea is that currently we are drifting to much and so we are switching to a fixed date release cycle. A 3 months based one. It will be a 3 phases cycle:

  * ~few weeks: re-factoring and such enhancements
  * one month: features
  * one month: freezing. No more feature (only in branch) and a 2 weeks RC phases (2 rc, only by weeks)

We did open the roadmap for the 2.4 version, with what we will add into it. It's this [ticket](https://github.com/naparuba/shinken/issues/1451)

Feel free to add the point you want to code/enhance in it, it's really open :)

For my part, my next big feature will be hot host definition. It's linked to another project of mine ([kunai](https://github.com/naparuba/kunai)) but I'll talk about it a bit later in this email.


### Our own repositories (debian/redhat/centos) & docker images

Some of the members of the team are working on distribution packages (debian & redhat like). But distribution inclusion is really a problem because it's really slow and in fact will be a real problem with the 3 months cycle. So we are working on our own repositories like other projects (mongodb for example). It will be far easier to manage, and the packages will be closer to the upstream version (yes distro, I'm talking about your stupid rules on the init.d scripts...)

Titilambert is already working on the debian package and Hvad got a great RPM :)

David Guenault is also working on docker images. I think it can be a really great way to setup shinken in the future :)


### Forum migration

We currently have issues on the forum due to spam. We are both fighting it every day with Andreas. He propose a new forum platform (vbulletin) and we did try a migration some months ago, that fail because vbulletin and nginx are not happy together in our tries. So we are redoing the migration one more time but with a more simpler architecture.

So if you saw change in the forum the next days/weeks, it will be normal ^^


### New websites

I did hire a designer some months ago and you can see the result of her work on our websites :)

She did a really great job, and now the websites have a common identity:

  * [shinken-monitoring.org](http://shinken-monitoring.org) : project main web site, about linking to the documentation, forum and co
  * [shinkenlab.io](http://shinkenlab.io): news & tutorial/courses
  * [shinken.io](http://shinken.io): exchane platform for pack&modules, and the new kernel.shinken.io shinken daemon monitoring tool ^^
  * [shinken-solutions.com](http://shinken-solutions.com): commercial part, about Enterprise edition & the professionnnal support offer on the framework version

More of them are available in my github repository, so if you think about an enhancement or a typo, just send me a pull request :)


### Professional support offer

I'm happy to announce that aside the Enterprise edition, my company (Shinken Solutions) is launching a professional support offer for the shinken framework version ^^

We (Shinken Solutions) are focusing on high end support. We have partners that are the best to know how the shinken framework is installed and configured, and so they provide the first levels of support. This combo can provide the best to be sure your Shinken will be running for years :)

For example if you are in the Canada you can ask to [Savoir Faire Linux](https://www.savoirfairelinux.com/) (where SebSolon, Titilambert & Gst are working) for this support :)

Other partners should be announced soon on other countries. You can follow the shinken-solutions & shinkenlab websites for such news ^^


### New side project about service discovery: Kunai

Like some of you already know, I did work on a side project for a customer the last months. It's a service discovery tool named Kunai. I did start to work on it in may, but I open the repository the last month at [https://github.com/naparuba/kunai](https://github.com/naparuba/kunai)

It's in MIT licence, and it's main focus is to manage very elastic environments. You can see it as a tool like [etcd](https://github.com/coreos/etcd)) but in a totally distributed mode (no central server :) ).

Even if it's main focus is service discovery (**not** network discovery, it do not scan the network ^^), it's already a great distributed agent (local scheduling, ngios checks, **batteries included** checks like for linux system, apache, mongodb or docker, automatic conf+plugins synchronization between nodes, etc etc). For the monitoring capabilities it's at the same level as Sensu, but without need to have a central node :)

I already wrote an article about it in Linuxmag (france), and I'm looking for a reviewer of an English version, and traductors for other languages :) Just ping me if you are interested :)


This project do not require Shinken to work, and Shinken won't need Kunai to work too. But I think using both can be a really great combo for the next versions (you boot a node in kunai and it will be automatically populated with local checks and will be inserted in shinken).

if you want to give a try to it, just drop me a mail and I'll send you the documentation I'm currently writing for it :)

Kunai will have its own site & documentation, but I think most of you will find it a good replacement for nrpe or such agent :)

### Conclusion

That's a quite big email (and I wrote it twice thanks to gmail...). I think it can be better if I stick so a weekly email that talk about what we did and to get more feedbacks too ;)

So please give a test to the master version during this week, and expect the 2.2 for the end of the week (⌐■_■)


