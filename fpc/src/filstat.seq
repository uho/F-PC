\ FILSTAT       Display file status, and loaded files.  by Tom Zimmer

decimal

: .LOADED       ( --- ) \ Print a list of all files that have been loaded.
                cr also files words previous ;

: .FILES        ( --- ) \ Print the contents of the system handles.
                hndls maxnest bounds
                ." \n\s11___Filename___\s11Status"
                do      cr i seqhandle =
                        if      ." Current - "
                        else    ." \s08- "
                        then    i count ?dup
                        if      type
                        else    drop ." .....No File....."
                        then    26 i c@ ?dup 0=
                                if      17 then - spaces
                                i >hndle @ 0<
                        if      i c@
                                if      ." Closed"
                                else    ." ......"
                                then
                        else            ." *Open*"
                        then
         b/hcb +loop    cr ;


