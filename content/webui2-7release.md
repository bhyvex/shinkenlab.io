Title: WebUI 2.7 is out
Date: 2017-10-23 10:20
Category: Talk
Tags: webui, release
Slug: webui2-7-release
Author: Guillaume Subiron
AuthorLogin: maethor
Summary: A new version of the WebUI is out: 2.7


Hi everybody,

After lot of work the new WebUI version is out: 2.7.

## Major improvements

Besides a lot of UI improvements, the goal of this version is to allow the user to stay as much as possible in the "Problems" view (/all).

<center><img src='/images/webui-2-7/all_problems.png'>
<i>Problems view</i>
</center>

The accordions have been debugged and they are displaying a lot of useful information to avoid going into the detailed hosts/services views.

<center><img src='/images/webui-2-7/graphs.png'>
<i>Graphs</i>
</center>


This version is also making a new place to comments, in the element's accordions. It should be easy to have a glance at the history of a service, and to add a new comment when necessary. It is actually opening the way to a more collaborative Shinken WebUI (something you usually find in tickets trackers or in Trello).

<center><img src='/images/webui-2-7/comment.png'>
<i>Comments</i>

</center>


<center><img src='/images/webui-2-7/host-history.png'>
<i>Element history</i>

</center>



## Enhancements
Some parts did get improved too:

 - Improve and cleanup sidebar
 - Lot of improvements and cleanup in problems view accordions
 - Cleanup style of accordions
 - Display comments in accordions
 - Display notes in accordions
 - Allow to add comments in accordions
 - Debug impacts in accordions
 - Add HTML anchors on accordions
 - Hide checkboxes and check_all and problems page (replaced by link hidden behind Business impact, and ctrl+click or maj+click on items)
 - Move actions in topbar
 - Lot of code cleanup in forms and actions
 - Infinite scrolling in element history
 - Navbar fixed on top of screen
 - When searching for hostgroups and hosttags, search returns hosts and associated services
 - Replace downtime icon, improve get_fa_icon_state, and add column in problems view with ack and downtime icons
 - Better log management
 - Cleanup eltdetail information tab

## Features

Other features are available:

 - Allow to add and list comments in item accordion (problems view)
 - New is:SOFT, is:HARD and is:FLAPPING filters
 - Quick "Schedule a downtime" with preset durations
 - Experimental, may not work at all : New alerts statistics module allowing to see most frequent alerts on last 30 days (depends on mod-mongo-logs)



## Bugs

Aside enhancements there are some fixed bugs:

 - [591](https://github.com/shinken-monitoring/mod-webui/issues/591) Fix Sound Toggle bug (Thanks @ornoone)
 - [592](https://github.com/shinken-monitoring/mod-webui/issues/592) Fix Sound icon (Thanks @ornoone)
 - [593](https://github.com/shinken-monitoring/mod-webui/issues/593) Fix Tooltip breaking tables columns (Thanks @ornoone)
 - Fix tooltip bug on dashboard
 - Manage Nagios Process avatar
 - Debug ACK and Downtime modals
 - Fix crash when searching for 'bp:'


Good update :)


Best regards




