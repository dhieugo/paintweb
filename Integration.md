# Integration #

PaintWeb has a main file intuitively named [paintweb.js](http://code.google.com/p/paintweb/source/browse/trunk/src/paintweb.js). This file holds the PaintWeb object. Whenever you want to start PaintWeb you must create an instance of the PaintWeb object and initialize it like this:

```
<script type="text/javascript" src="paintweb/build/paintweb.js"></script>
```

You need to point to a paintweb.js either from the build folder, or from the src folder. On production servers it's best you point to the build. During development, testing and debugging, you should point to the source folder.

In your document you can start PaintWeb with a simple script like this:

```
<script type="text/javascript">
var pw = new PaintWeb();
pw.config.guiPlaceholder = document.body;

// the path to the file is relative to the base folder
pw.config.configFile = 'your-config.json';

pw.init();
</script>
```

As you can see: you first create the PaintWeb instance. You then configure where you want PaintWeb to insert its own GUI - you must provide a DOM element reference. Lastly, you tell PaintWeb which configuration file to load. You have a [config-example.json](http://code.google.com/p/paintweb/source/browse/trunk/src/config-example.json) file to start with.

All the initialization is performed asynchronously. If you need to know when PaintWeb completes loading you can provide a function to the `init()` method like this:

```
pw.init(function (ev) {
  if (ev.state === PaintWeb.INIT_DONE) {
    alert('PaintWeb loaded successfully!');
  } else if (ev.state === PaintWeb.INIT_ERROR) {
    alert('PaintWeb failed loading!');
  }
});
```

The function you provide is called when there's an error or when the initialization is complete without any problems.

Additionally, if you want to tell PaintWeb to automatically load an image element, such that users edit the image you want from the document, you can do the following:

```
pw.config.imageLoad = imageElement;
pw.init();
```

You just have to provide the DOM image element reference. The PaintWeb Canvas will be initialized to the same dimensions, and the image will be loaded inside the Canvas.

**Note:** Please make sure it's not an image from a different server - that causes security violations.

When you want to load a different image in an existing PaintWeb instance you can do:
```
pw.imageLoad(anotherImageElement);
```
This updates the Canvas to hold the new image. You can call this method only after PaintWeb completes initialization.

It is recommended that you read about [extending PaintWeb](ExtendingPaintWeb.md) to fit your integration needs. You might be interested in using [application events](ExtendingPaintWeb#Application_events.md) for [image save](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/PaintWeb.html#imageSave) and more.

If you want PaintWeb to use a different language, other than English, you can do so easily:

```
// Before you initialize PaintWeb, you set the desired
// language in your PaintWeb instance.
pw.config.lang = 'fr'; // French
pw.init();
```

When you want to integrate PaintWeb in bigger Web applications you might want to load the PaintWeb script dynamically, without using any `<script>` element - you may use an `XmlHttpRequest` to load the file and then perform `eval(xhr.responseText)`. In such cases, the PaintWeb script will not be able to automatically determine its own base folder. After you execute the PaintWeb script you need to tell it where it is located:

```
// Point to the build or src folder,
// depending which PaintWeb version you want to load.
PaintWeb.baseFolder = 'somewhere/paintweb/build';
```

Make sure you update the `baseFolder` property before you initialize a PaintWeb instance.

If you look into [the TinyMCE plugin source](http://code.google.com/p/paintweb/source/browse/trunk/ext/tinymce-plugin/paintweb/editor_plugin_src.js) you will see that PaintWeb is loaded dynamically with the [tinymce.ScriptLoader.load()](http://wiki.moxiecode.com/index.php/TinyMCE:API/tinymce.dom.ScriptLoader) function which uses an `XMLHttpRequest`. In that case, the plugin updates the `PaintWeb.baseFolder` property before initializing an instance of PaintWeb.

# Image save integration #

To save an image from PaintWeb you do:
```
// Save the image using the PNG format.
pw.imageSave();
// Optionally you can also tell which image format you want.
pw.imageSave('image/jpeg');
```

Currently all browsers PaintWeb runs on support saving images as PNG. Firefox 3.5 and Opera 10 also implement support for JPEG.

Image saving is done asyncronous. You must have a custom application event listener for the [imageSave event](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.imageSave.html). Here's the code:

```
var evId = pw.events.add('imageSave', saveHandler);
// ...
// Later you can remove the event handler
pw.events.remove('imageSave', evId);
```

The event handler you provide receives one event object which holds three properties: the `dataURL` of the image, generated by the browser, and the `width` and `height` of the image.

Your event handler should cancel the default action of the `imageSave` event and dispatch the [imageSaveResult event](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.imageSaveResult.html). Your event handler could look like this:

```
// The imageSave event handler.
function saveHandler (ev) {
  // Cancel the default action of the event.
  ev.preventDefault();

  var handlerURL = 'image_save_script.php',
      send       = 'dataURL=' + encodeURIComponent(ev.dataURL),
      headers    = {'Content-Type': 'application/x-www-form-urlencoded'};

  // Send the data URL to a server-side script which stores the image.
  pwlib.xhrLoad(handlerURL, saveReady, 'POST', send, headers);
};

// The XMLHttpRequest onreadystatechange event handler.
function saveReady (xhr) {
  if (!xhr || xhr.readyState !== 4) {
    return;
  }

  if ((xhr.status !== 304 && xhr.status !== 200) ||
      !xhr.responseText || xhr.responseText !== 'OK') {
    // Image save failed.
    pw.events.dispatch(new appEvent.imageSaveResult(false));
    return;
  }

  // Image save was successful.
  pw.events.dispatch(new appEvent.imageSaveResult(true));
};
```

_(The API reference tells you more about the [pwlib.xhrLoad()](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.html#.xhrLoad) method.)_

By dispatching the `imageSaveResult` event you allow other scripts, other event handlers, and other extensions to properly provide visual feedback and/or functionality for when the image save is complete.

You need to write the server-side script which takes the data URL and saves the image on the server. Here's some PHP code to get you started:

```
// The list of allowed image MIME types associated to
// file extensions.
$imgallowedtypes = array(
    'image/png'  => 'png',
    'image/jpeg' => 'jpg'
);

$imgdataurl = &$_POST['dataURL'];

if (empty($imgdataurl)) {
  die('error');
}

// A data URL starts like this:
// data:[<MIME-type>][;charset="<encoding>"][;base64],<data>

// Here we find the comma delimiter.
$comma = strpos($imgdataurl, ',');
if (!$comma) {
  die('error');
}

$imginfo = substr($imgdataurl, 0, $comma);
if (empty($imginfo) || !isset($imgdataurl{($comma+2)})) {
  die('error');
}

// Split by ':' to find the 'data' prefix and the rest of
// the info.
$imginfo = explode(':', $imginfo);

// The array must have exactly two elements and the
// second element must not be empty.
if (count($imginfo) !== 2 || $imginfo[0] !== 'data' ||
    empty($imginfo[1])) {
  die('error');
}

// The MIME type must be given and it must be base64-encoded.
$imginfo = explode(';', $imginfo[1]);

if (count($imginfo) < 2 ||
    !array_key_exists($imginfo[0], $imgallowedtypes) ||
    ($imginfo[1] !== 'base64' && $imginfo[2] !== 'base64')) {
  die('error');
}

$imgdest = 'images/' . sha1($imgdataurl) . '.' .
            $imgallowedtypes[$imginfo[0]];
$imgdataurl = substr($imgdataurl, $comma + 1);

if (!file_put_contents($imgdest, base64_decode($imgdataurl))) {
  die('error');
}

echo 'OK';
```

Obviously, there are some loose ends in the above examples: you should generally pass the image URL the user is editing (`pw.config.imageLoad.src`) to the server-side script, such that the script can determine which file on the server needs to be updated. Additionally, you must work on the security of the script - for example, only allow logged-in users to save images.

That's pretty much all you need to do. Good luck!