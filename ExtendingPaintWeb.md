# Introduction #

You can extend PaintWeb in several ways:

  * new commands;
  * new extensions;
  * new drawing tools;
  * application events;
  * new user interface layout, style and/or script;

You pick what suits your needs.

**Note:** In this guide we will refer to the PaintWeb instance object as `pw`.

# New commands #

Commands are functions you associate to an ID. You can have a command invoked from a keyboard shortcut, or by a GUI element. Here's how you can [register a command](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#commandRegister):

```
var func = function () { alert('hello world!'); };
pw.commandRegister('alerter', func);
```

That's all! You only tell the command ID, "alerter", and the function itself. You can later [unregister the command](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#commandUnregister) using:

```
pw.commandUnregister('alerter');
```

To have the command invoked by a keyboard shortcut you can update your configuration JSON file like this:

```
"keys": {
  // Use "command": "id" to execute some command.
  "Control J": { "command": "alerter" },
  // ...
},
// ...
```

You only need to add the keyboard shortcut and the command ID. It's that easy!

# New extensions #

Extensions are more than simple commands. You can have completely features added to PaintWeb. Here's the way you can create a new extension:

```
// Create the extension object
pwlib.extensions.fooExt = function (app) {
  // app is a reference to the PaintWeb instance which has registered the extension.

  this.extensionRegister = function () {
    // if you return false, the extension is not registered.
    // ...
  };
  this.extensionUnregister = function () {
    // ...
  };
  // ...
};

pw.extensionRegister('fooExt');
```

You create your extension object in the `pwlib.extensions` object. You register the extension in the PaintWeb instance you want using the [extensionRegister()](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#extensionRegister) method.

Your extension object can have methods for the register and unregister events. If you return false in the extensionRegister() method, the extension is not registered.

The first argument received by the extension constructor is a reference to the PaintWeb instance object. This is needed when you want to have the same extension running in multiple instances.

In your extension you can do pretty much whatever you want: listen for custom application events, add event handlers, change functionality, add new tools and more. You should take a look at the two extensions provided in PaintWeb: the [Color Mixer](http://code.google.com/p/paintweb/source/browse/trunk/src/extensions/colormixer.js) and the [MouseKeys](http://code.google.com/p/paintweb/source/browse/trunk/src/extensions/mousekeys.js) extensions.

The extension object instance is stored in [pw.extensions](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#extensions). So, for each PaintWeb instance you can access the unique instance of your extension, something like `pw.extensions.fooExt`.

# New drawing tools #

To implement a new drawing tool you do:

```
// add the tool constructor function to pwlib.tools
pwlib.tools.rectangle = function (app) {
  this.preActivate = function () {
    // ...
  };

  this.activate = function () {
    // ...
  };

  this.mousedown = function () {
    // ...
  };
  // ...
};

pw.toolRegister('rectangle');
```

You add all the new drawing tools to the `pwlib.tools` object. This is used to create a certain level of consistency: all drawing tools live in a single place. You register the tool with the [toolRegister()](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#toolRegister) method.

The `tool.preActivate()` method is invoked, if available, before the tool is activated in PaintWeb. You can return false to cancel the tool activation if you have good reasons to do so.

The `tool.activate()` method is invoked, if available, once the tool is activated, after the previous tool has been deactivated.

Any drawing tool is useless without the mouse event handlers. You can choose which events you are listening for: mousedown, mousemove, mouseup, click, dblclick, and contextmenu.

To draw you need to access the image buffer to draw on it, and then merge the change to the image. Here's a minimal example:

```
pwlib.tools.rectangle = function (app) {
  var context = app.buffer.context,
      image   = app.image,
      mouse   = app.mouse;

  // The start coordinates.
  var x0, y0;

  this.mousedown = function () {
    x0 = mouse.x;
    y0 = mouse.y;

    return true;
  };

  this.mousemove = function () {
    if (!mouse.buttonDown) {
      return;
    }

    context.clearRect(0, 0, image.width, image.height);

    var x = Math.min(mouse.x,  x0),
        y = Math.min(mouse.y,  y0),
        w = Math.abs(mouse.x - x0),
        h = Math.abs(mouse.y - y0);

    if (!w || !h) {
      return;
    }

    context.strokeRect(x, y, w, h);
  };

  this.mouseup = function () {
    app.layerUpdate();
    return true;
  };

  this.keydown = function (ev) {
    if (!mouse.buttonDown || ev.kid_ != 'Escape') {
      return false;
    }

    context.clearRect(0, 0, image.width, image.height);
    mouse.buttonDown = false;
    return true;
  };
};
```

That's a minimal implementation of a rectangle tool. Here we see that the mouse coordinates and button state come from `pw.mouse`. Additionally, the image dimensions are stored in `pw.image`. The buffer context is given by `pw.buffer.context`. Similarly, you can access the image layer using `pw.layer.context`.

When the drawing operation is complete, you invoke the [layerUpdate()](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#layerUpdate) method. This merges the drawing from the buffer context onto the layer context. The image is also stored in history, such that the user can undo the drawing operation.

The `keydown()` event handler uses the event argument object to check the keyboard the user pressed. You should read the API reference about the PaintWeb [ev\_keyboard()](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#ev_keyboard) method and about the [pwlib.dom.KeyboardEventListener](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.dom.KeyboardEventListener.html) - these tell you which properties are added to the DOM Event object for keyboard shortcuts.

Make sure you checkout the code of the available [tools](http://code.google.com/p/paintweb/source/browse/trunk/src/tools).

# Application events #

The PaintWeb library includes some functionality for custom application events. In a nutshell, here's how you can use it:

```
// create a new event object.
pwlib.appEvent.flowerSizeChange = function (flower, size, sizeOld) {
  this.flower = flower;
  this.size = size;
  this.sizeOld = sizeOld;

  // inherit the base application event
  pwlib.appEvent.call(this, 'flowerSizeChange', true);
};
```

We add a new `appEvent` object, which sets a few properties, and inherits more from the [pwlib.appEvent](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.html) object. This is your new custom application event.

The [pwlib.appEvents](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvents.html) object is used for "managing" custom events. Here's how:

```
// Create the custom events manager.
// You always have to tell the event target - we use 'this' here.
var events = new pwlib.appEvents(this);

// We can add event listeners just by telling the event type we want
// to listen for and the event handler function.
var eventId = events.add('flowerSizeChange', function (ev) {
  // the object has ev.type === 'flowerSizeChange'
  if (ev.size > 5 && ev.flower !== 'lily') {
    ev.preventDefault();
  }
});
```

The events manager stores the event handler. When an event of the same type is dispatched, then the event handler is invoked. To remove an event listener you must use the event ID provided by the [events.add()](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvents.html#add) method. For the example above:

```
events.remove('flowerSizeChange', eventId);
```

To dispatch an event you do the following:
```
// Create an event instance and then dispatch it to the target you want.
var ev = new pwlib.appEvent.flowerSizeChange('test', 2, 1),
    cancel = events.dispatch(ev);

// You can check if the event.preventDefault() method was invoked or not
// by some event handler during event propagation.
if (!cancel) {
  // ...
}
```

The point of all of the above is that PaintWeb uses such custom events to aid you extend and integrate PaintWeb into your projects. Any PaintWeb instance object has the [events object](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#events), say `pw.events`.

In the PaintWeb API reference documentation you can see which methods dispatch which events. Here's an example: the [imageSizeChange](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.imageSizeChange.html) event is dispatched by the [imageCrop()](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#imageCrop) method. From your external code you can monitor the image dimensions using:

```
pw.events.add('imageSizeChange', function (ev) {
  console.log(ev.width, ev.height);
  // do something else ...
});
```

There are other objects in PaintWeb which use this custom event handling mechanism, for example the [guiResizer](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.guiResizer.html) object - a reusable component for resizable elements in the GUI.

Again, [the API reference](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/) is your friend for finding all the nitty gritty details through the entire codebase.

# Customisable user interface #

You can edit the user interface stylesheet, the interface layout and the script. From the PaintWeb configuration file you can point to different files, so you can work on new ones without breaking any of the existing code on your system.

There's one aspect important to the interface layout: it uses custom attributes, which allow you to make almost any element a custom GUI component. For example, you can do:

```
<p data-pwCommand="imageSave">Save image</p>
```
To quickly associate any command you want with an element. There are other attributes as well:

```
<p data-pwTool="selection">Selection</p>
```

That allows you to associate an element to a tool. Additionally, you can associate elements to any configuration property. Here's an example:

```
<input data-pwConfig="line.lineWidth" type="number" min="1" max="100" value="1">
```

That associates the input of type number to the `line.lineWidth` configuration property.

For more details about this, please read the API reference about the [interface layout parser](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.gui.html#initParseMarkup). Also, check out the [interface layout](http://code.google.com/p/paintweb/source/browse/trunk/src/interfaces/default/layout.xhtml) code itself.

The user [interface script](http://code.google.com/p/paintweb/source/browse/trunk/src/interfaces/default/script.js) makes important use of the main application events.