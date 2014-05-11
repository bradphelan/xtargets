--- 
title: Binding model attributes to form elements with backbone js
date: 11/06/2011
--- 

If you are familiar with [backbone js](http://documentcloud.github.com/backbone/#View) then you know the
events declaration for views.


    class FooView extends Backbone.View
      
      tag: "div"

      events:
        
        "click .button" : "doButton"

      doButton: ->

        alert("Button Clicked. I rock!")

      render: ->

        $(@el).html """
          <div class="button"> click me </div>
        """

        super

        @

However when tying the model's attributes to view elements we can use
a similar trick.

    class FooView extends MyView

      tag: "div"

      modelBindings:

        "change form input.address" : "address"
        "change form input.name"    : "name"
        "change form input.email"   : "email"
      
      render: ->

        $(@el).html """
          <form>
            <input class="address"/>
            <input class="name"/>
            <input class="email"/>
          </form>
        """

        super

        @


    # Instantiate the view 
    view = new FooView
      model: new Backbone.Model

    $("body").html(view.el) 

~
      

No messy manual handling of events. Totally declarative binding. We only need
to extend the render method and  add the following code to our View base class.


    class MyView extends Backbone.View

      render: ->

        if @model != null
          # Iterate through all bindings
          for selector, field of @modelBindings
            do (selector, field) =>
              console.log "binding #{selector} to #{field}"
              # When the model changes update the form
              # elements
              @model.bind "change:#{field}", (model, val)=>
                console.log "model[#{field}] => #{selector}"
                @$(selector).val(val)

              # When the form changes update the model
              [event, selector...] = selector.split(" ")
              selector = selector.join(" ")
              @$(selector).bind event, (ev)=>
                console.log "form[#{selector}] => #{field}"
                data = {}
                data[field] = @$(ev.target).val()
                @model.set data

              # Set the initial value of the form
              # elements
              @$(selector).val(@model.get(field))

        super
         
        @


Now you have declarative form element binding for backbone. It might
not handle all complex use cases but for the basic stuff it cut's
down the boiler plate quite a bit.


