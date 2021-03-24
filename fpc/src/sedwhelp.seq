\ SEDWHELP.SEQ          Word Help while in Editor       by Tom Zimmer

\ WORDHELP was suggested by Phil Friis

\ invoked by Alt-H

editor definitions

handle wordhndl
handle hndlsave
create helpbuf 32 allot

0 value wordline
0 value listsave
0 value newbrowse
0 value browseset
0 value ?wordhelp
0 value toggling

: 'word@cur     ( --- cfa f1 )
                @word@cur dup>r 1+ c@ hyperchar =
                                                \ remove a leading hyper char
                if      r@ count >r dup 1+ swap r> 1- cmove
                        -1 r@ c+!
                then
                r@ count + 2 bl fill            \ append a couple of blanks
                r@ hfind                        \ try to look it up
                r> c@ 0> and  ;

0 value wordfnd

: findword      ( --- f1 )
                IBRESET
                0.0 seqhandle movepointer
                0.0 filepointer 2!
                loadline off
                off> wordfnd
                bl helpbuf count + c!   \ follow string with a blank
                20000 1
                do      lineread c@ 0= ?leave
                        bl outbuf count + 2- c!
                                \ have at least 1 blank at end of line.
                        helpbuf count outbuf 1+ swap 1+ caps-comp 0=
                        if      i =: loadline
                                on> wordfnd leave
                        then
                loop    wordfnd ;

: cfa-word-ed/br    ( false cfa --- f1 )
                savescr
                cursor-off
                save> screenchar        \ save current cursor position
                here count 29 min helpbuf place
                wordhndl save!> seqhandle
                >viewfile       ( --- offset a1 )
                ?wordhelp
                if      " HLP" ">$ over $>ext
                then
                $file 0=
                if      =: listsave
                        on> leavesave  \ setup to leave EDIT
                        seqhandle hndlsave $>handle
                        browseset  =: newbrowse
                        screenline =: linesave
                        ?wordhelp
                        if      findword
                                if      loadline @ =: listsave
                                else    off> listsave
                                then
                        then
                        leavesave newfl and
                        if      on> changed     \ write newfile to disk
                        then
                else    drop true
                        " \4 FILE is not available " ?softerror
                then
                restore> seqhandle
                restore> screenchar             \ restore cursor position
                restscr
                cursor-on showcur
                leavesave
                if      sesc
                then    ;

                                        \ n1 = line number in file if found
: check-ndx     ( --- n1 f1 )           \ f1 = true if found index
                                        \ searched for word must be at HERE.
                here c@ 0= if 0 false exit then
                save> screenchar
                wordhndl save!> seqhandle
                here count 29 min helpbuf place
                $2020 helpbuf count + !
                " HYPER.NDX" ">$ $file 0=
                if      IBRESET
                        0.0 seqhandle movepointer
                        0.0 filepointer 2!
                        loadline off
                        off> wordfnd
                        0 20000 1
                        do      lineread c@ 0= ?leave
                                outbuf 1+ c@ 249 ( ù ) =
                            if          outbuf count 1 /string 2-
                                        hndlsave ">handle
                            else
                                $2020 outbuf count + 2- !
                                      \ have at least 1 blank at end of line.
                                helpbuf count outbuf 1+ swap 1+ caps-comp 0=
                                if      drop
                                        outbuf count bl scan 1 /string
                                        2dup bl scan nip - here place
                                        here count + 3 blank
                                        here number? 2drop
                                        on> wordfnd leave
                                then
                            then
                        loop    wordhndl hclose drop
                        wordfnd
                else    0 false
                then
                restore> seqhandle
                restore> screenchar ;

: line-ed/br    ( false line --- f1 )
                =: listsave
                save> screenchar        \ save because $FILE below resets it
                wordhndl save!> seqhandle
                hndlsave $file 0=
                restore> seqhandle
                if      wordhndl hclose drop
                        wordhndl hndlsave $>handle
                        on> leavesave  \ setup to leave EDIT
                        browseset  =: newbrowse
                        screenline =: linesave
                        newfl
                        if      on> changed     \ write newfile to disk
                        then
                        sesc
                else    true " \4 FILE is not available " ?softerror
                        scrshow
                then    restore> screenchar ;

: word-ed/br    ( false --- f1 )
                'word@cur
                if      cfa-word-ed/br
                else    drop
                        check-ndx
                        if      line-ed/br
                        else    drop
                                true " \4 No LINKAGE for this word "
                                ?softerror
                                scrshow
                        then
                then    ;

: wordedit      ( --- )
                off> browseset
                off> ?wordhelp
                word-ed/br ;

\ 146 fnset wordedit      \ function value for Alt-E

: worddefer     ( false --- f1 )
                on> browseset
                off> ?wordhelp
                'word@cur
                if      dup @rel>abs
                        ['] bgstuff @rel>abs =
                        if      >body @ cfa-word-ed/br
                        else    dup @rel>abs
                                ['] emit @rel>abs =
                                if      >is @ cfa-word-ed/br
                                else    drop
                                        true
                                        " \4 Not a DEFERED word " ?softerror
                                        scrshow
                                then
                        then
                else    drop
                        true " \4 No LINKAGE for this word " ?softerror
                        scrshow
                then    ;

' worddefer alias worddef

\ 240 fnset worddefer     \ function for Alt-F9

: wordbrowse    ( --- )
                on> browseset
                off> ?wordhelp
                word-ed/br ;

\ 176 fnset wordbrowse    \ function value for Alt-B
\ 195 fnset wordbrowse    \ function value for F9

: wordfrom      ( -- )                  \ show where word was loaded from
                on> loadedfrom
                wordbrowse ;

\ 330 fnset wordfrom      \ function value for Ctrl-F9

: browse-nln    ( --- )                 \ browse is Enter
                ?browse
                if      wordbrowse
                else    nln
                then    ;

\ control M ctlset browse-nln     \ install into Enter function

: wordhelp      ( --- )
                on> browseset
                on> ?wordhelp
                word-ed/br ;

\ 163 fnset wordhelp      \ function value for Alt-H

: sescALL       ( --- )         \ pop off all extra nest levels
                on> leavenow
                sesc
                off> leavenow
                on> pop-extra ;

\ 221 fnset sescALL       \ function value for Shift-F10

defer browbutton        ' noop is browbutton

: browsetgl     ( --- )
                ?browse 0=                              \ if browse is OFF
                if      changed updated or              \ have things changed
                        if      ['] browbutton save!> dobutton
                                cursor-off
                                16 8 64 12 box&fill     \ ask for verification
                                ."  You have made changes to this file," bcr
                                ."  do you want to SAVE your changes? "
                                ." \r Yes \0 \1 No " bcr
                                ." \s10\1 ESC = Cancel "
                                begin   key
                                        dup         13 =          \ Enter
                                        over        27 = or       \ ESC
                                        over bl or 'y' = or       \ YES
                                        over bl or 'n' = or 0=    \ NO
                                while   drop beep
                                repeat  cursor-on
                            dup 27 <>
                            if  bl or 'n' <>
                                if      updt            \ then save changes
                                        recover.$$$ ?ferr 0=
                                        if      off> updated
                                                off> changed
                                                ?browse 0= =: ?browse
                                        then
                                else    discard.$$$     \ or don't
                                        off> updated
                                        off> changed
                                        ?browse 0= =: ?browse
                                then    ['] hypertypeL is typeL
                            else drop                   \ or cancel operation
                            then
                                restore> dobutton
                                scrshow
                        else    ?browse 0= =: ?browse
                                ['] hypertypeL is typeL
                        then
                else    ?dosio
                        if      ['] (typeL)       is typeL
                        else    (lit) defers typeL is typeL
                        then
                        ?browse 0= =: ?browse
                then
                scrshow on> ?border showstat ;

\ 220 fnset browsetgl     \ function value for Shift-F9

64 array sednewbuf              \ define a 32 character buffer
         sednewbuf off          \ init to empty

: %sednew       ( --- )
                off> browseset          \ enter in EDIT mode
                ['] noop save!> dobutton
                savescr
                begin   ?shiftkey
                        if      @word@cur count 63 min sednewbuf place
                        then
                        6 7 73 13 box&fill
                        ."  \r Filename to OPEN or CREATE "
                        bcr bcr
                        #out @ 1+ #line @ ( --- x y )
                        bcr bcr
                        ."  Press Enter on empty line to pick from a list of files "
                        >attrib1
                        on> autoclear
        ( x y --- )     sednewbuf 64 lineeditor
                        >norm
                        if      sednewbuf count '*' scan nip
                                sednewbuf count '?' scan nip or
                                if      sednewbuf count dirspec$ place
                                        sednewbuf off
                                then
                                sednewbuf c@ 0=
                                if      getfile ( --- <a1> f1 )
                                        if      count 63 min sednewbuf place
                                                true true
                                        else    false
                                        then
                                else    true true
                                then
                        else    false true
                        then
                until
                if      hndlsave save!> seqhandle
                        sednewbuf $file 0=
                        if      hndlsave hclose drop
                                -1 =: leavesave  \ setup to leave EDIT
                                browseset =: newbrowse
                                screenline =: linesave
                                off> listsave
                        else    cursor-off
                                20 11 58 14 box&fill
                                ."  \2  File does not exist, CREATE it?  "
                                bcr
                                ." \s07\r Yes \0 No    ESC=Cancel"
                                0
                                begin   drop key
                                        dup  27 ( ESC )  =      \ ESC=No
                                        over 13 ( Enter) = or   \ Enter=Yes
                                        over upc 'Y'     = or   \ Y=Yes
                                        over upc 'N'     = or   \ N=No
                                until   dup 13 =                \ Enter
                                        swap upc 'Y'     = or   \ or Yes
                            if  seqhandle hcreate
                                20 4 61 6 box&fill space
                                if
                                   ." \2 Could NOT CREATE the requested file "
                                        beep 1 seconds beep
                                else
                                   ." \1   CREATING the requested NEW file   "
                                        2573 sp@ 2 seqhandle hwrite 2drop
                                        seqhandle hclose drop
                                        -1 =: leavesave  \ setup to leave EDIT
                                        browseset =: newbrowse
                                        screenline =: linesave
                                        off> listsave
                                then    >norm 1 seconds
                            then
                        then
                        restore> seqhandle
                        leavesave newfl and
                        if      on> changed     \ write newfile to disk
                        then
                then    restscr
                restore> dobutton
                cursor-on showcur ;

' %sednew is try_to_open

: sednew        ( --- )
                %sednew
                leavesave
                if      sesc
                then    ;

\ control O ctlset sednew

: togglefiles   ( --- )         \ rotate through open files
                ?shiftkey >r    \ with SHIFT to rotate backwards.
                savescr
                hseg 0= dup
                " \4 No handle stack segment allocated "      ?softerror
                hdepth maxh 1- >= dup  " \4 Nest stack FULL " ?softerror
                or 0=
                restscr
        if      screenline =: linesave
                off> leavesave
                sesc            \ leave this edit
                ed1>hstack      \ push this edit on stack
                r@
                if      hdepth 1- 0MAX 0
                        ?do     hrotate
                        loop
                else    hrotate \ rotate bottom of file stack to top
                then
                on> toggling
                toggle-wind
        then    r>drop ;

\+ toggle-files ' togglefiles is toggle-files

\ 232 fnset togglefiles           \ Alt-F1

0 value fliptop
0 value flipbot

: flipfiles     ( --- )
                ['] noop save!> dobutton
                savescr
                hseg 0= dup
                " \4 No handle stack segment allocated "     ?softerror
                hdepth maxh 1- >= dup " \4 Nest stack FULL " ?softerror
                or 0=
                restscr
        if      cursor-off
                10 11 hdepth 2+ 2/ - dup 1+ =: fliptop
                70 14 hdepth 1+ 2/ + dup    =: flipbot box&fill
        ." \r  \1 Press a letter for the file you want to select. \2 Line "
                bcr
                ."  A - " ed1hndl count type 64 #out @ - spaces
                curline 1+ 4 .r bcr
                hdepth 0
                ?do     space i 'B' + femit ."  - "
                        hseg b/hstk hdepth i 1+ - * 2dup 2dup c@L
                        swap 1+ swap typeL 64 #out @ - spaces
                        b/hcb + @L 4 .r bcr
                loop    ." \s20\r ESC = Cancel "
                begin   key     dup  'A' hdepth 'A' + between
                                over 'a' hdepth 'a' + between or
                                over 27 ( ESC ) = or 0=
                while   drop beep
                repeat  dup 27 <>
                cursor-on
                if      bl or 'a' - 0MAX hdepth swap - 1+ >r
                        screenline =: linesave
                        off> leavesave
                        sesc            \ leave this edit
                        ed1>hstack      \ push this edit on stack
                        r> 0
                        ?do     hrotate \ rotate bottom of file stack to top
                        loop
                else    drop scrshow
                then
        then    restore> dobutton ;

\ 212 fnset flipfiles             \ Shift-F1

: nxtbrowse     beep ;          \ Alt-N

forth definitions

