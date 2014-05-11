--- 
title: Using with_scope and ActiveRecord extension to set join model attributes
date: 29/02/2012
--- 

 
I have the following 


    class User < ActiveRecord::Base
      has_many :user_venue_managements, :dependent => :destroy

      has_many :venues, :through => :user_venue_managements do
        def create *params, &block
          UserVenueManagement.with_scope :create => { :role => :owner } do
            super *params, &block
          end
        end
        def create! *params, &block
          UserVenueManagement.with_scope :create => { :role => :owner } do
            super *params, &block
          end
        end
      end
    end

I can now do

    u = User.first
    u.venues.create! :address => "New York"

will create me a venue with the correct role set on the join model. Executed
SQL is

    INSERT INTO "venues" ("address") VALUES (?)  [["address", "New York]]
    INSERT INTO "user_venue_managements" ("role", "user_id", "venue_id") VALUES (?, ?, ?)  
       [["role", :owner], ["user_id", 1], ["venue_id", 148]]

I'm a little unhappy with the duplicate create and create! repititon but it
works.

  
