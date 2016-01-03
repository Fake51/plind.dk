---
category: blog
layout: default
published: true
title: Comparing CMS/blog systems, part 3.1 - TYPO3 day to day
---

The individual posting on the blogs and cms' will be bigger, given that there's simply more to do than for installing. Hence, I've decided to split the posts into smaller chunks, dealing with one product at a time. On top of that, the ordering has changed a tad - I wanted to start with the blogs again but as I'm working on a site for PL PHP and thought that I would do that in [Typo3](http://www.typo3.org/), it was a nobrainer to do this blog post alongside the new site. So, I'm reversing the order, taking the CMS systems first, then the blogs - one by one. Oh, and, just to completely change the idea, I've reverted to using PHP 5.2 instead of 5.3 - typo3 cannot handle 5.3 and some of the other systems weren't too happy with it either, so to keep the list systems I'm using 5.2.

## Designing
Before I started implementing the site in Typo3 I made a design for the site. I had a browse at [OSWD](http://www.oswd.org/) (sadly inactive - it's a great resource) and found a design that would work well for what I had in mind. I didn't feel like just grabbing the design (a perfectly valid option) but wanted some minute changes - so I started out with GIMP, got some graphics together, then quickly mocked up an HTML template frame.

In terms of HTML and CSS it's a pretty easy layout, everything is wrapped in a div for easy styling and separation.

## Hands dirty
So, with the HTML template in hand, I started with Typo3. Unfortunately, I didn't have any proper docs on how to go about things, so I started off thinking that it would be easy to get a site working, that Typo3 must be setup to easily do what it's meant to do, and that I would be able to google what I couldn't figure out on my own. Quite a bit of time later I had learned that Typo3 necessitates fairly detailed docs, is not created with any defaults in mind, and generally makes tasks much more complex than they need be. There are many interesting and cool parts to the system, but they are hard to find for the uninitiated, it seems.

Instead of just ranting, time to show how I went about things. The first step, I thought, was that of making a template. Typo3 is a CMS: content management system. You manage content, displaying it within templates. So, of course, you make a template, then make some content, then display your content in templates, right? No. You cannot make templates in the system before you've created pages. So, step 1 is to create your first page in the system, then you can go on and make a template for that page.

So, having gotten this far, I then dived into creating my site template from scratch. Now, more seasoned Typo3 users/developers would probably stop here and ask: why didn't you just install the TemplaVoila! extension? Or import your HTML template using the tools for that? Well, I wanted to learn how Typo3 actually works. Also, a system that only works if you add plugins/extensions is simply not a working system - it's a skeleton with no functionality. That aside, getting started with TypoScript wasn't hard in any way. The idea of the script is pretty straight forward, the syntax the same and if you look at some examples you should be able to pick up fundamentals fairly easy.

When starting with templates, you first create the template, give it a name and set options. You really only need to set the name to be able to edit it - but there is one other **very important part** to do, which unfortunately is not a default or even pointed out in the template creator. This important part is to include a static template that gives you access to content from pages and/or other places. Without this static template there's a fair chance you'll get nothing back when trying to insert content in your template. Now, there's probably a good reason why one of the static templates giving you access to content isn't by default included - it's most likely because there are several templates that do so and the creators of Typo3 don't want to make choices on behalf of users. Nonetheless, it seems to me that if you need one of these static templates in more than 66% of cases, then you at the very least make a big, fat, red arrow that points to this option. Normally, I'd think it best to include something by default if it's used a lot, and then let people deselect it if it's not used - unfortunately, that's not really an option for Typo3, as some templates exclude others.

Cutting this short: if you're making a site like me, styling it with CSS (i.e. no tables to position things) you most likely want to include the 'css_styled_content' template. This then gives you access to content in pages through a simple TypoScript copy: element < styles.content.get.

After setting up the basic stuff, you can go on to creating the template structure. This is done through mainly two parts of the template editor: constants and setup. Constants are, as the name implies, constants. These can be included in the setup, which is where the template is actually made. The upshot of this is that you can change a constant in one place and see changes in many, i.e. doing away with code duplication. Where this comes in handy is mainly when you're using one template in many places - no need to change template code, you just set slightly different constants for each page.

My first try at creating my site template in Typo3 was fairly crude. It looked something like this:

```
page = PAGE
page.bodyTag = <body>
page.stylesheet = fileadmin/styles/main.css

page {
  10 = HTML
  10.value = <div id='main_frame'>
  11 = HTML
  11.value = <div id='header'>
  12 = HTML
  12.value = <div id='logo'>
  13 = TEXT
  13.value = PL PHP
  13.wrap = <div id='logo_text'>|</div>
  14 = HTML
  14.value = </div>
... etc
}
```

Yes, at first I only knew about three elements in TypoScript: PAGE, TEXT and HTML. I was pretty sure there was a better way to do what I wanted but I find it's a better way to learn to first give things a try, then improve your first idea. So, using the above method I got most of the template working. I then took some time out to study TypoScript a bit more, learned about a few more elements and rewrote the template to:

```
page = PAGE
page.bodyTag = <body>
page.stylesheet = fileadmin/styles/main.css

page {
 10 = COA
 10.wrap = <div id="main_frame">|</div>
 10 {
  10 = COA
  10.wrap = <div id='header'>|</div>
  10 {
   10 = COA
   10.wrap = <div id='logo'>|</div>
   10 {
    10 = TEXT
    10.value = <a href='/'>PL PHP</a>
    10.wrap = <div id='logo_text'>|</div>
   }
   20 = TEXT
   20.value =
   20.wrap = <div id='menu'>|</div>
   30 = TEXT
   30.wrap = <div class='float-clear'>|</div>
  }
  20 < styles.content.get
  20.wrap = <div id='content'>|</div>
  30 = TEXT
  30.value (
   &amp;copy; 2009 Peter Lind<span class='splitter'>&amp;nbsp;</span>PL
   PHP<span class='splitter'>&amp;nbsp;</span>
  )
  30.wrap = <div id='footer'>|</div>
 }
}
```

The advantage in the above should be clear: elements are no longer split - instead, elements are contained within each other as in the actual HTML.

TypoScript is basically a collection of objects that are typically modeled on HTML or PHP counterparts. The TEXT and HTML elements, for instance, resolve to text outputs - the difference between the two is that the properties you set on the TEXT element will be set on it's stdWrap property (a TEXT element is close to being a stdWrap element) while the same doesn't go for the HTML element. Will this affect you when starting out with Typo3? Not very likely ...

Anyway, a quick run through: first, a PAGE element is declared. This represents the entire page (don't confuse this with the page you created for the template. The only elements of the page created that will be inserted automatically into the template are those that affect the <head> - so, the PAGE element doesn't import any content automatically) and you can set various properties such as stylesheet and bodyTag to make basic layout decisions. To get further structure to the template, you then need to add elements to the PAGE element. This is done in the above code with the HTML, TEXT and COA elements. The COA element is an array of cObjects - content objects. Better still, it allows you to wrap the contents as you wish - hence, you can see it as an HTML element that allows for a hierarchy (the HTML element cannot contain other elements).

Three other elements worth mentioning in the code are: first, the notation:

* = is simple assignment
* < means copy
* =< means reference (not used above)
* > means empty the element (not used above. Basic usage is if you're using an element already declared)
* {} serves to reduce typing. Left hand elements inside the block are treated as properties of the element to the left of the {
* () serves to contain multiple lines. You cannot have anything to the right of the ( and you cannot have anything to the left or right of the )

Secondly, the styles.content.get part. This refers, as one might guess from my rant above, to the included static template. If one was to use the template analyzer, one would see that css_styled_content appears as a template extension to the main template. In this extension styles.content.get is declared and it's properties filled. In your own template you can then copy the contents of styles.content.get with < - and the content defined in the page of the template will appear inside the template when rendered.

Lastly, as can be guessed from the numbering in the template, elements are rendered according to numerical position. That is, 10 is rendered before 20, which in turn is rendered before 30. Elements are resolved in full before later elements are resolved: 10.10 is resolved before 10.20, regardless of where 10.20 is declared.

Finally, to get the template working properly for the site, a menu is needed. This bit can cause a bit of confusion, mainly because the elements are more complex than other TypoScript elements. What's needed is an HMENU element and then either TMENU or GMENU element - the first would be Text Menu, the second Graphical Menu. As I'm opting for a css based menu and handling most things myself, I opted for the TMENU. The menu ended up looking like this:

```
10 = HMENU
10.entryLevel = 0
10 {
 1 = TMENU
 1 {
  NO {
   after = <span class='splitter'>&amp;nbsp;</span>
  }
  ACT < .NO
  ACT {
   ATagParams = class='active-menupoint'
  }
  ACT = 1
 }
}
```

The HMENU gets things started. The entryLevel bit sets the start level of the menu to just root level (which, strangely, means that root level pages should NOT be included - entryLevel seems to mean 'include things below this level'). The next part determines the layout of the following menus - which means that the numerical order here takes on a different sense than in the other rendering parts, as here each successive level describes a menu sublevel. Hence, HMENU.1 is menu level 1, HMENU.2 is menu level 2. For each menu level described, a number of properties must be set for them to render. For instance, the NO property of the TMENU element must be set for the menuitems to render.

Luckily, that's about all it takes to get a menu going, no need to include templates or extensions - just have to use the proper elements, declare them properly and then have a proper page hierarchy (the reason I use 'proper' so many times in a row is that I didn't ... chose to learn the hard way, I did).

Now, the templating part is done. It's time to skip to content creating, which is much easier (or harder, depending upon how creative you are) in Typo3. The backend site includes an ok JavaScript rich text editor that will do all the typical formatting tricks of the day, so there's not really much to say regarding that: it works and it works quite easy.

Then, to stick some icing on the cake, nice urls would be good. To achieve this, it's necessary to install an extension and mess about with the .htaccess file in the main site folder. Going by the instructions for the [Realurl extension](http://typo3.org/documentation/document-library/extension-manuals/realurl/1.7.0/view/) it's not too bad though - compared to getting content displaying on the page in the first place it was actually a breeze. There is one pitfall to be aware of - you need to download the extension, then upload it to your site. The 'discover' feature of Typo3 doesn't work, it imports an old version of the extension instead of the newest one. Apart from that, the extension works pretty well soon as you have edited your template (the setup part needs three lines) and your .htaccess file - you can even specify custom names for pretty urls.

## Done
Getting a basic site up and running to a point where I can look at it and say "that's alright" took a lot longer than expected. Some parts of Typo3 are overly complex for no apparent reason. Others seem to work fine and don't need any tweaking. The moral of the story seems to be that you shouldn't go into Typo3 land without a map and a guide - you probably won't find your way around.

Here's the list of things I used to get by:

* [Documentation at Typo3](http://typo3.org/documentation/document-library/) - You can find tutorials, videos and more here. It could be a great wealth of info but unfortunately it's not. A lot of the data is outdated, a lot of it is not detailed enough and a lot of it simply assumes to much of the reader. Still, you'll find answers to some questions here.
* The [TSref](http://typo3.org/documentation/document-library/core-documentation/doc_core_tsref/current/) - documentation of TypoScript elements and other stuff. Beware of the examples: they seem to make points but generally don't.
* [Typo3 Enterprise Management](http://www.amazon.co.uk/Typo3-Enterprise-Management-Rene-Fritz/dp/1904811418/ref=sr_1_1?ie=UTF8&amp;s=books&amp;qid=1260793050&amp;sr=8-1) - probably the best guide you can find, although it's a couple of years old.

One thing I'm rather undecided about is the whole idea of TypoScript. I came across the idea that PHP frameworks are not just a bonus - they can be problematic as well. Rasmus Lerdorf [voiced that idea and similar ones one](http://toys.lerdorf.com/archives/38-The-no-framework-PHP-MVC-framework.html) his blog a while ago: his point being that PHP in itself actually works as a framework and that if you stick a framework on top that, you're distancing yourself from what you actually need. TypoScript gives you the same feeling: why has HTML been recreated in this way? Why not just allow actual HTML as templates and mix in some PHP? What are the problems solved with TypoScript? What do we gain from representing templates as TypoScripts? This concern is amplified by the fact that extensions/plugins have been developed to take your HTMLand turn it into a Typo3 template: if TypoScript solves my problems why was TemplaVoila! created?

Some positive sides to it should be visible: templates won't be the typical mash of HTML and PHP - they'll be a unique type that encompasses both. This might make it easier to do some PHP without knowing the language fully. It can also simply writing HTML as some things will be done for you. Still, I wonder: do we need another set of building blocks on top of HTML and PHP?
