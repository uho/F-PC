\ SSCROLL.SEQ           Sample sub screen scrolling     by Tom Zimmer

comment:

  This file contains an example of how to make the screen scroll only a
limited set of lines.  the file contains three user words as follows:

        SUB-SET ( n1 --- )      \ set the line where scrolling starts
        SUB-ON                  \ start sub screen scrolling
        SUB-OFF                 \ stop sub screen scrolling

comment;

0 value sub-line

: sub-scroll    ( --- )         \ scroll only a portion of the screen
                #line @ sub-line <
        if      crlf
                                        \ scroll only a portion
        else    0 sub-line 1 max rows 1- min at -line
                0 rows 1- at            \ move to last line of screen
        then    ;

\ ******************* User words start here *******************

: sub-set       ( n1 --- )      \ set the starting sub screen scroll line
                1 max 23 min !> sub-line ;

: sub-on        ( --- )         \ Turn on sub screen scrolling
                ['] sub-scroll is cr ;

: sub-off       ( --- )         \ Turn off sub screen scrolling
                ['] crlf is cr ;

