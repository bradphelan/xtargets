--- 
title: Rails 3.1 Asset pipeline rocks!
date: 29/05/2011
layout: post
--- 

app/assets/javascripts/m/crazy.js.coffee.erb

    class <%= "CRAZY" %>
      <%(1..10).each do |p|%>
      method_<%=p%>: ->
        <%=p%>
      <%end%>


automagically ends up as


    (function() {
      var CRAZY;
      CRAZY = (function() {
        function CRAZY() {}
        CRAZY.prototype.method_1 = function() {
          return 1;
        };
        CRAZY.prototype.method_2 = function() {
          return 2;
        };
        CRAZY.prototype.method_3 = function() {
          return 3;
        };
        CRAZY.prototype.method_4 = function() {
          return 4;
        };
        CRAZY.prototype.method_5 = function() {
          return 5;
        };
        CRAZY.prototype.method_6 = function() {
          return 6;
        };
        CRAZY.prototype.method_7 = function() {
          return 7;
        };
        CRAZY.prototype.method_8 = function() {
          return 8;
        };
        CRAZY.prototype.method_9 = function() {
          return 9;
        };
        CRAZY.prototype.method_10 = function() {
          return 10;
        };
        return CRAZY;
      })();
    }).call(this);

 
Note the cascade of mime types  crazy.js.coffee.erb. I'm going to use
this to introspect my ActiveRecord classes and generate backbone.js
classes with all boiler plate stuff filled in.

~


