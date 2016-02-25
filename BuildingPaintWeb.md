# Introduction #

You need to be able to execute Makefiles (see [GNU Make](http://www.gnu.org/software/make/)). PaintWeb has a Makefile which drives the packaging process.

Prerequisites:
  * PHP 5+ which is used for some scripts;
  * bash to execute other scripts;
  * [yuicompressor](http://developer.yahoo.com/yui/compressor/);
  * [jsdoc-toolkit](http://jsdoctoolkit.org/) for the API reference documentation;
  * Java is needed to run the yuicompressor and the jsdoc-toolkit scripts.

Typically, on a Linux system you can easily install PHP 5, Java and you already have GNU make and bash.

# Get the code #

Check the [Downloads](http://code.google.com/p/paintweb/downloads/list) section for a release of PaintWeb. [Snapshot builds](http://www.robodesign.ro/paintweb/snapshots/) are also available.

Or, you can download the source code from the SVN repository.
```
svn checkout http://paintweb.googlecode.com/svn/trunk/ paintweb-read-only
```

# Configure make #

First invoke:
```
make config
```
then edit the new config-local.mk file. Make sure that:

  * BIN\_PHP points to your PHP binary.
  * FOLDER\_YUIC points to your YUI Compressor unpacked folder.
  * FOLDER\_JSDOCT points to your jsdoc-toolkit unpacked folder.

Now you are ready!

# Running make #

Once you changed the code you want, you can repackage PaintWeb. You can do this running make in the folder you checked-out PaintWeb:
```
make
```

This will generate the PaintWeb build folder. There you will have the minified paintweb.js. The paintweb.src.js file is there for debugging purposes.

To generate the API reference documentation run:
```
make docs
```

Generating the entire API reference takes a lot of time, so please be patient.

To have the archive, you do:
```
make release
```

That gives you the paintweb-0.9.tar.bz2 file. The version number is determined automatically.

You can also make a snapshot:
```
make snapshot
```
Snapshots have the build date included. For example paintweb-0.9-20090703.tar.bz2.

If you are interested in making a build of PaintWeb for usage with Moodle 1.9 or 2.0, then you can do:
```
make moodle19
or
make moodle20
```
This will make sure that the [Moodle extension for PaintWeb](http://code.google.com/p/paintweb/source/browse/trunk/src/extensions/moodle.js) is included in the package.

For development purposes, you can also generate a tags file for the whole PaintWeb source code. Simple invoke:
```
make tags
```
You will get a `tags` file inside the PaintWeb folder, which is useful when you are doing development.

That's about all. Enjoy!