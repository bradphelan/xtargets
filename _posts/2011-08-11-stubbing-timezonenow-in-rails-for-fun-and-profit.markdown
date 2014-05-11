--- 
title: Stubbing Time.zone.now in Rails for fun and profit
date: 11/08/2011
--- 

Having lots of queries in my project that depend on Time.now and
having Time.now scattered around my code was starting to cause me
testing/bdding/specing headaches. RSpec stubbing to the rescue! With
the below helper you can code like Marty Mcfly.

Throw this file in spec/support/time_helper.rb

    require 'rspec/mocks'

    class Time
      DEFAULT_STOP_ZONE = ActiveSupport::TimeZone["UTC"]
      def Time.stop
        RSpec::Mocks::setup(self)
        @stopped_time = DEFAULT_STOP_ZONE.now
        Time.zone = DEFAULT_STOP_ZONE
        Time.zone.stub!(:now).and_return {
          @stopped_time
        }
        Time.stub!(:now).and_return {@stopped_time}
      end

      def Time.advance duration
        raise "You have not stopped time yet McFly!" unless @stopped_time
        @stopped_time = @stopped_time.since duration
      end

      def Time.regress duration
        raise "You have not stopped time yet McFly!" unless @stopped_time
        @stopped_time = @stopped_time.since -duration
      end
    end

Then in your spec can write things like below

    describe Foo do
        before :each do

            Time.stop

            (1..10).each do |i|
                Factory :foo, :created_at => Time.now.ago(i.hours)
            end

            (1..10).each do |i|
                Factory :foo, :created_at => Time.now.since(i.hours)
            end

        end

        it "should have 10 elements created in the future" do
            Foo.where{created_at > my{Time.now}}.count.should == 10
        end

        it "should have 10 elements created in the past" do
            Foo.where{created_at < my{Time.now}}.count.should == 10
        end

        describe "advancing time" do
            before :each do
                Time.advance 1.5.hours
            end

            it "should have 9 elements created in the future" do
                Foo.where{created_at > my{Time.now}}.count.should == 10
            end

            it "should have 11 elements created in the past" do
                Foo.where{created_at < my{Time.now}}.count.should == 10
            end

        end

        describe "regressing time" do
            before :each do
                Time.regress 1.5.hours
            end

            it "should have 11 elements created in the future" do
                Foo.where{created_at > my{Time.now}}.count.should == 10
            end

            it "should have 9 elements created in the past" do
                Foo.where{created_at < my{Time.now}}.count.should == 10
            end

        end

    end 



The solution is good as long as you don't care about absolute time. Sometimes
you will need to set the current absoute time. You can do this through the
stopped_time accessor monkey patched into Time.
