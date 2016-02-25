This wiki page lists bug reports that have been published in the bug trackers of the Web browsers with which PaintWeb has been tested. This list might not be complete, and some of the bugs might be fixed already.

Some of the bugs below have affected PaintWeb or still affect the project.

Gecko:

  * [461766](https://bugzilla.mozilla.org/show_bug.cgi?id=461766): Very slow appendChild on Linux. This bug affects the color mixer palettes tab.
  * [422911](https://bugzilla.mozilla.org/show_bug.cgi?id=422911): canvas.2dcontext.lineWidth must ignore values <=0.
  * [401795](https://bugzilla.mozilla.org/show_bug.cgi?id=401795): Canvas toDataURL does not accept excess arguments. This bug affects image saving in PaintWeb.
  * [478445](https://bugzilla.mozilla.org/show_bug.cgi?id=478445): canvas arc function produces different results when strokeText function is called afterward. This bug did affect PaintWeb during development.
  * [519400](https://bugzilla.mozilla.org/show_bug.cgi?id=519400): Speed up Canvas's getImageData(). This performance issue affects PaintWeb, especially the Color bucket tool.

Webkit:

  * [29125](https://bugs.webkit.org/show_bug.cgi?id=29125): Images cannot be selected in documents with designMode enabled. This bug affects the TinyMCE plugin provided by this project.
  * [28976](https://bugs.webkit.org/show_bug.cgi?id=28976): element.hasAttribute() fails to return true for nodes imported from XML documents. This bug did affect PaintWeb during development - now the code works around this issue.
  * [29305](https://bugs.webkit.org/show_bug.cgi?id=29305): Canvas drawn with data URL image raises SECURITY\_ERR when toDataUrl() called. This bug affects PaintWeb until web browsers will upgrade to Webkit rev 48556 or newer which has fixed the bug.

Google Chrome:

  * [11153](http://code.google.com/p/chromium/issues/detail?id=11153): Canvas Shadows are not working with drawImage. This bug affects PaintWeb.

Opera:

  * Canvas Text API is unsupported by Opera.
  * Canvas Shadows are unsupported by Opera.
  * DSK-266224: Drawing corruption with Canvas coming from an XMLHttpRequest (see [test case](http://www.robodesign.ro/coding/0066/)). This affected PaintWeb during development.
  * DSK-265135: invoking queryCommandState causes image selection to be lost when an element is added into the document during a mouse event (see [test case](http://www.robodesign.ro/coding/0064/)). This bug affects the TinyMCE plugin provided by this project.
  * DSK-256817: Canvas drawImage(local svgDocument) causes security violation (see [test case](http://www.robodesign.ro/coding/0058)). This bug affected PaintWeb during development. I tried to workaround the lack of a Canvas Text API - the attempt failed due to this bug.
  * DSK-256711: Canvas drawImage with SVG causes memory leaks (see [test case](http://www.robodesign.ro/coding/0057/)). This bug affected PaintWeb during development.
  * DSK-238151: Pressing the Enter key does not fire the change event on inputs (see [test case](http://www.robodesign.ro/coding/0052/)). This bug affects PaintWeb.
  * DSK-234886: Crash/freeze with huge canvas when using get/putImageData (see [case](test.md)). This bug affected PaintWeb in older Opera versions.
  * DSK-232264: The mouseup event gives wrong offsetX/Y values (see [test case](http://www.robodesign.ro/coding/0049/)). This bug affected PaintWeb during development.
  * DSK-217538: elem.style.cursor not applied soon enough (see [test case](http://www.robodesign.ro/coding/0044/)). This bug affects PaintWeb.
  * DSK-217075: Opera does not allow to set elem.style.display = null (see [test case](http://www.robodesign.ro/coding/0042/)). This cross-browser compatibility bug affected PaintWeb during development.