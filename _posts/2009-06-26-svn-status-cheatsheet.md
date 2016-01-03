---
published: true
category: blog
layout: default
---
Recently I was looking for info on status messages from SVN and while googling for 'svn status cheatsheet' did turn up quite a few results, they were all useless: none of them went beyond the status messages you get in the first row from SVN when doing svn st. Seeing as I was up against 's' and 'uu' that didn't help. The [SVN manual](http://svnbook.red-bean.com/en/1.5) didn't prove very helpful either, at first - the status messages are spread out through the manual and finding the ones I needed took me very long. Hence, for future reference:

| **First column - file added or changed**                      |                                                                                                  |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
|                                                               | no changes                                                                                       |
| A                                                             | item will be added                                                                               |
| C                                                             | item has conflicts                                                                               |
| D                                                             | item will be deleted                                                                             |
| E                                                             | file existed before the update                                                                   |
| M                                                             | item has been modified                                                                           |
| R                                                             | item has been replaced                                                                           |
| X                                                             | item is present due to an externals definition                                                   |
| ?                                                             | item is not in repository                                                                        |
| I                                                             | item is being ignored                                                                            |
| !                                                             | item is missing                                                                                  |
| ~                                                             | item is versioned as one thing but replaced by another                                           |
| **Second column - properties changed**                        |                                                                                                  |
|                                                               | no changes                                                                                       |
| M                                                             | properties have changed                                                                          |
| C                                                             | properties for the item is in conflict with those in repository                                  |
| **Third column - locks**                                      |                                                                                                  |
|                                                               | no locks                                                                                         |
| L                                                             | item is locked                                                                                   |
| **Fourth column - for addition-with-history scheduled items** |                                                                                                  |
|                                                               | no history scheduled with commit                                                                 |
| +                                                             | history scheduled with commit                                                                    |
| **Fifth column - switching relative to parents**              |                                                                                                  |
|                                                               | item is child of its parent directory                                                            |
| S                                                             | item is switched                                                                                 |
| **Sixth column - lock information**                           |                                                                                                  |
|                                                               | item is not locked                                                                               |
| K                                                             | item is locked in this working copy                                                              |
| O                                                             | item is locked by another user or in another working copy                                        |
| T                                                             | item was locked in this working copy but the lock is invalid. Item is still locked in repository |
| B                                                             | item was locked in this working copy but the lock is invalid. Item is no longer locked           |
| **Seventh column - out-of-date information**                  |                                                                                                  |
|                                                               | item is uptodate                                                                                 |
| *                                                             | newer version of item exists in repository                                                       |

Most of this stuff won't show unless you do 'svn st --show-updates' or 'svn st -v' but it's very useful to know from time to time.
