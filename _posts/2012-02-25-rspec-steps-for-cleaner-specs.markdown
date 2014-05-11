--- 
title: RSpec steps for cleaner specs
date: 25/02/2012
--- 

The following RSpec idiom drives me nuts in my unit tests.

    describe Invitation do
      before do 
        @owner = Factory :registered_user
        @guest = Factory :user
        @event = Factory :event, :owner => @owner
        GirlFriday::WorkQueue.immediate!
      end
      describe "Comment subscriptions" do
        describe "A new invitation" do
          before do
            @invitation = @event.invite @guest
          end

          it "should not be subscribed to comments" do
            @invitation.should_not be_subscribed_for_comments
          end

          describe "After a comment has been made" do
            before do
              @comment = Comment.build_from(@event, @guest.id, "foo")
              @comment.save!
            end

            it "should be subscribed to comments" do 
              @invitation.reload
              @invitation.should be_subscribed_for_comments
            end
          end
        end
      end
    end


Yeah I know. No stubbing, mocks or other fancy cool stuff. However there is so
much boiler plate to describe what is really just a sequence of steps to verify.
The rspec output is ( and the wording could be better but this is not the point )

    Comment subscriptions
      A new invitation
        should not be subscribed to comments
        After a comment has been made
          should be subscribed to comments


Why all the indentation. It's a sequence of steps we are verifying the
behaviour of, not generating a YAML file!.

~


I've been using [RSpec Example Steps][1] in my acceptence tests recently and I
thought why not give it a try for normal RSpec tests and see what happens.


    describe Invitation do
      Steps do
        Given "A user a guest and an event" do
          @owner = Factory :registered_user
          @guest = Factory :user
          @event = Factory :event, :owner => @owner
          GirlFriday::WorkQueue.immediate!
        end

        When "the guest is invited to the event" do
          @invitation = @event.invite @guest
        end

        Then "the guest should not yet be subscribed to comments" do
          @invitation.should_not be_subscribed_for_comments
        end

        When "the guest makes a comment" do
          @comment = Comment.build_from(@event, @guest.id, "foo")
          @comment.save!
        end

        Then "the guest should be subscribed to comments" do
          @invitation.reload
          @invitation.should be_subscribed_for_comments
        end
      end

    end

with the RSpec output being

    Given A user a guest and an event
    When the guest is invited to the event
    Then the guest should not yet be subscribed to comments
    When the guest makes a comment
    Then the guest should be subscribed to comments

Why would you want to write your specs as if you are some asynchronous
coding god. If you want to test a sequence of steps and have a nice
RSpec report that describes the behaviour of the code then try out
[RSpec Example Steps][1]

[1]: https://github.com/railsware/rspec-example_steps


    
    
