gpx-layer
=========

This is a set of scripts for turning a set of GPS tracks in GPX format
into a map tracing layer.

Installation
------------

To start out, get a copy of this repository and of
[datamaps](https://github.com/ericfischer/datamaps):

    $ git clone https://github.com/ericfischer/gpx-layer.git
    $ git clone https://github.com/ericfischer/datamaps.git

If you don't already have it, make sure your system has
libxml-parser-perl for XML parsing and libpng-dev for generating
map tiles

    $ sudo apt-get install libxml-parser-perl libpng-dev

Go into the datamaps directory and compile it:

    $ cd datamaps
    $ make

Then come back here:

    $ cd ../gpx-layer

Extracting points from GPX files
--------------------------------


Making map tiles
----------------


Adding your tiles as a tracing layer in iD
------------------------------------------
