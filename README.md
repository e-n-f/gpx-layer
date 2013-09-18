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
libxml-parser-perl for XML parsing and libpng-dev and pngquant
for generating map tiles

    $ sudo apt-get install libxml-parser-perl libpng-dev pngquant

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

The <code>-z20</code> option means to index with enough precision to
draw map tiles at zoom level 20. The </code>-m8</code> option means
that there are 8 bits of metadata (in this case, the direction that
the GPS is moving) associated with each entry in the file.

If you keep your GPX files in a directory other than <code>~/gpx</code>,
use the name of that directory instead.

If you already have an old <code>gpx.dm</code> directory, you'll first need to

    $ rm -r gpx.dm

to get rid of it before you can index new tracks into it.

Making map tiles
----------------

Then it is time to make map tiles out of the data. You can do this with

    $ ../datamaps/enumerate -z18 gpx.dm | xargs -L1 -P8 ./make-tile

This will take a few minutes if you have a few million points in your GPX files,
or hours and hours if you have billions.

If you don't want to go all the way to zoom level 18, you can use a different number
instead of <code>-z18</code>.  
If your computer has more than 8 processors, you can use a different number
instead of <code>-P8</code>.

Trying out your map tiles
-------------------------

The previous step will have placed the map tiles into a directory called
<code>tiles/gpx.dm</code>.

If you the directory you are working in is accessible from a web browser,
there is a file called <code>test.html</code> that is already set up to
show <code>tiles/gpx.dm</code> in a web map. Otherwise, you'll need to
copy the <code>tiles</code> directory to a web server to try it out.

Adding your tiles as a tracing layer in iD
------------------------------------------
