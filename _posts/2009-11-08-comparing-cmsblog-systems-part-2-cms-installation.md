---
category: blog
layout: default
published: true
title: Comparing CMS/blog systems, part 2 - CMS installation
---
After installing three different blog systems (and trying to install a fourth), it's now time to install five different CMS packages. The five are:

* [CMS Made Simple](http://www.cmsmadesimple.org/)
* [Joomla!](http://www.joomla.org/)
* [modx](http://modxcms.com/)
* [Drupal](http://drupal.org/)
* [Typo3](http://typo3.org/)

The basic goal in this post is to see how easy it is to get these systems installed and ready for use. What obstacles will you have to overcome and how confused will you get before you can start using your CMS? Those are the questions.

## Environment
The environment for the systems is the same as in [the first blog post in this series](http://plind.dk/2009/10/24/comparing-cmsblog-systems-part-1-blog-installation), but I'll run through it again for good measure. The basic setup is Apache 2, PHP 5.3 and MySQL 5.0. With PHP 5.3 this series also becomes a test of how well the packages manage in a more up-to-date environment. Each package gets a separate bit of space, separate database and user, plus own VirtualHost. Finally, I'll try to make use of localisations where possible.

## CMS Made Simple

* Version tested: 1.6.6
* Requirements:
  * PHP 4.3+ up to 5.2.x
  * ImageMagick/GD support
  * MySQL 4.1+/PostgreSQL 7+

Although the package requirements stated that CMS Made Simple does not work under PHP 5.3 I figured I'd give it the benefit of the doubt - the latest package was date October 2009 giving it a theoretical chance - and try the install. I first downloaded and extracted the files:

```
mkdir cms &amp;&amp; cd cms
wget http://s3.amazonaws.com/cmsms/downloads/4470/cmsmadesimple-1.6.6-full.tar.gz &amp;&amp; tar -zxvvf cmsmadesimple-1.6.6-full.tar.gz
```
Note the extra steps above: the package doesn't come in it's own folder, so unless you're careful, you'll end up with a mess in your www directory.

After extracting things, I had a read through /doc/README.txt and /doc/INSTALL.txt. The latter proved to be a very good primer on the installation process, detailing how to get everything setup. I proceeded to setup a database and a user for the package and when everything was ready, aimed FF at the proper place.

Turns out the CMS Made Simple documentation is up to date: the system won't run on PHP 5.3. I could not get the installation process started as too many error outputs blocked the session handling. Moral of the story: don't go for CMS Made Simple if you want to use PHP 5.3.

* Ease-of-installation: 0-4(*)/5
* Instructions given: 4/5
* Confusion level: low(*)

(*) seeing as I couldn't actually run through the installation process, it wasn't easy - and the score is 0. However, the part that I did go through was OK handled and presented little problems. Similarly, the confusion level was low, up until the installation process crashed.

## Joomla!

* Version tested: 1.5.15
* Requirements:
  * PHP 4.3.10+
  * MySQL 3.23+
  * Apache 1.3+

The Joomla! site is pretty well done, getting the info you need from it is easy. A minute after browsing to it I was downloading the source files while looking at the requirements. The following should work fine:

```
mkdir joomla; cd joomla; wget http://joomlacode.org/gf/download/frsrelease/11396/45610/Joomla_1.5.15-Stable-Full_Package.zip -O joomla.zip &amp;&amp; unzip joomla.zip
```

You should now have the Joomla! sources unzipped in a folder, ready to get on with the installing. You'll need to setup a database for Joomla! as well as a user and grant all (not really needed but it's easier than getting into details) privileges to that user. You'll also need to set up a virtualhost file for it. And finally, make sure that apache can write to the files in the folder you installed Joomla! in. After that's done, pointing your browser at the web root of the install should kick off the remaining bits of the installation.

There's not much to say about the web-based install other than that using it is very easy and there's lots of documentation handed to you. Very well done.

The last step of the installation is removing the installation directory - if this is not done, the system won't function properly. It's a typical precaution to take, but one does wonder a bit why this can't be automated. PHP does have the capability to remove files and folders ... however, with a simple

```
rm -rf installation
```

you're through, and you can enjoy your working Joomla! install.

* Ease-of-installation: 4/5
* Instructions given: 4/5
* Confusion level: None

## MODx

* Version tested: 1.0.2
* Requirements:
  * PHP 4.3.11+
  * MySQL 4.1.20+ (but not 5.0.51, it seems)

MODx looks to be a nice and well-maintained system - latest release was dated November 4th '09. In general, the usability of the MODx site deserves a thumbs up: getting the needed info was very easy.

Getting started with the install should amount to

```
wget http://modxcms.com/download/ga/modx-1.0.2.tar.gz &amp;&amp; tar -zxvvf modx-1.0.2.tar.gz
```

However, the otherwise friendly people of MODx for some strange reason decided to throw obstacles in the way of users, and you have to register on their site to be able to download the sources of MODx. Fairly pointless exercise and I'm sure they get tons of useless signups that never lead to any site-activity.

After unpacking the files, setting up a virtualhost file, creating a database and database user, and setting permissions on the install folder of MODx, it's time to point the browser to the install folder. This will provide a page with a big, fat, red box notifying you that MODx is not installed and asking you if you want to, perhaps, install now. Clicking that will take you to the somewhat nicer designed install guide.

Like with Joomla! the install guide of MODx has been cared for. It's a breeze to get through it, with some javascript effects to make it even tastier. There's not quite as much documentation, but you won't really find yourself in need of it.

At the end of the install guide, there's a nice touch: the option to automatically remove the install folder. After setting up the few first bits such as Apache config files and MySQL user/database, you won't need to get into the dirty details again - MODx takes care of it (well, as long as you did a proper job the first time round).

* Ease-of-installation: 4(*)/5
* Instructions given: 3/5
* Confusion level: low

(*) while the install was easy, the download wasn't, as noted. Seeing as the competition doesn't block users from downloading, there's really no excuse for it.
## Drupal

* Version tested: 6.1.4
* Requirements:
  * PHP 4.3.5+
  * MySQL 4.1+ / PostgreSQL 7.4+

One of the most hyped CMS products, this package is supported by a huge community. The major advantage to this is the amount of plugins and modules available for the system. Something to toy with later - but first, the install.

The Drupal site is slightly overpacked with information. Finding the requirements list for Drupal 6.1.4 involved clicking through 5-6 ... a bit much when you're just at the step of wondering if it'll even work for you (having the requirements or at least a link to them along with a download link would be a good idea).

After downloading and unpacking

```
wget http://ftp.drupal.org/files/projects/drupal-6.14.tar.gz &amp;&amp; tar -xzvvf drupal-6.14.tar.gz
```

you need to spend some time with the online documentation or the INSTALL.txt file. Specifically, you'll need to copy the Drupal example settings file from sites/default/default.settings.php to sites/default/settings.php. After that, give Apache write permission to the sites/default/ folder so the install guide will be able to create what it needs. As always, you also need to set up a database and a database user with proper rights. Not to mention a virtualhost entry for Apache. After that, point the browser to the root of the installed folder.

Unfortunately, unlike what is specified on [the page of requirements](http://drupal.org/requirements) for Drupal, it does NOT work with PHP 5.3. Well, at least the installer doesn't work - but seeing as there is no Drupal without installation, that amounts to the same thing. The problem is the use of the ereg() function - deprecated in PHP 5.3 along with all other ereg* functions. So, Drupal is a no-go, unfortunately.

* Ease-of-installation: 0-3(*)/5
* Instructions given: 3/5
* Confusion level: medium

(*) the part of the installation I could go through was ok, though too manual. Seeing as the installation crashed it's not really possible to rate it, hence the 0-3

## Typo3

* Version tested: 4.2.10
* Requirements:
  * PHP ?
  * MySQL ? / others ?

While it's easy to find the download link on typo3.org, it's seemingly impossible to find a page on requirements. So, it's very hard to say if the installation will prove to be a waste of time or not. Only one way to find out - trial and error.

So first thing is grabbing the files and unpacking them. Typo3 has an unusual setup - you need to download a skeleton site as well as the sources. You can get both in one packaged file though, so it's not too bad.

```
wget http://downloads.sourceforge.net/project/typo3/TYPO3%20Source%20and%20Dummy/TYPO3%204.2.10/typo3_src%2Bdummy-4.2.10.zip?use_mirror=dfn -O typo3.zip &amp;&amp; unzip typo3.zip
```

You should now have the files needed to setup a Typo3 site, and while the site doesn't offer you helpful info straightaway, the INSTALL.txt and README.txt have lots of good stuff. Among other things, they go through all the steps of installing Apache, PHP and MySQL ... so, all the way from scratch. Luckily, I don't need to do that - I just need to set up a database and a user for Typo3, set the proper permissions on the unpacked files (specifically allowing for writing to /typo3temp, /typo3/temp, /typo3conf/localconf.php) and then add the virtualhost record. After that, the install should work as per the typical web install guide.

And so after these steps, it's time to point the browser to the installed CMS. The first thing to greet you when you do is a big JS alert box, notifying you that you really should change the default password. Obviously good to know, but this is not really the way information like this should be presented to the user. The next pages are better done and quite simple in their presentation which is good - it's the typical setup pages asking for database connection details. You don't want any unnecessary info here, confusing the user.

At the end of the web install guide things do get more confusing, though. Specifically, the user is told that a given install folder is a big security problem. You're then presented with three options for fixing this problem ... none of which involve clicking a link and seeing it get done. In fact, the install guide recommends that you carry on setting up the Typo3 installation instead of tending to the security threat. That's not proper handling of priorities - either don't tell me about the problem now or make me fix it straight away.

Choosing to carry on with the installation I'm then greeted with a fairly confusing install tool, the purpose of which is hard to guess. Is it used for reinstalls? For configuring advanced stuff? For letting me know what I chose? Apparently all of the above. Sensing the confusion level rising I then opted to have a look at the installed site. The frontpage greeted me with PHP warnings  and a Typo3 error to the effect that no pages on rootlevel could be found - regardless of the install of skeleton site. Looking at the admin pages, things didn't get much better. Tons of PHP warnings make it pretty much unusable - you either have to switch off PHP warnings or get to work with search and replace taking out ereg* calls. Not a good start for the Typo3 install, but at least it didn't crash completely - there's a possibility it could work.

* Ease-of-installation: 3/5
* Instructions given: 4/5
* Confusion level: medium

## Conclusion
Three out of five CMS systems aren't ready yet for the latest stable version of PHP. I find that rather surprising actually - PHP 5.3 has been on it's way for some time, there's been plenty of time to weed out the problematic parts. My best guess is that the forces behind these projects figure that web-hosts probably won't upgrade to 5.3 for a while and thus the majority of users won't see the problems. It's fair reasoning but still a bad excuse - most deprecated functions can be fixed in very little time.

Overall, the web guided installations have really come far and most of these products treat the user to a nice and only slightly confusion ride. Some could still do with some polishing but overall it's not that hard anymore installing your own CMS. The biggest annoyance comes from the need to remove various files or copy them round during installation: why is this a demand? Why don't the installers check if the needed permissions are there? With the installers being so user-friendly it's just strange that they're not taken the last step.
