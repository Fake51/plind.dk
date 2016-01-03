---
category: blog
layout: default
published: true
title: Comparing CMS/blog systems, part 1 - blog installation
---
In order to get to know more about the various open source CMS and blog systems out there, I've decided to fill up some hard disk space by installing a bunch of them and comparing them as I go along. I'll be detailing all my progress here with notes thrown in left and right. The basic setup of this small series of blog posts will be:

* Installation
* Day to day tasks
* Going beyond basics
* Hacking it

The series may turn out longer or shorter, but that's the opening strategy. Now onto the players (I got inspiration for the list by checking out [OpenSourceCMS](http://php.opensourcecms.com/) - very nice site):

* Blogs
 * [Textpattern](http://textpattern.com/)
 * [Nucleus](http://www.nucleuscms.org/)
 * [Serendipity](http://www.s9y.org/)
 * [WordPress](http://wordpress.org/)
* CMS systems
 * [CMS Made Simple](http://www.cmsmadesimple.org/)
 * [Joomla!](http://www.joomla.org/)
 * [modx](http://modxcms.com/)
 * [Drupal](http://drupal.org/)
 * [Typo3](http://typo3.org/)

## Environment
Environment is Apache 2, PHP 5.3 and MySQL 5.0. Given the use of PHP 5.3 this is, in part, also a test of how up to date the software packages are. Apart from PHP it won't be a test of how the software performs in an up to date environment, though.

Each of the packages will be setup in it's own space on my local machine (running ubuntu on an XPS m1330) and each will get it's own virtualhost in Apache, to make things easy as well as fair/square. Something to the effect of:

```
<VirtualHost *>
 ServerName subject
 DocumentRoot /var/www/subject
 ErrorLog /var/log/apache2/subject.log
 <Directory /var/www/subject/>
  Options FollowSymlinks MultiViews
  AllowOverride all
  Order deny,allow
  Deny from all
  Allow from 127.0.0.1
 </Directory>
</VirtualHost>
```

Each will also get it's own database with access from own user, amounting to something like:

```
CREATE DATABASE subject DEFAULT CHARACTER SET utf8;
CREATE USER 'subject'@'localhost' IDENTIFIED BY 'subject';
GRANT ALL ON subject.* TO 'subject'@'localhost';
```

If present in the product, I'll make use of localisations, specifically Danish (mainly to test it, as I prefer English).

Todays subject is blog installation, so, the first four on the above list. In the next post I'll be looking at installing the CMS systems.
## Textpattern

* Version tested: 4.2.0
* Requirements listed:
 * PHP 4.3+
 * MySQL 3.23+
 * mysql + xml extensions for PHP

I started by [downloading the source files from the textpattern site](http://textpattern.com/download) and unpacking them using

```
wget http://textpattern.com/file_download/56/textpattern-4.2.0.tar.gz &amp;&amp; tar -xzvvf textpattern-4.2.0.tar.gz
```

It's worth noting here that it's also possible to download the source files using SVN which can come in very handy when updating core files. There's a slight difference between the SVN files and the latest package but mainly in terms of file hiearchy.

Onwards with the installation, I had a look at the accompanying README.txt file which was fairly short. 1) drop files in web root or somewhere else, 2) create a database and 3) load /textpattern/setup/ in a browser. The shortness of instructions either means that things are easy to do or that only little time was spent writing them up. To figure out which I created a 'textpattern' user and database, set up the Apache VirtualHost and then pointed FF to http://textpattern/textpattern/setup/. Which worked without further ado, even let me select my very own localised language.

However, having selected a non-English language, I got a slight let-down: nothing was translated into my language, instead I just got raw string keys like 'welcome_to_textpattern'. That's not overly impressive - I can understand some words not being translated in the main product, but nothing on the first page you click into in the installer? After entering details on the MySQL connection, I got another disappointment: I was shown a textarea with config details I should copy and paste into a config file. Probably due to the localisation problems the page didn't actually let me know what file to use but did show me that I needed to put it in /textpattern/.

```
vim /var/www/textpattern/textpattern/config.php
```

Two things worth noting here: PHP has no problem writing data to a file and while copypasting isn't rocketscience it doesn't take many extra tabs from a browser to screw up a file, especially when the best practice of not using end PHP tags is not followed.

The third step consists of setting up an admin user and here things are nicer. No double email to validate things, no hidden password. Good good. This appeared to be the last step in the process as I was told index.php would be accessible after this, but unfortunately I wasn't so lucky: the first thing to greet me from http://textpattern/index.php was a big fat error message in xdebug orange to the effect that some columns did not exist although the code assumed they did. This went away after a page refresh though, but in the meantime I also managed to log into a non-functioning admin area - both experiences very confusing for a new user and fairly unnecessary. After that, textpattern seemed to work ok with no further work needed.

* Ease-of-installation: 4/5
* Instructions given: 2/5
* Confusion level: Medium to High

## Nucleus CMS

* Version tested: 3.50
* Requirements:
 * PHP 5.0.6+
 * MySQL 3.23+

First, grab source files and unpack. In this case, that includes grabbing the extra language files, which translates to

```
wget http://downloads.sourceforge.net/project/nucleuscms/1.%20Nucleus%20Core/Nucleus%20v3.50/nucleus3.50.zip?use_mirror=dfn &amp;&amp; unzip nucleus3.50.zip
cd nuclues3.50/nucleus/language/ &amp;&amp; wget http://prdownloads.sourceforge.net/nucleuscms/danish-3.22.zip?download &amp;&amp; unzip danish-3.22.zip
```

Next, I had a look for a readme file and found a readme.html in the main folder. I set up the VirtualHost entry as the next thing and then directed FF to http://nucleus/readme.html which worked fine - and the documentation on installing that followed was well written and easy to follow, which was cool. A fly in the ointment was the advice on chmod'ing files and folders with 0666 or 0777 - this should be a last resort, not the first suggested point!

After these points on file setup I was directed to install.php which should take care of the rest. After loading this, it seemed time to create another user/database pair, as the first questions I got were about MySQL connections. Having done this, I could go on to filling in all the details for the blog ... some of which were slightly confusing as there were no explanations for them (such as a ping plugin).

At the end of the form there was a minor annoyance: a warning that the user should submit the form once and **only once!** While this is normally not a bad point, it's a whole lot better to make sure the user only **can** submit the form once, instead of trying to work around this problem. After that surprise there was the positive point of seeing Nucleus remind people to change access properties of the config.php file back to something secure.

After the install was done, there was a bit of maintenance to be done by hand: the install files must be removed

```
rm install.*
```

After removing these files, the blog was setup and functional.

* Ease-of-installation: 4/5
* Instructions given: 3/5
* Confusion level: Low

## Serendipity

* Version tested: 1.4.1
* Requirements:
 * PHP 4.3.0+
 * MySQL/PostgreSQL/SQLite
 * Apache

First, grab the source code and extract files:

```
wget http://prdownloads.sourceforge.net/php-blog/serendipity-1.4.1.tar.gz?download &amp;&amp; tar -zxvvf serendipity-1.4.1.tar.gz
```

Serendipity also lets you get the source files through SVN like textpattern, an obvious plus.

Next step: check documentation. Serendipity doesn't come with installtion documentation, which is a minus in my book. Why do I have to check their site for further info, when I already have downloaded their stuff? On the plus side though, the documentation is very good, and definitely prepares you for the installation.

After adding a user/database and setting up the VirtualHost, it's time to run the installer, which is done by aiming the browser at the main folder of the Serendipity install (here http://serendipity/). The installer first does a check to see what works and what needs some work, which is a great feature. After tweaking some file permissions, I got the choice of easy install or advanced install - I chose the latter, as I wanted to see what one could set. I wasn't disappointed, there was a whole lot of settings to work with. Unfortunately, this also meant that the admin user setup got tucked away in the middle of the setup, and as the values all came prefilled, I was close to just scrolling by that bit. There was only one thing bugging me in the options: you have to set a prefix for the database tables of Serendipity, even if you're giving it a separate database to play with.

After checking the form, I hit 'complete installation' and then instantly had errors in my face. Turns out that Serendipity 1.4.1 is **NOT** compatible with PHP 5.3 - quite a few deprecated functions were used. As the installer ended by claiming that things were in fact working, I decided to brush the warnings aside and have a look at things. That, unfortunately, turned out impossible, as Serendipity really is incompatible with PHP 5.3 - neither the main site nor the admin part works. I presume the cause of this is that the 1.4.1 version is from January (PHP 5.3 was released half a year later) but that doesn't really help, especially as the documentation claims Serendipity is fully 5.x compatible ([third paragraph under requirements](http://www.s9y.org/36.html#A3)). The alternative is to try the beta (latest release from August) or a nightly ... neither of which is really acceptable to someone that wants a stable blog.

* Ease-of-installation: 0-3(*)/5
* Instructions given: 4/5
* Confusion level: Maximum

(*) if the installer had worked, it would have been a 3. Seeing as now I'll have to either install something else or muck about in .php files to get it working, it's a 0
## WordPress

* Version tested: 2.8.5
* Requirements:
 * PHP 4.3+
 * MySQL 4.0+

As with the other blog packages, first grab the sources and extract them from their package:

```
wget http://wordpress.org/latest.tar.gz &amp;&amp; tar -zxvvf latest.tar.gz
```

After that's done, it's time to set up the VirtualHost for WordPress. Like Nucleus, WordPress comes with a readme.html, so that's the first thing to point the browser at once you can. This file will then tell you to manually edit wp-config.php and after that run wp-admin/install.php ... maybe a famous 5-minute install but could have been done easier. You have to enter variables for the DB connection as well as authentication keys, and especially the last could do with some more documentation or a better interface.

After that, the install is pretty much done - you only have to enter a name for your blog and an email address, and you're done. Well, at least I thought so. On the [wordpress.org](http://wordpress.org/) site it states that you'll be downloading version 2.8.5 when you get the sources. However, after installing I found myself looking at a notice wanting me to update my install to version 2.8.5 ... If I didn't just install that version, what did I install?! Judging by the WordPress dashboard, it appears the package you download is actually 2.8.4 and to finish installing 2.8.5 you have to update the install through the dashboard. So much for 5 minutes.

I opted for the automatic install option but quickly found myself staring at a screen asking for ftp login details ... for which site I have no idea. The download option, on the other hand, just offered me a .zip file ... which didn't help all that much (does that mean reinstall? Upgrade?). Browsing the WordPress documentation on upgrading told me that the Apache user needs to own the WordPress source files (having write access is not enough, it seems) or you'll get the ftp connection details screen when auto-updating. Strange way to present an error to the user, if you ask me. However, as soon as I had changed ownership of the files, the auto upgrade went smooth.

Overall, the install presented more work than it should have, and seemingly for no reason. The process was fairly short, though.

* Ease-of-installation: 4/5
* Instructions given: 3/5
* Confusion level: medium

## Conclusion
Of the four blog systems checked, Nucleus probably was the easiest to install, with the least confusion following. While you may not choose your blog by the installer, you probably wish you had when installing it ... especially in the case of Serendipity. It's something that should not be overlooked, seeing as no one will be able to use your blog software if they can't install it.

It seems that most of the blog packages have realised that there are many things you can do to ease a users first experience with your software: they have realised that it's more important to get the basic stuff set up, let the user use the blog, and then let people make layout and other advanced decisions later. It would be nice to see more use of simple forms with documentation at your fingertips instead of somewhere on the web. And lastly, you definitely need to review your documentation on a regular basis, to avoid confusing people (and you obviously also need to make sure that the download named 'latest' IS IN FACT the latest version of your software).

Apart from Serendipity, that failed miserably, the install process went OK for all the blogs. In general, it's not hard to install a blog and getting it ready for use - especially if you're a knowledged user.

Next time: installing the CMS systems.
