\ HTYPE.SEQ     Hyper Type, selectively erases or bolds text    T. Zimmer

: hypertypeL    ( SEG a1 n1 --- )
                ?dosio if (typeL) exit then
                dup 0>
        if      ROT SAVE!> SSEG
                over @> SSEG swap c@L hyperdest =    \ (ø) 248 char
                if      1 /string
                        2dup bl scan nip over swap -
                        dup 1+ spaces /string
                then                            \ scan for hypertext char
                begin   2dup hyperchar scan nip ?dup
                                                \ if found, show following
                while   over >r - dup>r over >r \ word in reverse video
                        @> SSEG -ROT defers typeL
                        r> r> r> swap /string
                        space 1 /string         \ strip off hyperchar to
                        >rev
                        2dup bl scan nip
                        over >r - dup>r over >r
                        @> SSEG -ROT defers typeL
                        r> r> r> swap /string
                        >norm
                repeat  @> SSEG -ROT defers typeL
                        restore> sseg
        else    2drop DROP
        then    ;

\ ***************************************************************************
\ Words to insert the hyper text characters

: hsrcschr      ( --- )         \ mark the source of a hypertext link
                rchr lwrd
                hyperchar schr ;

\ 234 fnset hsrcschr              \ Alt-F3

: hdstschr      ( --- )         \ mark the destination of a hypertext link
                shoml
                true save!> imode
                hyperdest schr
                restore> imode ;

\ 236 fnset hdstschr              \ Alt-F5

