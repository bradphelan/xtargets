--- 
title: Cutting and pasting source code from VIM to Skype
date: 13/10/2010
--- 

I found cutting and pasting snippets of code into my skype im very tedious
as I'd always have to say which file and which line numbers. I wrote this
small snippet to solve it

    function! CopyWithLineNumbers() range
        redir @*
        sil echomsg "----------------------"
        sil echomsg expand("%")
        sil echomsg "----------------------"
        exec 'sil!' . a:firstline . ',' . a:lastline . '#'
        redir END
    endf

    com! -range CopyWithLineNumbers <line1>,<line2>call CopyWithLineNumbers()

and when the CopyWithLineNumbers command is run on highlighted text you get
in your paste buffer.


    ----------------------
    /home/brad/.vimrc
    ----------------------
    206 " Allows copy the buffer with line numbers included.
    207  
    208 function! CopyWithLineNumbers() range
    209     redir @*
    210     sil echomsg "----------------------"
    211     sil echomsg expand("%")
    212     sil echomsg "----------------------"
    213     exec 'sil!' . a:firstline . ',' . a:lastline . '#'
    214     redir END
    215 endf
    216  
    217 com! -range CopyWithLineNumbers <line1>,<line2>call CopyWithLineNumbers()

I dedicate this blog post to my colleagues who keep insisting on sending me
code snippets without telling me the file or line number it comes from




