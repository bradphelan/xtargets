--- 
title: Example rails3 app using backbone.js, coffeescript and jquery mobile ui
date: 07/04/2011
layout: post
--- 

In a previous post I noted some work I was doing on a rails3 app for mobile to help
me learn German noun genders. I've decided to make the project public as it is a nice
little case study in using

- [rails3](http://rubyonrails.org/)
- [coffescript](http://jashkenas.github.com/coffee-script/)
- [backbone.js](http://documentcloud.github.com/backbone/)
- [jquery mobile ui](http://jquerymobile.com/)

all in one app.

Originally the client code was done in pure javascript but I wanted to learn
coffeescript and being a ruby / python programmer I was very impressed with
the improvements made to vanilla javascript.

Note the following magic.

    @game_engine.bind 'change:word', => @changeWord()
    @game_engine.bind 'change:correct_answer', => @renderMessage()

becomes in javascript

    this.game_engine.bind('change:word', __bind(function() {
      return this.changeWord();
    }, this));
    return this.game_engine.bind('change:correct_answer', __bind(function() {
      return this.renderMessage();
    }, this));

what is happening is that the *=>* operater generates a function which when
called will be bound to the this from the enclosing scope bypassing the
annoying feature often in js where *this* is bound to the DOM element
triggering the callback.

You will also note that @foo is expanded to this.foo. Minor improvement but
cuts down on the keystrokes.




You can find the githib repository at [github](https://github.com/bradphelan/ohmyderdiedas). The application
is live at [ohmyderdieas](http://ohmyderdiesdas.heroku.com)

~~
