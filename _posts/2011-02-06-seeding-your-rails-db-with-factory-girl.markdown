--- 
title: Seeding your rails db with Factory Girl
date: 06/02/2011
layout: post
--- 

Assuming you are using factory girl!!

In your db/seeds.rb file add

    require 'factory_girl'
    Dir[Rails.root.join("spec/factories/*.rb")].each {|f| require f}

    10.times do
      Factory.create :user
    end

This assumes a factory file spec/factories/*.rb
 
    Factory.sequence :email do |n|
      "somebody#{n}@example.com"
    end

    Factory.define :user do |f|
      f.email { Factory.next :email }
      f.password 'testing'
      f.password_confirmation 'testing'
    end

then

    rake db:seed 

will fill you db with goodness straight from the factory.
    

