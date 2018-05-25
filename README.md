Heroku buildpack: FFMpeg
=======================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for using [ffmpegthumbnailer](https://github.com/dirkvdb/ffmpegthumbnailer) in your project.  
It doesn't do anything else, so to actually compile your app you should use [heroku-buildpack-multi](https://github.com/ddollar/heroku-buildpack-multi) to combine it with a real buildpack.

Tested on cedar-14.

Usage
-----
To use this buildpack, you should prepare .buildpacks file that contains this buildpack url and your real buildpack url.  

    $ ls
    .buildpacks
    ...
    
    $ cat .buildpacks
    https://github.com/heroku/heroku-buildpack-python.git
    https://github.com/beyond12/heroku-buildpack-ffmpegthumbnailer.git

    $ heroku create --buildpack https://github.com/ddollar/heroku-buildpack-multi.git

    $ git push heroku master
    ...

You can verify installation of ffmpegthumbnailer by issuing the following command.

    $ heroku run "ffmpegthumbnailer -version"

Testing
-------
Run tests using the [Buildpack testrunner](https://github.com/heroku/heroku-buildpack-testrunner)

Updating
--------
To eliminate the need to compile ffmpeg on every push, this buildpack downloads a custom-built tarball of libraries and binaries.

To rebuild or update this tarball, you need to compile ffmpeg into a build directory from a dyno on your stack, compile ffmpegthumbnailer into the same directory, and then package it.

```# Install ffmpeg
./configure --enable-shared --disable-asm --prefix=/app/vendor/build
make && make install

# Install ffmpegthumbnailer
PKG_CONFIG_PATH=/app/vendor/build/lib/pkgconfig/ cmake \
-DCMAKE_BUILD_TYPE=Release \
-DENABLE_GIO=ON \
-DENABLE_THUMBNAILER=ON \
-DCMAKE_INSTALL_PREFIX:PATH=/app/vendor/build .
make && make install
cd /app/vendor/build
tar czvf ffmpegthumbnailer.tar.gz lib bin```


