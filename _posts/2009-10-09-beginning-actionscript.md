---
category: blog
layout: default
published: true
title: Beginning actionscript
---
Set off by a job interview I got, I've started learning [actionscript](http://en.wikipedia.org/wiki/Actionscript). It's not something I've dealt with before apart from decompiling flash scripts to debug them. There's been some obstacles before I could really dig into it - the first being getting an actionscript compiler installed on Ubuntu. As always, though, someone else had already had the same problem, solved it, and [blogged about it](http://www.williambrownstreet.net/wordpress/?p=78). turns out all you have to do is [grab the Adobe Flex SDK](http://opensource.adobe.com/wiki/display/flexsdk/Download+Flex+3), unzip, chmod the mxmlc file and you're set on your way.

Of course, some people demand an IDE and it turns out that Adobe has been heeding their calls: you can get the Adobe Flex Builder as a plugin for Eclipse. Getting that to work on Ubuntu 9.04 was a bit more work than the above, but again: [others](http://www.insideria.com/2008/04/step-by-step-setting-up-flex-b.html) [have](http://kbala.com/2009/03/install-adobe-flex-builder-linux-alpha-in-ubuntu/) [already](http://blog.tripu.info/item/flex-linux) [solved](http://www.markvandenbergh.com/archives/83/installing-flex-builder-on-ubuntu-904/) that problem. Get a Java runtime environment, get the newest build of Eclipse (the one in the Ubuntu default sources is too old), [get the plugin from Adobe](http://labs.adobe.com/downloads/flexbuilder_linux.html).

After that, I could start having a look at the language, and it is indeed obvious that it's based on EcmaScript: it's fairly straightforward if you know JavaScript, which is obviously a plus for me. There are some big differences from the start, though, such as the strange need to package things ... but allowing for stuff outside the package in files. Also, if only one class is allowed inside a package, there's no need whatsoever to enforce naming to be the same as the file containing it - whatever the public class inside the package in the file, THAT'S what is instantiated.

I'm guessing that whoever designed ActionScript liked a lot of JavaScript but was scared witless by all the weak typing and the passing around of functions, etc., so strong typing was (almost) enforced on all variables (the actionscript compiler will complain about missing types but will compile nonetheless ...) as well as classes and what have you.

So far, the best part about actionscript is the ease with which you'll get going. And the worst part is my lack of a debugger (can't blame the language on that though). On that note, I find it rather strange that Flex Builder provides me with rubbish error output whereas running the command line compiler pinpoints the problems.

Anyway, here's my first try at writing something in actionscript: [line-drawing with random colors](/downloads/line_drawing.swf) [flash file].

And here's the code for it:

```
package
{
    import flash.display.Sprite;
    import flash.events.Event;
    import flash.events.MouseEvent;

    public class drawing extends Sprite
    {
        private var _mouse_moving:Boolean;
        private var _line:Line;

        public function drawing()
        {
            stage.addEventListener(Event.ENTER_FRAME, init);
        }

        private function init(event:Event):void
        {
            stage.removeEventListener(Event.ENTER_FRAME, init);
            stage.addEventListener(MouseEvent.MOUSE_DOWN, mouseDown);
            stage.addEventListener(MouseEvent.MOUSE_UP, mouseUp);
            stage.addEventListener(MouseEvent.MOUSE_MOVE, mouseMove);
        }

        public function mouseMove(event:MouseEvent):void
        {
            if (_mouse_moving == true)
            {
                _line.drawLine(event.stageX, event.stageY);
            }
        }

        public function mouseDown(event:MouseEvent):void
        {
            _mouse_moving = true;
            var color:int = uint(((Math.random() * 255) << 16) + ((Math.random() * 255) << 8) + ((Math.random() * 255))) 
            _line = new Line(color, 2, event.stageX, event.stageY);
            addChild(_line);
        }

        public function mouseUp(event:MouseEvent):void
        {
            _mouse_moving = false;
        }
    }
}

import flash.display.Sprite;
import flash.display.Shape;
import flash.events.Event;

class Line extends Shape
{
    private var _color:int;
    private var _thickness:int;
    private var _start_x:int;
    private var _start_y:int;

    public function Line(color:int, line_thickness:int, start_x:int, start_y:int)
    {
        _color = color;
        _thickness = line_thickness;
        _start_x = start_x;
        _start_y = start_y;
    }

    public function drawLine(end_x:int, end_y:int):void
    {
        graphics.clear();

        graphics.lineStyle(_thickness, _color);
        graphics.moveTo(_start_x, _start_y);
        graphics.lineTo(end_x, end_y);
    } 
}
```
