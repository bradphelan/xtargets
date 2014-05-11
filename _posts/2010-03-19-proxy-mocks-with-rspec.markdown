--- 
title: Proxy mocks with RSpec
date: 19/03/2010
layout: post
--- 

Perhapps you want to test a particular method on a class and really like the RSpec mock framework for tracking
the ordering of the method calls for you. However you would really like not to have to provide stubs. You 
really want the methods to execute.
 

    class Fing
      def bar
        puts " bar really called"
      end
      def bing
        puts " bing  really called"
      end
    end

    describe Fing do
      it "is a monkey" do
        b = Fing.new
        b.should_receive!(:bar).once.ordered
        b.should_receive!(:bing).once.ordered
        b.should_receive!(:bar).once.ordered
        b.bar
        b.bing
        b.bar
      end
    end


Well we can monkey patch the Object class just like RSpec does and add a should_receive! method. This
now uses RSpec to track the method calls but also passes the arguments to the real method calls.


	class Object

		class ProxyObject

			attr_accessor :fake_class

			def to_s
				@fake_class.to_s
			end

		end

		def should_receive!(sym, opts={}, &block)

			@__rspec_obj__ ||= ProxyObject.new
			@__rspec_obj__.fake_class = self.class

			m = class<<self;self;end

			sym_old =  "__#{sym}__old__"

			# Generates for sym => :foo
			#
			#   alias :foo_old :foo
			#
			#   def foo *args, &block
			#       foo_old *args, &block
			#       @__rspec_obj__.send :foo, *args, &block
			#   end
			if not respond_to?("#{sym_old}".to_sym)
				m.class_eval <<-EOS, __FILE__, __LINE__
						alias :#{sym_old} :#{sym}

						def #{sym}  *args, &block 
								#{sym_old} *args, &block
								@__rspec_obj__.send :#{sym}, *args, &block
						end
				EOS
			end
			@__rspec_obj__.should_receive(sym, opts, &block)
		end

	end
 
I'm not sure if this is considered heretical in the world of mocks but it was a fun
excercise in metaprogramming.


