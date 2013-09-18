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

The next step is to extract tracks from your GPX files and index
them by location. If you keep your files in <code>~/gpx</code>,
index them into a new datamaps directory called <code>gpx.dm</code>
by doing

    $ find ~/gpx -name '*.gpx' -print0 | xargs -0 ./parse-gpx | ../datamaps/encode -z20 -m8 -o gpx.dm

If you keep your GPX files in a different directory, use the name of that
directory instead.

If you already have an old <code>gpx.dm</code> directory, you'll first need to

    $ rm -r gpx.dm

to get rid of it before you can index new tracks into it.

Making map tiles
----------------


Adding your tiles as a tracing layer in iD
------------------------------------------
