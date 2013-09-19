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
draw map tiles at zoom level 20. The <code>-m8</code> option means
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

    $ ../datamaps/enumerate -s -z18 gpx.dm | xargs -L1 -P8 ./make-tile

This will take a few minutes if you have a few million points in your GPX files,
or hours and hours if you have billions.

If you don't want to go all the way to zoom level 18 (or if you want to go
even deeper), you can use a different number instead of <code>-z18</code>.
If your computer has more than 8 processors, you can use a different number
instead of <code>-P8</code>.

Trying out your map tiles
-------------------------

The previous step will have placed the map tiles into a directory called
<code>tiles/gpx.dm</code>.

If you the directory you are working in is accessible from a web browser,
there is a file called <code>test.html</code> that is already set up to
show <code>tiles/gpx.dm</code> in a web map. (You will probably need to
scroll and zoom to see anything, since it doesn't know where your GPS tracks
are located and is probably pointed to the wrong part of the earth.)

Otherwise, you'll need to
copy the <code>tiles</code> directory to a web server to try it out.

Adding your tiles as a tracing layer in iD
------------------------------------------

To use your tiles in iD, they really need to be on a web server,
and you need to make a copy of iD on the web server (or some other
web server) too.

On my home machine, the root directory of the web server is /var/www,
so I copied my <code>tiles</code> directory into a directory there
called <code>tracing</code> with

    $ sudo -s
    # rsync -vax tiles /var/www/tracing

Then I also checked out a copy of iD in that directory:

    # cd /var/www/tracing
    # git clone https://github.com/systemed/iD.git

The tricky part is that you then have to edit iD's
<code>data/imagery.json</code> file to include your new layer.

The simplest thing is to add it like this at the start, right
after the opening bracket:

    {
        "name": "GPX overlay",
        "template": "/tracing/tiles/gpx.dm/{z}/{x}/{y}.png",
        "overlay": true,
        "scaleExtent": [ 0, 18 ],
        "extent": [ [ -180.00, -90.00 ], [ 180.00, 90.00 ] ]
    },

even though this overstates the area where the layer is relevant.
Be sure to include the trailing comma after the closing brace.
It's easy to leave off by accident.

You can then open up your modified copy of iD
(http://localhost/tracing/iD or wherever you put it instead)
in a web browser and turn your new layer on
in the Layers popup at the right:

<a href="http://www.flickr.com/photos/walkingsf/9807568194"><img src="http://farm4.staticflickr.com/3684/9807568194_ea603fb247_o.png"></a>

Looking for missing streets
---------------------------

The layer you turn on last draws on top of the other layers,
so one nice thing this enables is looking for streets that
are missing from the map but are in the GPS logs.

Turn the Locator Overlay off and back on again, and then it
looks like this:

<a href="http://www.flickr.com/photos/walkingsf/9807625623/"><img src="http://farm8.staticflickr.com/7357/9807625623_4d42349da9_o.png"></a>

with only the GPS tracks that don't quite correspond to
streets showing through. In this case, the map of San Francisco
is pretty complete, so all that shows through is
the ferry route to Sausalito, the Exploratorium pier,
and some noise downtown from bad GPS reception.

Using the layer this way is usually practical from zooms 12 to 15,
where it is zoomed in enough that the Locator Overlay shows all
the streets instead of only the major ones, but not zoomed in enough
that iD switches from display to editing.
