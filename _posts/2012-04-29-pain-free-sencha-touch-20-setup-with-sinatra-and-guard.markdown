--- 
title: Pain free sencha touch 2.0 setup with sinatra and guard
date: 29/04/2012
layout: post
--- 

Setting up a dencent workflow with sencha touch is not too complex especially
if you pull out the ruby toolbelt. I'll show you how to do it and add in
coffeescript for a bonus.

~

First download the sencha touch packages and install as per instructions. If you
are using ZSH then it probably won't work for you. Add this or similar to your
zshrc

```
export PATH=/Applications/SenchaSDKTools-2.0.0-Beta:$PATH
export PATH=/Applications/SenchaSDKTools-2.0.0-Beta/bin:$PATH
export PATH=/Applications/SenchaSDKTools-2.0.0-Beta/jsbuilder:$PATH
```

Create a new sencha project and add it to your favorite version control
system. I use git. I would skip the sdk directory that is auto generated
as it is full of win32 binaries that you may not want clogging your
version control server.

Now create a Gemfile for use with bundler

```ruby
source 'http://rubygems.org'
gem "sinatra"
group :development do
  gem "compass"
  gem "guard"
  gem "guard-coffeescript"
  gem "guard-compass", :git => 'git@github.com:bradphelan/guard-compass.git'
  gem "growl"
end
```

Do

    bundle install

and all your gems will be installed. 

Sinatra will help us to create a simple web server. No need for apache! Create the
following ruby file call *app.rb* in the project root directory.

    require 'sinatra'
    require "sinatra/reloader" if development?

    # Make sure our assets reload on every request.
    set :static_cache_control, [:public, :max_age => 0]

    # Pick which set of files get served depending
    # on our environment
    case ENV["RACK_ENV"] || "development"
    when "production"
      puts "Production"
      set :public_folder, "./build/production"
    when "testing"
      puts "Production"
      set :public_folder, "./build/testing"
    else "development"
      puts "Debug"
      set :public_folder, "./"
    end

    get "/" do
      redirect "/index.html"
    end

You will need a rake file for common command like starting the server
and building releases. This is a simple starter to put in file **Rakefile**
in the project root directory.

    namespace :build do
      desc "Build the production code"
      task :production do
        sh 'sencha app build -e production'
      end

      desc "Build the testing code"
      task :testing do
        sh 'sencha app build -e testing'
      end

    end

    namespace :serve do

      desc "Run the production code"
      task :production do
        ENV['RACK_ENV']='production'
        sh 'ruby app.rb'
      end

      desc "Run the testing code"
      task :testing do
        ENV['RACK_ENV']='testing'
        sh 'ruby app.rb'
      end

      desc "Run the development code"
      task :development do
        ENV['RACK_ENV']='development'
        sh 'ruby app.rb'
      end
    end


Now you will want to fiddle with the standard CSS of sencha or add your own
custom styles. Sencha uses SASS so that is why we have the compass gem
installed. We will use that next. Create a file called **Guardfile** in the
project root directory and put this in it

    require 'guard/guard'

    guard 'coffeescript' do
      watch %r{^app/.+\.coffee$}
      watch %r{^app.coffee$}
    end

    config = File.expand_path "../resources/sass/config.rb", __FILE__
    path = File.expand_path "../..", config

    guard 'compass', :project_path => path,  :configuration_file => config do
      watch %r{resources/.+\.scss}
    end

You can then from the command line run

    bundle exec guard

and your app.css will always be up to date. There is also a section in
my guard file for compiling my coffeescript to javascript. Coffeescript
is a great alternative to javascript and works perfectly with Sencha.

Now to start your server you just do

    rake server:development

and you will see something like

    rake serve:production (in /Users/bradphelan/workspace/xxxxx)
    ruby app.rb
    Production
    [2012-04-29 21:44:04] INFO  WEBrick 1.3.1
    [2012-04-29 21:44:04] INFO  ruby 1.9.2 (2011-02-18) [x86_64-darwin10.8.0]
    == Sinatra/1.3.2 has taken the stage on 4567 for production with backup from WEBrick
    [2012-04-29 21:44:04] INFO  WEBrick::HTTPServer#start: pid=84278 port=4567

and you can point your browser / iDevice / fondlePad / android thingie at

    http://localhost:4567/index.html

and your app is served. And now you have a sinatra backend you can add
custom routes to do backend processing of the sencha data stores. If 
you are interested in me writing a post on how to do that then tweet
me and I'll write that up next.

And to to build your production release which packages all your
js code into a single minified file do

    rake build:production

That package in **app/build/production** is what is served when
you start the server in production mode and is the default when
served from somewhere like heroku.


