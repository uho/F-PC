\ LISTSET.SEQ   Allow adjusting the LIST segment of F-PC

\ Assure #LISTSEGS has at least "segments" of free memory and then
\ re-save F-PC back to disk where it came from with the same name if
\ we needed to change the segments available.

: LISTSET       ( segments -- )
                #listsegs >r
                1 ?ENOUGH
                200 UMAX
                XDPSEG @ XSEG @ - + =: #LISTSEGS        \ adj #LISTSEGS
                #listsegs r> <>                         \ if not the same
        if      cr ." Re-saving with adjusted LIST space of "
                #listsegs h. ."  to >> "
                ME@     ME$ C@ 0>                       \ can i find my name?
                IF      ME$                             \ return it if i can
                ELSE    " F.EXE" ">$                    \ or F.EXE if i can't
                THEN
                dup count type
                $>TIB                                   \ send it to TIB
                FSAVE                                   \ re-save Forth
        else    cr ." No save needed, listseg correct."
        then    cr bye ;


