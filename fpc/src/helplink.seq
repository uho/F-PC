\ HELPLINK.SEQ          link in Help to Editor          by Tom Zimmer

editor definitions

: helpF1        ( --- )                 \ F1 in editor
                on> browseset
                " edmain" ">$ here over c@ 1+ cmove
                bl here count + c!
                check-ndx
                if      line-ed/br
                else    drop
                        true " \4 HELP is not available " ?softerror
                        scrshow
                then    ;

\ 187 fnset helpF1        \ function value for F1

: F1help        ( | word --- )          \ F1 at Forth level
                on> newbrowse
                on> ?browse
                off> seding
                off> newfl
                on> leavenow
                sequp
                savecursor
                savescr
                " edmain" ">$ here over c@ 1+ cmove
                bl here count + c!
                check-ndx
                if      hndlsave file-line_view
                else    drop
                        true " \4 HELP is not available " ?softerror
                then    emptykbd
                off> leavenow
                restscr
                restcursor
                seqdown ;

' F1help is dofhelp             \ link into F1 key handler

forth definitions

' F1help alias F1

