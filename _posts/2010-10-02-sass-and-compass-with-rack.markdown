--- 
title: SASS and Compass with Rack
date: 02/10/2010
layout: post
--- 

As it took me a little while to collect the information on how to do this perhapps
I should publish the details. It's not complicated and all the code is around abouts
anyway but a fully worked example is the easiest.

If you want to run SASS and Compass on a pure Rack application running on Heroku you
have to initialize everything manually. No magic Rails generators to help you. As
well with Heroku you have the added complexity that the public directory of your
application is read only. 

Heroku provides you with a *tmp* directory off the root of your project. It's no
good for permanent file storage as it may or may not be wiped on the next request.
However as SASS files are perfect for caching which Heroku does with Varnish, all
we need is to generate them once and they should be cached.

Here is the relevant part of my config.ru

require 'sass/plugin/rack'
use Sass::Plugin::Rack

    require "fileutils"

    # Create some directories off tmp that SASS will require.
    cssdir = File.expand_path("../tmp/stylesheets/compiled", __FILE__)
    cachedir = File.expand_path("../tmp/sass-cache", __FILE__)
    FileUtils.mkdir_p(cssdir)
    FileUtils.mkdir_p(cachedir)

    # Tell Rack where to pick up the compiled stylesheets
    use Rack::Static, :urls => ["/stylesheets/compiled"], :root => "tmp"

    # Configure SASS where to compile the stylesheets
    Sass::Plugin.options[:css_location] = cssdir
    Sass::Plugin.options[:cache_location] = cachedir
    Sass::Plugin.options[:template_location] = File.expand_path("../sass", __FILE__) 

    # Setup compass. Calling the below Rails specific code seems to
    # be good enough. Not sure if there is a better way.
    require 'compass'
    Compass::AppIntegration::Rails.initialize!


