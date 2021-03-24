\ SAVESCR.SEQ   Routine to save and restore screen.     by Tom Zimmer

decimal

0.0 pointer svseg               \ screen save segment
    4 value svmax               \ maximum number of screens to save
 4000 value svsize              \ size of each screen
    0 value svtotal             \ total of saved screens

: svinit        ( --- )         \ screen save initialization
                defers initstuff
                rows cols 2* * =: svsize        \ set save size per screen
                65535. svsize um/mod nip        \ maximum number of screens
                1- svmax min =: svmax           \ limit svsize to < 64k
                svsize svmax * =: svtotal       \ adjust total arraysize
                svmax 1+ svsize *D sizeof!> svseg
                svseg 0=
                if      cr
                        ." Could not allocate memory for Screen save routine."
                then    ;

' svinit is initstuff

\ nestable save and restore up to SVMAX times.

: savescr       ( --- )
                svseg 0= ?exit
                svseg 0 2dup svsize + svtotal cmovel>   \ Make a hole in buf
                blank.color
                video-seg @ 0 svseg 0 svsize cmovel       \ & save screen in it.
                show.color ;

: restscr       ( --- )
                svseg 0= ?exit
                blank.color
                svseg 0 video-seg @ 0 svsize cmovel     \ Restore screen &
                show.color
                svseg svsize over 0 svtotal cmovel ;    \ compress buffer.

: recoverscr    ( --- )                                 \ get a copy of the
                svseg 0= ?exit                          \ last saved screen
                blank.color
                svseg 0 video-seg @ 0 svsize cmovel     \ Restore screen
                show.color ;

: recoverline   ( n1 --- )                          \ get a copy of line n1
                svseg 0= if drop exit then          \ from last saved screen
                dup 0 rows 1- between
                if      cols 2* * >r
                        blank.color
                                                        \ Restore line n1
                        svseg r@ video-seg @ r> cols 2* cmovel
                        show.color
                else    drop
                then    ;

comment:

\ This commented out section of code saves and restores the screen on entry
\ and exit from F-PC. It is not being compiled at the present time because
\ I do not like the effect.

2variable entry-curpos

0 24 entry-curpos 2!                    \ initialize to bottom of screen

: entry-save    ( --- )                 \ save screen on entering F-PC
                defers initstuff
                #out @ #line @ entry-curpos 2!
                savescr ;

' entry-save is initstuff

: exit-restore  ( --- )                 \ and restore screen on leaving F-PC
                defers byefunc
                restscr
                entry-curpos 2@ at ;

' exit-restore is byefunc

comment;

\ Do we need screen blanking for out COLOR display, here are the words to
\ select blanking or non-blanking.

: blankon       ( --- )
                blanking on ;

: blankoff      ( --- )
                blanking off ;

