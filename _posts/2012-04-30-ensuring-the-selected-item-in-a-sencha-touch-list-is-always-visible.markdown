--- 
layout: post
title: Ensuring the selected item in a sencha touch list is always visible.
date: 30/04/2012
layout: post
--- 

Sencha Touch "Ext.List" lists are nice and flexible but they have one fiddly
problem. If you manually set the selected item in a list from a callback like
so

    card.on
      activeitemchange: (card, item, oldIndex)=>
        index = card.items.indexOf(item)

        # Change the list selection to reflect
        # the current card
        list.select(index)


where **list** is a currently defined **Ext.List** object then all works fine
until you select an item that is not currently in the visible window.
**Grrrrrr** it is not visible and there is no public API to help you keep it
so.

For example my setup is a card layout on the right and a list on the
left reflecting the current card.

<div class="thumbnail"><a href="https://skitch.com/bradphelan/8s38m/ios-simulator"><img style="max-width:638px" src="https://img.skitch.com/20120430-ppxukim7mchmh3buana8pnufec.medium.jpg" alt="iOS Simulator" /></a><br /><span>Uploaded with <a href="http://skitch.com">Skitch</a>!</span></div>

To select a card you can either directly select an item from the list or proceed 
sequentially back and forth using the left and right arrow on top of
the card.

~

What we would like is an unobtrusive solution that doesn't jump the scroll
of the list around too much but keeps the selected item always in view and
makes it always possible to directly select a next item on the list without
finger scrolling.

I have added the following code to my **activeitemchange** callback. It
can be added anywhere it is appropriate or made a method on a subclass
of **Ext.List** if you prefer.

    card.on
      activeitemchange: (card, item, oldIndex)=>
        index = card.items.indexOf(item)
        list.select(index)

        # Get the selected item. Seems
        # a bit ugly but it works
        cls = list.getSelectedCls()
        selectedElement = list.element.down("." + cls)
        selectedHeight = selectedElement.getHeight()

        if selectedElement
          # Height of the full list
          fullListHeight = list.element.down(".x-list-container").getHeight()

          # Height of the scroll window
          scrollWindowHeight = list.element.getHeight()

          yTop = selectedElement.dom.offsetTop
          yBottom = yTop + selectedHeight
          
          scroller = list.getScrollable().getScroller()

          # Outside this zone a scrollTo will be triggered
          triggerZone =
            min: scroller.position.y + selectedHeight
            max: scroller.position.y + scrollWindowHeight - selectedHeight

          # Conditions for items above the current scroll
          # position
          if yTop < triggerZone.min
            pos = yTop - selectedHeight

          # Conditions for items below the current scroll
          # position
          if yBottom > triggerZone.max
            pos = yTop + 2*selectedHeight - scrollWindowHeight

          # Adjust for the edge cases where the item
          # to scroll is at the top or bottom of the
          # list so we don't get nasty jumping effects
          if pos?
            pos = Math.max(0, pos)
            pos = Math.min(pos, fullListHeight - scrollWindowHeight)
            scroller.scrollTo(0,pos,true)


You can see a screen cast of the effect in action below

<iframe src="http://player.vimeo.com/video/41286078" width="500" height="644" frameborder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>

  

