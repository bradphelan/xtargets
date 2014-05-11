--- 
title: notify-send
date: 10/06/2010
--- 

If you as me spend a lot of time waiting for C++ projects to rebuild then
you generally try to find other things to do while the compiler is generating
C02. However it is also very easy to get immersed in your alternate activity,
be that reading up on the latest meta programming tricks in boost or consuming
noise on slashdot. Before you know it a whole morning has passed but your
compile stage finished hours ago.

notify-send to the rescue. When run, it just pops up a subtle message box on
your desktop, over all your other windows with whatever message you want. 
Install it via.

    aptitude install libnotify-bin 

and then if you are using make

    make ; notify-send build done

or scons

    scons; notify-send build done

or ant

    ant; notify-send "build done" "you java jockey"

That's it. 
