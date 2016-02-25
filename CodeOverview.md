# Introduction #

The [PaintWeb source folder](http://code.google.com/p/paintweb/source/browse/trunk/src) is structured as follows:
  * [paintweb.js](http://code.google.com/p/paintweb/source/browse/trunk/src/paintweb.js) holds the main PaintWeb object - this is the entrance file.
  * [includes/json2.js](http://code.google.com/p/paintweb/source/browse/trunk/src/includes/json2.js) which holds a JSON parser and serializer. This is loaded in case the browser does not support JSON natively. The file comes from the official [json.org](http://www.json.org/js.html).
  * [includes/lib.js](http://code.google.com/p/paintweb/source/browse/trunk/src/includes/lib.js) which is the PaintWeb library. More details below.
  * [tools/](http://code.google.com/p/paintweb/source/browse/trunk/src/tools) folder holds each tool with its own implementation code.
  * [interfaces/](http://code.google.com/p/paintweb/source/browse/trunk/src/interfaces) folder holds the interface implementation, together with its own stylesheet and images.
  * [extensions/](http://code.google.com/p/paintweb/source/browse/trunk/src/extensions) folder holds each PaintWeb extension.
  * [lang/](http://code.google.com/p/paintweb/source/browse/trunk/src/lang) folder holds each language file in JSON format.
  * [colors/](http://code.google.com/p/paintweb/source/browse/trunk/src/colors) folder holds color palettes (JSON format) used by the [Color Mixer extension](http://code.google.com/p/paintweb/source/browse/trunk/src/extensions/colormixer.js).

All the code uses jsdoc-formatted comments, two spaces, no tabs, 80 characters line wrapping. You should take a look at the [API reference](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/) while reading this code overview.

It should be noted that some JSON files include comments, which are not allowed technically. This is a "digression" I made for the purpose of providing a well-documented configuration file. PaintWeb strips the comments from such files before using `JSON.parse()`.

# The main code #

The main code contains strictly the PaintWeb object. The initialization code performs the following:

  * it loads the JSON mini-library file, if needed;
  * it loads the configuration file;
  * it loads the language file;

  * it creates the Canvas elements in the document, adds all the needed event listeners, sets up the values for the 2D context properties which are configurable from the JSON file, and some more;

  * it loads and initializes the user interface: the layout, the stylesheet and the script files are all loaded.

  * it loads each tool and extension configured.

The initialization code is able to determine when PaintWeb is packaged. The interface script, the tools and the extensions are all contained in the same script, and no additional loading is performed.

Besides the initialization code, you have API for custom application events, for registering new tools, commands and extensions.

# The PaintWeb library #

The PaintWeb code uses the PaintWeb library. The [lib.js](http://code.google.com/p/paintweb/source/browse/trunk/src/includes/lib.js) adds only one global object: `pwlib`.

The library has the purpose of serving PaintWeb itself. PaintWeb exposes lots of API via the library. For example, the main PaintWeb code uses `pwlib.appEvents` extensively to allow external code to listen for various application events ([toolActivate](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.toolActivate.html), [toolRegister](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.toolRegister.html), etc).

The library holds static methods, properties and some generic objects:

  * [pwlib.browser](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.browser.html) is an object holding information about the current browser (is it opera? is it firefox? etc). Unfortunately, a bit of browser sniffing is required at time to work around browser bugs in PaintWeb.

  * [pwlib.dom.KeyboardEventListener](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.dom.KeyboardEventListener.html) is a class used for better DOM keyboard events support across PaintWeb. This is a cross-browser compatibility layer.

  * [pwlib.appEvent](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.html) and [pwlib.appEvents](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvents.html) provides PaintWeb important functionality for [custom application events](ExtendingPaintWeb#Application_events.md).

  * `pwlib.tools` holds each drawing tool object. PaintWeb creates instances of these objects when needed.

  * `pwlib.extensions` holds each extension object. PaintWeb creates an instance any such object when an extension is registered.

Multiple PaintWeb instances share all the library code.

# PaintWeb internals #

PaintWeb creates two Canvas elements. One element is the "layer" - the image - and the second element is the buffer - which is always on top. Drawing tools render temporarily in the buffer. At the end of the drawing operation, the result is drawn/merged in the layer / the image itself.

Important methods and properties in the main code:

  * **PaintWeb.image** is the object which holds information about the image being edited. Properties: 'width', 'height' and 'zoom'.
  * **PaintWeb.mouse** is the object which holds information about mouse location on the image being edited. Properties: 'x', 'y' and 'buttonDown'. The latter is a boolean which tells you if the mouse left button is down, or not.
  * **PaintWeb.layer** is the object which references the layer Canvas element. Properties: 'canvas' and 'context'.
  * **PaintWeb.buffer** is the object which references the buffer Canvas element. Properties: 'canvas' and 'context'.
  * [PaintWeb#ev\_canvas()](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#ev_canvas) This is the event handler for all the mouse events fired at the Canvas element. This method invokes the event handlers of the currently active drawing tool.
  * [PaintWeb#ev\_keyboard()](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#ev_keyboard) This is the event handler for all the keyboard events. This method invokes any keyboard-related event handler from the currently active drawing tool. If there's no such event handler, then it uses the global keyboard shortcuts configuration to execute commands, to activate tools, to execute methods from extensions, or anything associated to the current keyboard shortcut.

The two methods are the main drivers of user interaction across the entire PaintWeb.