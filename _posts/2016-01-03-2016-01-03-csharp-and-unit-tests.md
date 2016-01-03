---
layout: default
category: blog
splash: ""
tags: null
published: true
title: "C# and Unit tests"
---

Starting out with Csharp on Linux, using MonoDevelop and trying to do it the proper way, which means: TDD/BDD. The easiest way to do this seems to be using Nunit for unit tests, so here's how to go about getting started with that.

## Step one
Get monodevelop installed. Consider installing mono-complete as well, just for good measure. And get monodevelop-nunit as well, as you'll need it for the tests.

```
sudo apt-get install monodevelop mono-complete monodevelop-nunit
```

## Step two
Create a new C# solution, of the type you want. For a start console projects aren't bad.

Then, in the solution pane, right click the solution (the top-level item) and choose Add -> New project. This time, choose the NUnit -> NUnit Library Project.

## Step three
Profit! Or, well, you now have your first unit test setup for your project. There's a lot of info available about [Nunit](http://www.nunit.org/) and on top of that there are books for reading - I'm currently on [The Art of Unit Testing](http://artofunittesting.com/) by Ray Osherove, which looks quite decent.