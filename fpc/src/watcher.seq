\ WATCHER.SEQ           Watch window for debugger       by Tom Zimmer

\   Created at the suggestion of Jerry Boutelle

\   Links into the debugger through the words: .WATCH, .WATCHER & SETWATCH
\ to provide a way to have a small area of memory watched while you step
\ through a program.

only forth also definitions hidden also

headerless

 0 value watch_segment
-1 value watch_address

: .watch        ( -- )
                defers .defsrc
                watch_address -1 = ?exit        \ if not watching, leave
                savecursor >attrib3
                save> base hex                  \ dump is in HEX
                save> dumpseg
                watch_segment dumpseg !         \ dump the watch segment
                watch_address
                max-split 1+ split-l# over min
                ?do     0 i at eeol
                        0 i 1- at dup dln 16 +
                loop    drop
                restore> dumpseg
                restore> base                   \ restore the base
                restcursor ;

' .watch is .defsrc

0 value depth_save

32 array stibbuf

: watch_cmd     ( -- f1 )
                tib 31 expect  span @ #tib ! >in off
                bl word  skip'c' ?uppercase
                c@ 0= dup ?exit drop            \ leave if empty input
                here 1+ c@ ''' <>
                if      here find
                        if      !csp execute
                                sp@ csp @ =
                                if      !> watch_address true
                                        ?cs: !> watch_segment
                                else    sp0 @ sp!       \ clear stack
                                        0 0 0 0
                                        beep false
                                then
                        else    %number nip
                                if      !> watch_address true
                                        bl word ?uppercase c@
                                        if      here %number nip
                                                if      watch_address
                                                        !> watch_segment
                                                        !> watch_address
                                                else drop
                                                then
                                        then
                                else    drop false
                                then
                        then
                else    here count 1 /string here place
                        bl here count + c!      \ add trailing blank
                        here find
                        if      !> watch_address true
                        else    drop beep false
                        then
                then    ;

: setwatch      ( -- )
                savecursor
                watch_segment 0=
                if      ?cs: !> watch_segment
                then
                depth dup !> depth_save
                begin   dup 0>
                while   1- swap >r
                repeat  drop 0 0 0 0    \ some dummy parameters
                0 split-l# at >attrib3
                ."  Enter watch WORD, 'WORD or addr or seg addr [-1=remove]:"
                stibbuf save!> 'tib
                save> #tib #tib off
                save> >in
                at? 2>r
                begin   2r@ at eeol 2r@ at
                        watch_cmd
                until   2r> 2drop
                restore> >in
                restore> #tib
                restore> 'tib
                depth 0 ?do drop loop           \ clear dummy parameters
                begin   depth_save 0>           \ recover stack contents
                while   r> -1 +!> depth_save
                repeat
                restcursor ;

: watch-commands ( c1 f1 -- c1 f2 )
                [ bug ]
                defers extra-commands
                over ?upc 'W' = if setwatch              0= then
                watch_address -1 <>             \ if not watching, leave
        IF      over     200 = if -16 +!> watch_address 0= then \ Up arrow
                over     208 = if  16 +!> watch_address 0= then \ Down arrow
                over     201 = if  -4 +!> watch_segment 0= then \ PgUp
                over     209 = if   4 +!> watch_segment 0= then \ PgDn
                max-split 1+ !> scroll-l#
        ELSE    split-l# !> scroll-l#
        THEN    ;

' watch-commands is extra-commands

headers

