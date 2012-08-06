node-gallery
============

NodeJS Photo Gallery. Feed it a directory of photos, get back a JSON object & a styled photo gallery ready for the web. 
* No database needed
* Folders titles = Album titles
* Image titles = Photo Titles
* EXIF title = Description
* Displays camera capture info (exposure, aperture, ISO..)
* Optional album description, album thumbnail by info.json

Installation
============

    $ npm install node-gallery

Examples
===================
A usage example using node-gallery with Express can be found in app.js. It should be simple to use with a framework of your choice - EJS templates are included.

Photos
===================
Photos are added to your specified 'directory', in the example "photos". Albums are created for every folder we encounter, and thumbnails are automatically generated by using the first image we come across in this album.
An optional info.json file can be supplied in an album to supply:
*Description* Plaintext album description, can contain HTML
*Thumbnail* Override the default thumbnail we find for this album. Specified relative to the current album, so if it's a photo in the album 'image.jpg', and if it's in a child album 'childAlbumFolderName/image.jpg'
*Name* Override the default album name we obtain from the directory. Won't reflect in the breadcrumb for now because of the way it's constructed - hoping to change this soon!
    {
      "description" : "Album <strongDescription</strong>.",
      "thumbnail" : "North%20Coast/_MG_0925.jpg",
      name: "Some Overriding Name"
    }

Usage (ExpressJS)
===================
    /*
     * Tested using Express 3.0 alpha - full example in 'app.js'
     */

    // Setup our app to use gallery middleware - also does init
    app.configure(function(){
      app.use(gallery.middleware({static: 'resources', directory: '/photos', rootURL: "/gallery"}));
    });

    // now, our middleware does gallery lookups for every URL under rootURL - e.g. /gallery
    app.get('/gallery*', function(req, res){
      // We automatically have the gallery data available to us in req thanks to middleware
      var data = req.gallery;
      // and we can res.render using one of the supplied templates (photo.ejs/album.ejs) or one of our own
      res.render(data.type + '.ejs', data);
    });


Usage (Standalone)
===================

    gallery.init({
      static: 'resources',
      directory: '/photos',
      rootURL: '/gallery'
    });

    /*
     * Now we can use gallery.getPhoto or gallery.getAlbum to pass web requests through to node-gallery
     */
    gallery.getAlbum({
          album: 'Ireland/Co. Waterford',
        }, function(err, album){
          // photo contains a JSON object representing our album.
          // Now, we'd pass this photo to ejs.render('views/album.ejs', album);
    });

    gallery.getPhoto({
      album: 'Ireland/Co. Waterford',
      photo: 'IMG_2040.jpg'
    }, function(err, photo){
      // photo contains a JSON object representing our photo.
      // Now, we'd pass this photo to ejs.render('views/photo.ejs', photo);
    });


Tests
============
Tests are written in raw javascript. To run,

    $ npm test
