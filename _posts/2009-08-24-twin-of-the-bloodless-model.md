---
category: blog
layout: default
published: true
title: Twin of the bloodless model
---
For a couple of weeks now I've been working on refactoring an MVC app in the [BeWelcome](http://www.bewelcome.org) code I'm working on. The reasons behind the refactoring are that 1) it's built on an old part of our codebase (yes, we have several separate parts and yes, it's a nightmare) and 2) it's using some code and database tables that we want to get rid of. Hence, I'm refactoring the app to get it up to date and to remove any references to the problem code.

While doing this, I've had lots of chances to get familiar with the twin of the [bloodless](http://blog.astrumfutura.com/archives/373-The-M-in-MVC-Why-Models-are-Misunderstood-and-Unappreciated.html) [model](http://monstersgotmy.net/post/Anemic-Domain-Model-Anti-Pattern.aspx) [anti-pattern](http://www.martinfowler.com/bliki/AnemicDomainModel.html): the anorectic controller. I'm sure lots of people will not see this as an anti-pattern but if you ask me, it's right up there with the anemic model. If you're looking at your controller and wondering exactly what's going on and where the code and data is, you might just be looking at this.

What I'm objecting to is a controller where the route method is called by the framework only for the controller to then call one method on another object - and this object then pulls data out of requests, sends it here and there and probably does cooking and cleaning too while it's at it. This is not a controller - this is lack of control. It means that the primary contact to the surrounding world in a PHP MVC setup is NOT responsible for the contact to the surrounding. And consequently that you're coupling your OTHER classes (whether the model or, as in this case, the view) to the surrounding world. And whoops, there goes the idea of changing how you send variables to the MVC right along with ease of maintenance.

To me it's simple: the controller is the surrounding worlds point of contact to the MVC; it's also the MVC's point of contact to the surrounding world. There: ONE interface between surrounding world and MVC. Not so hard to locate anymore, is it?

Of course this doesn't mean that you have to suck the blood out of the model to get some meat on your controller. All it means is that you separate things into where they should go: data processing and business logic in the model, control over program flow and contact with surrounding code in the controller, and UI in the view. Don't move program flow control into the view and don't move contact to the surrounding world into the model. Just. Don't.
