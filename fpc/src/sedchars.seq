\ SEDCHARS.SEQ  Extended character set insertion for SED

forth definitions

defer charbutton ' noop is charbutton

59 value charcol
01 value charline
 0 value chcol
 0 value chrow

XHERE PARAGRAPH + DUP XDPSEG ! XSEG @ - XDP OFF
value extcharseg ( --- seg-rel )
0               x,"           " 1+
        xdp @   x,"   	        " 10 extcharseg +xseg rot 6 + c!L  1+
                x,"          " 1+
                x,"          " 1+
                x,"  Ä Å Ç É Ñ Ö Ü á " 1+
                x,"  à â ä ã å ç é è " 1+
                x,"  ê ë í ì î ï ñ ó " 1+
                x,"  ò ô ö õ ú ù û ü " 1+
                x,"  † ° ¢ £ § • ¶ ß " 1+
                x,"  ® © ™ ´ ¨ ≠ Æ Ø " 1+
                x,"  ∞ ± ≤ ≥ ¥ µ ∂ ∑ " 1+
                x,"  ∏ π ∫ ª º Ω æ ø " 1+
                x,"  ¿ ¡ ¬ √ ƒ ≈ ∆ « " 1+
                x,"  » …   À Ã Õ Œ œ " 1+
                x,"  – — “ ” ‘ ’ ÷ ◊ " 1+
                x,"  ÿ Ÿ ⁄ € ‹ › ﬁ ﬂ " 1+
                x,"  ‡ · ‚ „ ‰ Â Ê Á " 1+
                x,"  Ë È Í Î Ï Ì Ó Ô " 1+
                x,"   Ò Ú Û Ù ı ˆ ˜ " 1+
                x,"  ¯ ˘ ˙ ˚ ¸ ˝ ˛ ˇ " 1+
constant extrows

: ?auob         ( c1 -- f1 )    \ return true if A, U, O or B
                upc >r
                'A' r@ =    'U' r@ = or
                'O' r@ = or 'B' r> = or ;

: extchar@      ( --- c1 )
                extcharseg +xseg 0 chrow 0
                ?do     2dup c@L + 1+
                loop    1+ chcol 2* + 1+ c@L ;

: eurchar@      ( c1 -- c2 )    \ translate AUOB into an eumlated character
                " AéUöOôB·aÑuÅoîb" rot scan
                if      1+ c@
                else    drop bl
                then    ;

                                \ f1 is true if operation was not canceled
: graphicchar   ( --- <c1> f1 ) \ c1 present if f1 is true
                get-cursor >r
                ?doingmac 0=    \ if we are performing a macro, don't show
                                \ the character box in screen, we don't
                                \ want to see it anyway, and it slows things
                                \ down too much.
                dup>r
                if      savescr
                        big-cursor
                        save> attrib >norm
                        charcol 23 - charline
                        charcol  1 - charline 4 + box
                        >ATTRIB1
                        ."  Press key:  A U O B " >norm bcr
                        ."  upper case  é ö ô · "       bcr
                        ."  lower case  Ñ Å î  "
                        charcol charline
                        charcol extcharseg +xseg 0 c@L
                        + 1+ charline extrows 2+ + box
                        tx 1+ ty extrows 1+ + at ."       ESC=Cancel "
                        extcharseg +xseg 0 extrows 1+ 1
                        do      tx 1+ ty i + at
                                2dup c@L 1 0 d+ 3dup typeL +
                        loop    2drop
                        restore> attrib
                then
                ['] charbutton save!> dobutton
                begin   r@ \ if not doing a macro display value
                        if      tx 1+ ty extrows 1+ + at extchar@ 4 .r
                                tx 2+ chcol 2* + ty 1+ chrow + at
                        then
                        key dup $20   >=
                           over ?AUOB 0= and
                while   ( c1 --- )
                        case
( Up Arrow )                200 of  chrow 1-            0max =: chrow endof
( Dn Arrow )                208 of  chrow 1+  extrows 1- min =: chrow endof
( Rt Arrow )                205 of  chcol 1+           7 min =: chcol endof
( Lt Arrow )                203 of  chcol 1-            0max =: chcol endof
( PgDn )                    209 of  chrow 5 + extrows 1- min =: chrow endof
( PgUp )                    201 of  chrow 5 -           0max =: chrow endof
( Home )                    199 of                         0 =: chcol endof
( End )                     207 of                         7 =: chcol endof
                                drop
                        endcase
                repeat
                restore> dobutton
                r>                                      \ doing macro flag
                if      restscr
                then    dup 13 =
                if      drop extchar@ true
                else    dup ?AUOB
                        if      eurchar@ true
                        else    dup 27  =               \ if ESC, cancel
                                if      drop false
                                else    true            \ else insert char
                                then
                        then
                then    r> set-cursor ;

' graphicchar is graphchar

\ Link the graphics character selector into editor assuming the editor
\ has been loaded, otherwise we are done.

defined editor nip #if          \ load only if EDITOR exists

: sedchar       ( -- )
                ?browse ?exit
                graphicchar
                if      schr
                then    scrshow ?cursor ;

' sedchar is insany

: (--)char      ( -- )          \ insert a standard comment framework
                ?browse ?exit
                " ( -- ) \ " bounds
                do      i c@ schr
                loop    scrshow ?cursor ;

#endif

forth definitions


