--- 
title: Tableless views with active record
date: 02/08/2011
--- 

ActiveRecord is great. It is awsome. The new ActiveRelation support for chaining
and query building is amazing. However ActiveRecord is table bound. This means
you cannot create models that do not exist as tables. Why would you want to do 
this?

~

For example say we have two simple models

    class GameAward < ActiveRecord::Base

        attr_accessible :points, :date

        has_many :tags


    class Tag < ActiveRecord::Base

        attr_accessible :name

        belongs_to :game_award

and we have an associated table called game_awards. We would like to create
a query that returns the number of points each tag is associated with. This
would be an SQL statement something like

    select sum(points) as points, tag.name as name from game_awards 
    join tags on 
        tags.game_awards_id = game_awards.id
    group by
        tags.name

That's all very well if we were into writing SQL by hand but we are
much too hip for that. And wqe have another problem. What model should
the above query be associated with. Is it a Tag model or is it
a GameAward model. I say it is neither. It is the result of joining
and transforming the data from two other tables. 

We need a third model. Let's call that Stats

    class Stat < ActiveRecord::Base

At this point ActiveRecord will puke because there is no table called
Stats. We need to trick ActiveRecord into letting us play in the sandbox
without bringing our own bucket and spades. We will create a wrapper
called VirtualRecord that provides the shims we need. First of all 
how might we use such a VirtualRecord in our Stats class

    class Stat < VirtualRecord
        column :points, :integer
        column :name,   :string
        
        def self.find_all
            select{
                [sum(game_awards.points).as points, tags.name as name]
            }.from{game_awards}.join{tags}.group{tags.name}
        end

    end

We would need to define some query to return all the records from the database.
I have done the above with SQUEEL

    https://github.com/ernie/squeel

an excellent query builder DSL. Check it out! The find_all method is hooked
by the VirtualRecord class to provide a default_scope that when you call

    Stat.all

you generate the above query. It is ActiveRelation friendly and can be chained.
The implementation of the VirtualRecord is

    # requires the squeel gem
    #
    # https://github.com/ernie/squeel
    # 
    # Inherit from this class to create tableless
    # models that are backed by a query.
    #
    # For example
    #
    # class Award < ActiveRecord::Base
    #   has_many :award_type_view
    # end
    #
    # class AwardTypeView < VirtualRecord
    #   column :award_type, :string
    #   column :award_id, :integer
    #
    #   belongs_to :award
    #
    #   def self.find_all
    #     select{[awards.type.as(award_type), awards.id.as(award_id)]}.from("awards")
    #   end
    # end
    #
    # The cool thing is, is that the relations work on both directions
    #
    # a = AwardTypeView.first
    #
    #   SELECT "award_type_views".* FROM 
    #   (SELECT "awards"."type" AS award_type, "awards"."id" AS award_id FROM awards ) 
    #   award_type_views LIMIT 1
    #
    # a.award
    #
    #   SELECT "awards".* FROM "awards" WHERE "awards"."id" = 1 LIMIT 1
    #
    # b = Award.first
    #
    #   SELECT "awards".* FROM "awards" LIMIT 1
    #
    # b.award_type_views
    #
    #   SELECT "award_type_views".* FROM 
    #   (SELECT "awards"."type" AS award_type, "awards"."id" AS award_id FROM awards ) 
    #   award_type_views 
    #   WHERE "award_type_views"."award_id" = 1
    class VirtualRecord < ActiveRecord::Base
      def self.columns() 
        @columns ||= [] 
      end  

      def self.columns_hash()
        @columns_hash ||= {}
      end

      def self.find_all
        raise "please override"
      end

      def self.inherited klass 
        default_scope do
          table = klass.to_s.underscore.pluralize
          q = klass.find_all.arel.as table
          select{}.from(q)
        end
      end

      def self.column(name, sql_type = :string, default = nil, null = true)  
        column = ActiveRecord::ConnectionAdapters::Column.new(name.to_s, default, sql_type.to_s, null)  

        columns << column
        columns_hash[name.to_s] = column   
      end  

      self.abstract_class = true

      def self.table_name
        to_s.underscore.pluralize
      end

      def readonly?
        true
      end

    end

