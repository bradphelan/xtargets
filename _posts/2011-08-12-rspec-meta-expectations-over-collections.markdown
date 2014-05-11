--- 
title: RSpec meta expectations over collections
date: 12/08/2011
layout: post
--- 

Found myself writing specs like

    describe "1..10" do

        before :each do
            @array = [1,2,3,4,5,6,7,8,9]
        end

        it "should have every item as numeric" do
            @array.each {|i|
                i.should be_kind_of(Numeric)
            }
        end


    end

And I didn't like it. I wanted

    describe "1..10" do

        before :each do
            @array = [1,2,3,4,5,6,7,8,9]
        end

        it "should have every item as numeric" do
            @array.should each be_kind_of(Numeric)
        end

    end

and here is the solution and it is very simple.

    RSpec::Matchers.define :each do |meta|
      match do |actual|
        actual.each_with_index do |i, j|
          @elem = j
          i.should meta
        end
      end

      failure_message_for_should do |actual|
        "at[#{@elem}] #{meta.failure_message_for_should}"
      end
    end

and some specs to demonstrate

    describe "passing" do
      it "should be a number" do
        (1..10).should each be_kind_of(Numeric)
      end
    end

    describe "failing" do
      it "should not be a string" do
        [1,2,3,4,"cow",6,"7"].should each be_kind_of(Numeric)
      end
    end

    describe "failing again" do
      subject{[1,2,3,4,"cow",6,"7"]}
      it{should each be_kind_of(Numeric)}
    end


enjoy

