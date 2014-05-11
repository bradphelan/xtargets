--- 
title: CANCAN Devise and Apotomo living together in harmony
date: 14/02/2012
--- 

The [Apotomo Framework](https://github.com/apotonick/apotomo) cleans up Ruby On
Rails view code. It encapsulates **widgets** into easy to reuse object oriented
modules packages. However none of the examples I could find used real world
examples with authorization and authentication bundled in. 

The packages I use are [Devise](https://github.com/plataformatec/devise) and
[CANCAN](https://github.com/ryanb/cancan) which seem to be pretty much standard
with most new rails projects

The easy way to get it working is to create a base class which adds in all the functionality
for auth that you will need.

    class AuthorizableWidget < Apotomo::Widget

      include Devise::Controllers::Helpers 
      helper_method :current_user

      def can?(action, object)
        current_ability.can? action, object
      end
      helper_method :can?

      def cannot?(action, object)
        current_ability.cannot? action, object
      end
      helper_method :cannot?

      def authorize! *args
        parent_controller.authorize! *args
      end

      def current_ability
        ::Ability.new current_user
      end

    end


Note that above I am calling the **authorize!** method on the parent_controller which
is the *real* controller that the browser calls. The controller.authorize! method sets
a variable on the controller which sets a flag telling the controller that an **authorize!**
method has been called. If you use the method **check_authorization** as a sanity check
this flag is what stops the error being thrown.

This becomes the base class for all new widgets that need authorization capabilities. You
use it so

~

    class CommentsWidget < OpenKitchen::AuthorizableWidget
                                        
      responds_to_event :comment
      responds_to_event :destroy
      
      #
      # Events
      #

      # Destroy a comment
      def destroy(evt)
        @comment = Comment.find params[:comment_id]
        @event = @comment.commentable
        
        # Call the CANCAN authorize! method here!
        authorize! :destroy, @comment

        @comment.destroy
        update({:state => :display}, @event)
      end

      # Add a comment
      def comment(evt)
        commentable_type = case params[:comment][:commentable_type]
                            when 'Event'
                              ::Event
                            else
                              raise "nice try!"
                            end

        @event = commentable_type.find(params[:comment][:commentable_id])

        @comment = Comment.build_from @event, 
          current_user.id, 
          params[:comment][:body]

        # Call the CANCAN authorize! method here!
        authorize! :create, @comment

        @comment.save


        update({:state => :display}, @event)
      end

      #
      # Views
      #
      def display(event = options[:event])
        @event = event
        @comments = @event.root_comments
        render
      end

      def list(comments)
        render locals: { comments: comments}
      end

      def item(comment)
        render locals: { comment: comment }
      end

    end

Note that the helpers are also now available to the Apotomo views

*app/widgets/comments/item.html.haml*

    %li.comment{id: "comment_#{comment.id}"}
      %span.author=comment.user.name
      %span.body=simple_format h(comment.body)
      %small
        %span.date
          =distance_of_time_in_words_to_now comment.created_at, true
          ago
      - if can? :destroy, comment
        = link_to url_for_event(:destroy, :comment_id => comment.id),
          remote: true, 
          class: "destroy", 
          title: t('comment.remove'), 
          :confirm => t('confirm') do
          %small
            %i.icon-fire
            =t :delete


It would be nice if this was distributed with Apotomo by default as it was a
bit of trouble to figure out, especially the special flag set by authorize! as
I was initially just calling **current_ability.authorize!** which didn't set
the flag.


