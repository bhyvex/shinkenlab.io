Title: WebUI 2 release
Date: 2015-09-22 10:20
Category: Talk
Tags: webui, release
Slug: webui2-release
Author: Frédéric MOHIER
AuthorLogin: mohierf
Summary: Here a new version for webui :)


Hi everybody,

After several months of hard work with Guillaume [@maethor](https://github.com/maethor), I am happy to announce the release of a completely new version of the Shinken Web UI.

This hard work started almost one year ago with the BS3 branch I opened on the project. At that moment, I wanted to have a nice UI to consult key data managed by the Shinken framework, from different Web browsers.

After several months of sleeping, Guillaume and I reactivated this project. Today we have achieved an almost completely redesigned version.

This new WebUI version features:

 * full Bootstrap 3 Web User Interface
   <ul>
   <li>modern and responsive UI compatible with all Web browsers and mobile devices</li>
   </ul>


 - real time Shinken data consulting 
    <ul><li>user is able to see almost all Shinken framework managed data</li></ul>

 - out of the box operational User Interface
   <ul>
   <li>the new WebUI embeds all the main needed modules, significantly simplifying configuration</li>
   <li>no need to 'shinken install' many modules before starting</li>
   </ul>

 - powerful Shinken resources filtering
   <ul><li>hosts, services may be problems, impacts ... filtering is an help for the user !</li></ul>

 - new Shinken modules strongly enriching Web UI features
   <ul><li>mongo-logs, storing all shinken logs and hosts availability in MongoDB allowing to do basic SLA with Shinken</li></ul>

- multi user with restricted "client" accounts
   <ul><li>a huge work has been done on the security to allow access to "client" accounts which are allowed to see only a subset of the hosts and services</li></ul>
 

 Many other features are available in this new interface. You are very welcome to deploy and test it...


 Last but not least, we opened a Wiki in the project repository to make documentation available for every user. The one link that you should use today is: [https://github.com/shinken-monitoring/mod-webui/wiki/Installation](https://github.com/shinken-monitoring/mod-webui/wiki/Installation).


 In the next weeks, we will extand the Wiki to focus on the new killer features of the new Web UI; such as filtering problems, system logs, hosts availability, ...
 

Back to this object: 'shinken install webui2' is your best friend ...

Best regards


Fred

