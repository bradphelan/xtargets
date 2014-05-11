--- 
layout: post
title: MVVM with Backbone.js
date: 07/07/2012
--- 

I became intruiged with knockout.js recently as I remember my frustration
with creating bidirectional bindings between backbone view elements
and the models. It turns out there is a very nice solution via Bart Woods
[ModelBinder](https://github.com/theironcook/Backbone.ModelBinder) plugin
for [Backbone](http://backbonejs.org/).

I patched together some classes to make a more declarative style to ModelBinder
and it collection binder helpers. It's just a small experiement but the 
results are summarized below.

~

Here are my models and collections

    class Person extends Backbone.Model
      first_name: "brad"
      last_name: "phelan"

    class PersonCollection extends Backbone.Collection
      model: Person

    # Collection instance with some pre-filled data
    collection = new PersonCollection [
        first_name: 'brad'
        last_name: 'phelan'
    ,
        first_name: 'sally'
        last_name: 'winterbottom'
    ]

Here is my template

    .backbond_model_binding_version
      %h1 Backbone Model Binding Example
      .header
        .new_person
          %h3 New Person
          %div
            %label First Name
            %input.first_name
          %div
            %label Last Name
            %input.last_name
          %button.add New
      .body
        .collection
          %script(type="text/html")
            .person
              .inner(data-name="cid")
                %hr
                %div
                  %label First Name
                  %input(data-name="first_name")
                %div
                  %label Last Name
                  %input(data-name="last_name")
                %div
                  %button.delete Delete


and here is the coffeescript to bind to it

    # Backbone Model Binding Version
    class BBMBNewPersonView extends Backbone.BoundView

      modelBindings:
        first_name: 'input.first_name'
        last_name: 'input.last_name'

      events:
        "click button.add": "onAdd"

      onAdd: ->
        collection.add @model
        @setModel(new Person())


    # Collection View
    class BBMBPersonCollectionView extends Backbone.BoundCollectionView

      modelBindings: "data-name"

      initialize: (options)->
        options.template = @$('script').text()
        super(options)

      events:
        "click button.delete": "onDelete"

      onDelete: (event)->
        @getModelForEl(event.target).destroy()


and here is the setup when you are ready to put
it on the screen

    $(document).ready ->

      # ##################################
      # Backbone model binding setup
      # ##################################
      new BBMBNewPersonView
        model: new Person()
        el: $(".backbond_model_binding_version .new_person")

      new BBMBPersonCollectionView
        collection: collection
        el: $(".backbond_model_binding_version .collection")


You will note that all the model bindings are done declaratively
in the coffeescript *not* in the html/haml template where it
can get ugly fast.

My summary is that if you want knockout.js style magic but
like backbone then you can have both.

The full source code to my example including a knockout version can be found in
this [jsFiddle](http://jsfiddle.net/KRwHv/3/)




