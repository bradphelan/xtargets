--- 
title: "Changing the bullets style for showoff"
date: 15/10/2010
--- 

I got forced into doing a presentation lately and as I'm no
fan of powerpoint I tried out [showoff](http://github.com/schacon/showoff),
a markdown based presentation generator. You can generate static or
fully web based presentations.

However I didn't like the bulleting style which centered everything 
which I think is quite ugly. It's easy to change however
with a bit of CSS.


    .bullets ul li {
        text-align: left;
    }

    .smbullets ul li {
        text-align: left;
    }

This can be placed in *foo.css* or *anythingelse.css* in the root
directory of your presentation and showoff will automatically load
it.


