\ SEDWIND.SEQ   Editor window adjustment words

editor definitions


0 value zoomed?
0 value topwind?
12 value splitline#

defer toggle-files      ' noop is toggle-files

: adjust-window ( --- )
        begin
                savescr
                20  6 58 10 box&fill
                ."  Adjust window size..  \r ESC \0 = done"
                bcr bcr
                ."  =Smaller window   =Bigger window "
                cursor-off key dup 27 <>
                restscr
        while   dup 200 =       \ UP arrow      SMALLER WINDOW
                if      first.textcol 0=
                    if        1 =: first.textcol
                        cols 2- =: last.textcol
                    else
                        first.textline 2 <
                        if      incr> first.textline
                                0 recoverline
                                incr> screenline
                        else    last.textline 1- first.textline 2+ max
                                =: last.textline
                        then
                    then
                then
                    208 =       \ DOWN arrow    BIGGER WINDOW
                if      first.textline 1 =
                        splitwind? 0= and
                    if     0    =: first.textcol
                        cols 1- =: last.textcol
                    else
                        last.textline rows 2- =
                        if      first.textline 1- 1 max =: first.textline
                                decr> screenline
                        else    last.textline 1+
                                rows 2- min =: last.textline
                        then
                    then
                then    scrshow on> ?border showstat
                last.textline rows 1- <
                if      last.textline 2+ recoverline then
        repeat  drop
        splitwind?
        if      topwind?
                if      last.textline  !> splitline#
                else    first.textline !> splitline#
                then
        then
        off> zoomed?                    \ next zoom goes to big window
        tscrn   scrshow                 \ move cursor to top of screen
        on> ?border showstat
        cursor-on ?cursor showcur ;

' adjust-window is adjwind

 1 value first.textline.saved
24 value last.textline.saved
 0 value first.textcol.saved
80 value last.textcol.saved

: zoom-window   ( --- )     \ Zoom the window from current size to full size.
                zoomed? dup 0= =: zoomed?
                if      \ IF we are already ZOOMed then zoom to small window
                        first.textline.saved =: first.textline
                        last.textline.saved  =: last.textline
                        first.textcol.saved  =: first.textcol
                        last.textcol.saved   =: last.textcol
                        screenline first.textline max
                                   last.textline  min =: screenline
                        0 recoverline
                        scrshow on> ?border showstat
                        last.textline rows 2- <
                        if      rows    last.textline 2+
                                ?do     i recoverline
                                loop
                        then
                else    \ ELSE zoom to big window
                        first.textline =: first.textline.saved
                        last.textline  =: last.textline.saved
                        first.textcol  =: first.textcol.saved
                        last.textcol   =: last.textcol.saved

                                    1  =: first.textline
                               rows 1- =: last.textline
                                    0  =: first.textcol
                               cols 1- =: last.textcol
                        first.textline.saved screenline =
                        if      first.textline =: screenline
                        then
                        on> ?border showstat
                        scrshow
                then    ;

' zoom-window is zoomwind
0 constant topwindline

: toggle-wind   ( -- )
                splitwind? 0= ?exit
                topwind? dup 0= !> topwind?
                if      splitline# 1+ =: first.textline
                              rows 2- =: last.textline
                else   topwindline 1+ =: first.textline
                        splitline# 1- =: last.textline
                then
                scrline first.textline max last.textline min
                dup =: screenline !> scrline
                ( showstat scrshow showcur ) ;

: split-wind    ( -- )          \ split window into two windows
                hdepth 1 <              \ must have two file sopen
                if      beep exit
                then
                splitwind? dup 0= =: splitwind?
                if      \ IF WE ARE ALREADY SPLIRT, THEN UNSPLIT
                        first.textline.saved =: first.textline
                        last.textline.saved  =: last.textline
                        first.textcol.saved  =: first.textcol
                        last.textcol.saved   =: last.textcol
                        screenline first.textline max
                                   last.textline  min =: screenline
                        0 recoverline
                        scrshow on> ?border showstat
                        last.textline rows 2- <
                        if      rows    last.textline 2+
                                ?do     i recoverline
                                loop
                        then
                else    \ ELSE split the window
                        first.textline =: first.textline.saved
                        last.textline  =: last.textline.saved
                        first.textcol  =: first.textcol.saved
                        last.textcol   =: last.textcol.saved
                        toggle-files
                then    ;

' split-wind  is splitwind

forth definitions

