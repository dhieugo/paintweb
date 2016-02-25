# Introduction #

During the Google Summer of Code 2009 program the project author has worked on PaintWeb integration into Moodle. The results are that now PaintWeb can be used within TinyMCE and Moodle. For further details check the [project specification](http://docs.moodle.org/en/Development:Paint_tool) and the [GSOC 2009 conclusions](http://www.robodesign.ro/mihai/blog/gsoc-conclusions).

# For users #

Moodle 1.9 integration is almost complete. Given that no major release of 1.9 is coming, there will be only unofficial builds with PaintWeb patches. Stay tuned for more information!

Moodle 2.0 integration is complete. It should be expected that the final Moodle 2.0 release will include PaintWeb by default.

# For developers #

The [ext/moodle](http://code.google.com/p/paintweb/source/browse/trunk/ext/moodle) folder includes the Moodle glue-code. There are scripts for localization integration, a custom PaintWeb configuration file for Moodle, and image save scripts for Moodle 1.9 and 2.0.

Moodle 1.9 does not include TinyMCE 3 by default. There are [patches available for TinyMCE 3 integration into Moodle 1.9](http://repo.or.cz/w/moodle/mihaisucan.git?a=shortlog;h=refs/heads/mdl19-tinymce3). [Patches for PaintWeb integration into Moodle 1.9](http://repo.or.cz/w/moodle/mihaisucan.git?a=shortlog;h=refs/heads/mdl19-paintweb) are also available, but they are not yet complete.

Moodle 2.0 includes TinyMCE 3 by default. The important script which integrates TinyMCE into Moodle is found in **lib/editor/tinymce/lib.php**.

You can get the Moodle 2.0 integration code from a [mdl20-paintweb](http://repo.or.cz/w/moodle/mihaisucan.git?a=shortlog;h=refs/heads/mdl20-paintweb) Git branch.

Technical details:

  * Most of the PaintWeb code can be found in the **lib/paintweb** folder.

  * The **lib/editor/tinymce/lib.php** file tells TinyMCE to load the [TinyMCE plugin provided by PaintWeb](http://code.google.com/p/paintweb/source/browse/trunk/ext/tinymce-plugin/paintweb) (read the [documentation](UsageInTinyMCE.md)). In Moodle, the plugin can be found in **lib/editor/tinymce/plugins/paintweb** and in **lib/paintweb/ext/tinymce-plugin/paintweb**. Only the former folder is used.

  * In the same **lib.php** file PaintWeb is configured to load the Moodle-specific configuration file: [ext/moodle/config.json](http://code.google.com/p/paintweb/source/browse/trunk/ext/moodle/config.json). This file can be found in **lib/paintweb/ext/moodle/**.

  * The [imageDataURLfilter](UsageInTinyMCE#The_image_data_URL_filter.md) is an optional configuration property which allows you as a developer to tell the TinyMCE plugin which server-side script to use when the user tries to edit an image which has a data URL as its source. This is used in Webkit and Firefox 3.0 - there's a problem with images having data URLs being considered as "external insecure resources" (make sure you read the documentation about the configuration property).

  * The configuration JSON file tells PaintWeb to load the [Moodle extension](http://code.google.com/p/paintweb/source/browse/trunk/src/extensions/moodle.js) (read the [API reference](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.extensions.moodle.html)).

  * The [PaintWeb extension for Moodle](http://code.google.com/p/paintweb/source/browse/trunk/src/extensions/moodle.js) uses the [PaintWeb API](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/) to perform image save whenever needed, and to glue with the Moodle server. Here's what it does:
    * It glues into the Moodle forms error handler, such that a Moodle forms error message is displayed when the user tries to submit the form while editing an image with unsaved changes. This is achieved using the **onSubmitUnsaved** TinyMCE plugin configuration callback (read about [how you can use the TinyMCE plugin](UsageInTinyMCE#Use_the_plugin.md) to learn more).
    * It uses the [imageSave](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.imageSave.html) PaintWeb application event to perform the actual image save operation. This uses an XmlHttpRequest to perform a HTTP POST request to the server with the whole image data URL. The code also deals with the server-side result:
    * The image save HTTP request is sent over to the [imagesave19.php](http://code.google.com/p/paintweb/source/browse/trunk/ext/moodle/imagesave19.php) or [imagesave20.php](http://code.google.com/p/paintweb/source/browse/trunk/ext/moodle/imagesave20.php) scripts, depending on the Moodle version being used. These scripts use the Moodle API to store the image on the server.
    * The extension also provides visual integration into Moodle. When the GUI is shown, this extension hides the textarea icons and the textarea format drop-down from the page - these are usually displayed below any textarea in a Moodle page. The [guiShow](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.guiShow.html) and [guiHide](http://www.robodesign.ro/paintweb/trunk/docs/api-ref/symbols/pwlib.appEvent.guiHide.html) PaintWeb application events are used for this purpose.

Overall the idea is we have the PaintWeb plugin for TinyMCE which needs to be "neutral" - we did not want it to be limited to Moodle's use of TinyMCE. Then we have PaintWeb as a project, which we also wanted to be pluggable into other web applications, so it had to be neutral. This is why PaintWeb has an extension specific to Moodle, and a configuration file. The TinyMCE plugin also uses a lot of the new PaintWeb API to make all things work nicely - together with the [TinyMCE API](http://tinymce.moxiecode.com/js/tinymce/docs/api/index.html) as well (obviously).

Make sure you also read about [the paint tool integration work](http://docs.moodle.org/en/Development:Paint_tool_integration) over at the Moodle Docs wiki. The linked wiki page includes further details on the Moodle side of things - check the results sections.