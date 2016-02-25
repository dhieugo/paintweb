One very important place for PaintWeb documentation is the [API reference](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/) which is generated from the source code.

# Wiki pages #

On the [Google Code wiki pages](http://code.google.com/p/paintweb/w/list) of this project you can find:

  * [Integration](Integration.md) - How to integrate PaintWeb into your project. This page includes details on how you can use the PaintWeb script, from the basics, up to the more advanced matter of handling image saving.

  * [Usage in TinyMCE](UsageInTinyMCE.md) - How to use PaintWeb in [TinyMCE](http://tinymce.moxiecode.com).

  * [Moodle integration details.](MoodleIntegration.md)

  * [Building/packaging PaintWeb](BuildingPaintWeb.md) - How to package PaintWeb yourself. This details the usage of the provided Makefile.

  * [PaintWeb code overview](CodeOverview.md) - The page explains you how the PaintWeb code and files are structured.

  * [Extending PaintWeb](ExtendingPaintWeb.md) - How you can extend PaintWeb: new extensions, tools, commands, keyboard shortcuts, application events and more.

  * [TODO](TODO.md) - Ideas for further work on PaintWeb. Read this if you are wondering what might be implemented in the future.

  * [Bugs in web browsers](BrowserBugs.md) which affect PaintWeb.

On other wiki servers you can find:

  * [Paint tool specification](http://docs.moodle.org/en/Development:Paint_tool) at Moodle Docs - presents the Paint tool project for the Moodle GSOC 2009.

  * [Paint tool integration](http://docs.moodle.org/en/Development:Paint_tool_integration) at Moodle Docs - explains all the work on PaintWeb integration inside Moodle.

  * [HTML canvas performance](http://wiki.laptop.org/go/HTML_canvas_performance) at the OLPC wiki - explains the main Canvas performance issue on the OLPC XO laptops, and the work-around implemented in PaintWeb.

# Tutorials #

Here are several tutorials published by [Opera Software](http://www.opera.com), which are strongly related to PaintWeb:

  * [HTML 5 canvas - the basics](http://dev.opera.com/articles/view/html-5-canvas-the-basics/). This article provides an introduction to the basics of using the new Canvas element.

  * [Creating an HTML 5 canvas painting application](http://dev.opera.com/articles/view/html5-canvas-painting/). This article starts from the very beginning of PaintWeb - you will learn how to start the development of a paint tool.

  * [Keyboard accessibility in Web applications](http://dev.opera.com/articles/view/keyboard-accessible-web-applications-1/) is a three-parts article in which I focus on making the basic paint application keyboard accessible. I continue from the final example provided in the previous article I wrote on how to create your own paint application.
    1. [Keyboard shortcuts for tool activation](http://dev.opera.com/articles/view/keyboard-accessible-web-applications-1/) - how to implement keyboard shortcuts per drawing tool.
    1. [Drawing with the keyboard](http://dev.opera.com/articles/view/keyboard-accessible-web-applications-2/) - the implementation of the [MouseKeys extension](http://code.google.com/p/paintweb/source/browse/trunk/src/extensions/mousekeys.js) for PaintWeb. This extension allows users to draw with the keyboard, without any pointing device.
    1. [Cross-browser compatibility](http://dev.opera.com/articles/view/keyboard-accessible-web-applications-3/) - how you can implement a cross-browser compatibility layer for handling DOM keyboard events.

# Other articles #

Here's a list of other articles published about PaintWeb which detail technical aspects. These articles are not documentation per-se, but they provide details otherwise unknown.

  * [PaintWeb performance work did during GSOC 2009](http://www.robodesign.ro/mihai/blog/paintweb-performance).

  * [Canvas text rendering](http://www.robodesign.ro/mihai/blog/canvas-text-rendering). This article details the work I did on the text tool for PaintWeb, and the problems I got into with Opera and older Firefox versions.

  * [PaintWeb packaging results](http://www.robodesign.ro/mihai/blog/api-reference-and-packaging-for-paintweb). This article details the work I did on PaintWeb packaging - it provides the numbers of files and the numbers of kilobytes before and after packaging. It's worth to know why packaging is important.

Further documentation will become available.