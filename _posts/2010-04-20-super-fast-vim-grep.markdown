--- 
title: Super fast vim grep
date: 20/04/2010
layout: post
--- 

In your .vimrc file do

    set grepprg=zsh\ -c\ 'grep\ -nH\ $*'

zsh allows recursive glob expansions so you can do do from
your vim command line

    grep "def foo" **/*.rb

of which the results will end up in you quickfix buffer. This
is several orders of magnitude faster than the standard vimgrep
command.

To see the quick fix buffer you need to do

    :copen

at the vim command line

