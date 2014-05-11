--- 
title: Make a nice horizontal list with dividers with sass and compass
date: 05/05/2011
layout: post
--- 

List are nice. They are easy to build with html and using css can be hooked
to style pretty much any way you want. Here is a very simple trick to 
horizontally style a list and place a vertical bar between every item
but not on the ends.

    @import "compass/utilities"

    .simple_navigation
      +no-bullets
      +horizontal-list

      li
        border-left: 1px solid
        border-right: 1px solid
        margin-top: 2px
        margin-bottom: 2px

      li:first-child
        border-right: 1px solid
        border-left: 0 none

      li:last-child
        border-left: 1px solid
        border-right: 0 none


First of all we import the compass utilities libraries
where we find two mixins for dealing with lists.

http://compass-style.org/reference/compass/typography/lists/

    +no-bullets
    +horizontal-list

That does the bulk of the work for us. Then we need to add the vertical bars.
First we style the list items to have solid left and right borders. Then we
specialize only the first and last item in the left to have right and left
border respectively.

Now it is bad form to use .simple_navigation in your html directly. 
.simple_navigation describes a style of a list. We should use
semantic names for each horizontal list we require. We can also
specialize the simple navigation using further mixins or extensions.

    #main_menu
        @extends .simple_navigation
        +adjust-font-size-to(30px)

    #footer_menu
        @extends .simple_navigation
        +adjust-font-size-to(15px)

Then #main_menu is a horizontal list with dividers and large text 
and #footer_menu is a horizontal list with dividers and small text

So then the following haml

    %ul#main_menu
        %li item 1
        %li item 2
        %li item 3

will end up something like below

    item 1 | item 2 | item 3 | item 4

Simple!
