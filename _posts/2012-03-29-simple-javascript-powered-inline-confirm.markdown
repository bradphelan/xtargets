--- 
title: Simple javascript powered inline confirm
date: 29/03/2012
layout: post
--- 

Browser confirmation dialogs are ugly. They can't really be styled
and anyway who wants a big fat popup making the user drive thier mouse
and focus away from where the action is at. Here is an easy solution 
using unobtrusive javascript techniques

Say we have an image viewer and we want a link to delete the image.
My haml might look like


    .item{'data-image_id'=>image.id}
      = image_tag image.attachment.expiring_url(s3_expiry, :large)
        .carousel-caption

          = link_to url_for_event(:delete_image, :asset_id => image.id),
            :method => :delete, 
            :remote => true,
            :class => "delete inline-confirm" do
            delete

Forget the fluff. I'm using twitter bootstrap carousel here but that
is irrelevant. The key is adding the class **inline-confirm**. That
is all.

The user interface interaction will go like this


    IMG

    delete

Imagine IMG is replaced with a picture of your sweetheart. You then
click on **delete** and the screen changes to


    IMG

    yes no

You click on __yes__ and the original link is followed. You click on __no__
and the action canceled. The coffeescript to achieve this is 

    $(document).ready =>

      $("body").on "click", ".inline-confirm", (e)=>
        t = $(e.target)
        t.hide()

        yn = """
          <div>
            <a class="no" href='#' >no</a>
            &nbsp;
            <a class="yes" href='#'>yes</a>
          </div>
        """
        yn = $(yn)

        yn = yn.insertAfter t
        yn.fadeIn()

        yn.find(".yes").one "click", =>
          t.removeClass("inline-confirm")
          t.click()
          t.addClass("inline-confirm")
          yn.remove()
          false

        yn.find(".no").one "click", =>
          yn.remove()
          t.show()
          false

        false

The user experience improvement was immediate. To delete a sequence of images could be done
an order of magnitude faster because I didn't have to move the mouse around.

The code could be enhanced with more features and maybe some styling. If you have ideas
for enhancements tweet me.

~




        

