--- 
title: temporarily modifying a ruby attribute
date: 15/03/2010
layout: post
--- 

A usefull little trick mixed into class Object

    class Object
      # === Description
      #
      # Change +attr+ to +val+ within the scope of block
      # and then sets it back again
      #
      def change_attr attr, val, &block
        old_val = instance_variable_get attr
        begin
          instance_variable_set attr, val
          yield
        ensure
          instance_variable_set attr, old_val
        end
      end
    end


Can be used so

    @foo = 30
    change_attr :@foo, 10 do
        @foo.should == 10
    end
    @foo.should == 30

~
