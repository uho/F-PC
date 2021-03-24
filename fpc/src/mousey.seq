\ MOUSEY.SEQ    Development level mouse support for F-PC    by Tom Zimmer

comment:

  This file contains the various mouse button drivers for the F-PC
development environment.  In effect each mode in an application needs a new
button driver if the mouse is to be used effectively in that mode.

  These drivers are fairly simple to write, as you can see from the
following examples.  In addition to the following, you need to create an
installer in your mode switch word to cause your driver to take effect when
a particular mode is entered.  Here is an example of a simple mechanism to
install a driver for a particulary mode.

: MYMODE        ( --- )
                VARIOUS INITIALIZATION

                ['] MY-BUTTON SAVE!> DOBUTTON   \ install new and save
                                                \ current driver

                DO WHATEVER I WANT TO DO IN MY MODE

                RESTORE> DOBUTTON               \ restores previous driver
                ;                               \ all done leave

  try using FLOOK to find occurances of DOBUTTON in the SRC directory, for
actual examples of how the above  works.

comment;

only forth also hidden definitions also
\unless editor  editor also

headerless

\ ***************************************************************************
\ Line editor button driver

: %ledbutton    ( --- )         \ line edit button handler
                mousebutton
                case
              2 of      27 ( ESC )   =: mousechar endof
              1 of      13 ( enter ) =: mousechar endof
                        drop
                endcase ;

' %ledbutton is ledbutton

\ ***************************************************************************
\ Window file selection button driver starts here

: ?dir-window   ( --- )
                mousexy forgy 1- > swap forgx > and
                mousexy forgy dlen + < swap forgx 15 + < and and
                                                        \ within files box?
                if      mousexy nip dirrow forgy + - ?dup
                        if      dup abs swap 0<
                                if      0 ?do pfl loop
                                else    0 ?do nfl loop
                                then
                                hide.ms showdir show.ms
                        else    13 ( Enter ) =: mousechar
                        then
                        track-mouse
                        hide.ms showdir show.ms
                then    ;

: ?dir-down     ( --- )
                mousexy forgy dlen + = swap forgx 15 + = and
                if      dlen 2/ 0 do nfl loop
                        hide.ms showdir show.ms
                then    ;

: ?dir-up       ( --- )
                mousexy forgy = swap forgx 15 + = and
                if      dlen 2/ 0 do pfl loop
                        hide.ms showdir show.ms
                then    ;

: ?path-window  ( --- )
                mousexy forgx forgy 26 11 d+ rot = >r 41 over + between
                r> and
                if      '\' =: mousechar
                then    ;

: %wflbutton    ( --- )
                mousebutton
                case
              2 of      27 ( ESC )   =: mousechar endof
              1 of      ?dir-window
                        ?path-window
                        ?dir-up
                        ?dir-down
                        endof
                        drop
                endcase ;

' %wflbutton is wflbutton

\ ***************************************************************************
\ Menubar button driver starts here

: ?select-menu  ( --- )
                mousexy nip mline - dup mrow <= swap 0> and
                if      13 ( Enter ) =: mousechar
                else    27 ( ESC )   =: mousechar
                then    ;

: %mbutton      ( --- )
                mousebutton
                case
                2 of    27 ( ESC )   =: mousechar endof
                1 of            ?select-menu      endof
                        drop
                endcase ;

' %mbutton is mbutton

defined charline nip
#if

\ ***************************************************************************
\ Graphic character insertion tool button driver

: %charbutton   ( --- )         \ mousebutton down handler
                mousebutton
                case
              2 of      27 ( ESC )   =: mousechar endof
              1 of      mousexy charline extrows over + between
                        swap    charcol dup 1+ swap
                        extcharseg +xseg 0 c@L + between and
                        if      mousexy                 \ if on same char
                                ty 1+ - swap tx 2+ - 2/ swap 2dup
                                chrow = swap chcol = and
                                if      2drop           \ do the char
                                        13 ( Enter ) =: mousechar
                                else                    \ else move to char
                                        =: chrow =: chcol
                                        tx 1+ ty extrows 1+ + at
                                        extchar@ 4 .r
                                        tx 2+ chcol 2* + ty 1+ chrow + at
                                then
                        then
                        endof
                        drop
                endcase ;

' %charbutton is charbutton

\ ***************************************************************************
\ The SED editor button driver starts here.

: move>mouse    ( --- )                 \ move edit cursor to mouse position
                mousexy swap 1- 0MAX winoff + =: screenchar
                screenline - dup 0<
                if      abs 0 ?do suln loop
                else        0 ?do sdln loop
                then    ;

: track-marks   ( --- )         \ follow cursor and mark some lines for
                                \ cut or copy.
                mousexy nip
                begin   mousebutton             \ while the mouse is pressed
                while   mousexy nip over <>
                        if      mark-clear
                                mark-on/off
                                begin   scrshow
                                        move>mouse
                                        hide.ms showstat show.ms
                                        mousebutton 0=
                                until
                                mark-on/off
                                hide.ms showstat show.ms
                        then
                repeat  drop ;

: ?cursor-move  ( x y --- x y )
                2dup
                first.textline last.textline   between swap
                first.textcol  last.textcol 1- between and
                if      mousexy
                        swap 1- 0MAX screenchar =       \ on col
                        swap screenline = and           \ on line
                        if      ?altkey
                                if      163 ( alt-h)    \ MDC see help
                                else    176 ( Alt-b )   \ MDC see source
                                then    =: mousechar
                        else
                                move>mouse              \ else move cursor
                                hide.ms scrshow
                                showstat show.ms
                                track-marks
                        then
                then
                showcur
                ;

: ?help-do      ( x y --- x y )
                2dup last.textline 1+ = swap 2 10 between and
                if      187 ( F1 ) =: mousechar
                then    ;

: ?menu-do      ( x y --- x y )
                2dup last.textline 1+ = swap
                window.right 11 - window.right 2- between and
                if      27 ( ESC ) =: mousechar
                then    ;

: ?insert-toggle ( x y --- x y )
                hide.ms
                2dup statusline = swap 3 10 between and
                if      ?browse
                        if
                                220 ( browsetgl ) =: mousechar
                        else    imode 0=
                                if      on> imode
                                        220 ( browsetgl ) =: mousechar
                                else
                                        210 ( Ins ) =: mousechar
                                then
                        then
                then
                show.ms
                ;

: ?unlink       ( x y --- x y )         \ Button on F10 in upper right corner
                2dup statusline = swap 73 77 between and
                if      196 ( F10 ) =: mousechar
                then
                ;

: ?scroll-up    ( x y --- x y )
                ?lastline ?exit
                2dup last.textline 1+ =
                swap 11 window.right 12 - between and
                if      begin   scldn
                                showstat
                                mousebutton 0= ?lastline or
                        until
                then
                showcur
                ;

: ?scroll-dn    ( x y --- x y )
                curline 0= ?exit
                2dup statusline =
                over 11 window.right between and        \ on top line but not
                                                        \ in INSERT
                swap 73 77 between 0= and               \ not in F10
                if      begin   sclup
                                showstat
                                mousebutton 0= curline 0= or
                        until
                then
                showcur
                ;

: ?scroll-right ( x y --- x y )
                2dup statusline last.textline 5 - between
                swap window.right = and
                if      begin   1 %scrlrt
                                cursor-off showstat cursor-on
                                mousebutton 0=
                        until
                then
                showcur
                ;

: ?scroll-left  ( x y --- x y )
                over window.left =
                if      begin   1 %scrllft
                                cursor-off showstat cursor-on
                                mousebutton 0=
                        until
                then
                showcur
                ;

: ?page-down    ( x y --- x y )
                2dup last.textline dup 1- swap between
                swap window.right = and
                if      209 ( PgDn ) =: mousechar
                then    ;

: ?page-up      ( x y --- x y )
                2dup last.textline 4 - dup 1+ between
                swap window.right = and
                if      201 ( PgUp ) =: mousechar
                then    ;

: %sbutton      ( --- )
                mousebutton
                case
                2 of    27 ( ESC )   =: mousechar endof
                1 of    mousexy ?cursor-move
                                ?insert-toggle
                                ?help-do
                                ?menu-do
                                ?scroll-up
                                ?scroll-dn
                                ?scroll-left
                                ?scroll-right
                                ?page-down
                                ?page-up
                                ?unlink
                                2drop
                        endof
                        drop
                endcase ;

' %sbutton is sbutton

\ ***************************************************************************
\ The SED PRINTING button driver starts here.

: %pbutton      ( --- )
                mousebutton
                case
                2 of    27 ( ESC )   =: mousechar endof
( printto line) 1 of    mousexy 18 = swap 32 71 between and
( set device )          mousexy 16 = swap 38 40 between and or
                        if      's' =: mousechar
( start printing )      else    mousexy 16 = swap 26 28 between and
                          if      'p' =: mousechar
( ESC, stop printing )    else  mousexy 16 = swap 11 15 between and
                            if      27 ( ESC ) =: mousechar
( else down arrow )         else
                                pitem @ 1+ pitems mod pitem ! sc
                                showpcur pnumval off
                                begin   mousebutton 0=
                                until   off> mousewasdown showpcur
                            then
                          then
                        then
                        endof
                        drop
                endcase ;

' %pbutton is pbutton

\ ***************************************************************************
\ The BROWSE button driver starts here.

: %browbutton    ( --- )         \ line edit button handler
                mousebutton
                case
              2 of      27 ( ESC )   =: mousechar endof
              1 of      mousexy dup 10 =
                        if      drop dup 52 56 between
                                if      'y' =: mousechar else
                                dup 58 61 between
                                if      'n' =: mousechar else
                                beep
                                then
                                then    drop
                        else    11 = swap 27 40 between and
                                if      27 ( ESC ) =: mousechar
                                else    beep
                                then
                        then
                        endof
                        drop
                endcase ;

' %browbutton is browbutton

#endif

only forth also definitions

headers

