\ SEDPAGE.SEQ   Allow going to a particular page in SED         by Tom Zimmer

editor definitions

: gopage        ( --- )         \ set right margin to current column
                20  6 50 12 box&fill
                bcr ."  Current page is " curline prtlines / 1+ .
                bcr
                bcr ."  Goto Page "
                pad off
                32 10 pad 8 lineeditor
        if      pad count dup #tib ! tib swap cmove >in off
                bl word  dup c@ 0> >r number? r> and
                if      drop 1 max 132 min 1- prtlines * curline over >
                        if      1 max
                                first.textline =: screenline
                                backto.line
                        else    to.line
                        then
                else    2drop
                then
        then    edeeol >norm scrshow ;

\ ' gopage is topage

: goline        ( --- )         \ set right margin to current column
                20  6 50 12 box&fill
                bcr ."  Current line is " curline 1+ .
                bcr
                bcr ."  Goto Line "
                pad off
                32 10 pad 8 lineeditor
        if      pad count dup #tib ! tib swap cmove >in off
                bl word  dup c@ 0> >r number? r> and
                if      drop 0MAX maxlines min 1- curline over >
                        over 9 min first.textline +
                        last.textline min =: screenline
                        if      backto.line
                        else    to.line
                        then
                else    2drop
                then
        then    edeeol >norm scrshow ;

\ ' goline is toaline

: nextpar       ( --- )
                begin   linelen  0> ?lastline 0= and while <sdln> repeat
                begin   linelen  0= ?lastline 0= and while <sdln> repeat
                curline screenline 8 max min =: screenline
                scrshow ;

: prevpar       ( --- )
                <suln>
                begin   linelen  0= ?firstline 0= and while <suln> repeat
                begin   linelen  0> ?firstline 0= and while <suln> repeat
                ?firstline
                if      first.textline =: screenline
                        scrshow
                else    last.textline first.textline - 2/ screenline min
                        =: screenline
                        nextpar
                then ;

: ALT-GO        ( --- )         \ Alt-G options
                ['] noop save!> dobutton
                savescr
                ?doingmac 0=    \ If we are doing a macro, don't display
                                \ command menu box.
                if      screenline 1+ dup 12 >
                        if      13 -
                        then    20 swap 60 over 9 + box&fill
                        ."  Goto commands.. Select an operation" bcr bcr
                        ."     P - Goto a specified Page"     bcr
                        ."     L - Goto a specified Line"     bcr
                        ."     N - Goto the Next paragraph"   bcr
                        ."     B - Goto the paragraph Before" bcr bcr
                        ." \s10\r ESC \0 = cancel"
                        showcur
                then
                key bl or >r
                restscr
                'p' r@ = if gopage          then
                'l' r@ = if goline          then
                'n' r@ = if nextpar         then
                'b' r> = if prevpar         then
                restore> dobutton
                sdisplay showstat cursor-on ;

' ALT-GO IS ALT-G

forth definitions

