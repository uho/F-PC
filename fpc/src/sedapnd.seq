\ SEDAPND.SEQ   Append text to TEMP.SEQ or user specified file.

editor definitions

: append        ( --- )
                marking 0=
                if      .nomark exit
                then    "  Append marked text to filename: " ">$ =: export$
                savescr
                ?getexpfile ?exit
                ."  Appending text to " ed2hndl count type ." ..." >norm
                sdln                            \ move down a line in file
                read-write                      \ open for append
                ed2hndl hopen                   \ open if possible
                if      ed2hndl hcreate 0=      \ or make if needed
                else    true
                then
                if      ed2hndl endfile 2drop
                        off> wblen
                        markend 1+ markstrt
                        do      i linewrite ?leave
                        loop    flushwrite      ( --- f1 )
                        if      ." Failed, Disk FULL " beep 2 seconds
                        then    ed2hndl hclose
                        " \4 Error Closing File " ?softerror
                        mark-clear
                else    true
                        " \4 Failed, Couldn't OPEN or CREATE file "
                        ?softerror
                then    restscr scrshow suln ;

' append is appendx     \ patch function into SED

forth definitions

