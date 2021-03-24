\\ WINDEX.SEQ    Make an index of Forth words.           by Tom Zimmer

Displays a more or less alphabetized list of Forth words showing
vocabulary and file where it was defined.

Just type  "ALLWORDS <enter>"  to display an index of all forth words.

Use  ">BROWSER ALLWORDS <enter>"  to create a printable file.

{

VARIABLE WADDR
0 value FIRSTLETTER
0 value MAXCFA
0 value MAXNAME

: files_set     ( --- )
                ['] files >body here 500 + #threads 2* cmove ;

: 1file         ( --- false | nfa )
                here 500 + #threads largest dup
                if      dup l>name >r y@ swap ! r>
                else    nip
                then    ;

: >wviewfile     ( cfa --- offset a1 )
                >r files_set off> maxcfa off> maxname
                begin   1file dup
                while   r@ over name> u>
                        if      dup name> maxcfa u>
                                if      dup =: maxname
                                        dup name> =: maxcfa
                                then
                        then    drop
                repeat  drop r> >view y@
                maxname name>pad ;

: ?\comment     ( a1 n1 -- )    \ display if a comment line
                2dup bl skip drop c@ '\' =
                if      type cr
                else    2drop
                then    ;

: W.ANAME       ( NFA --- )
                DUP 1+ YC@ 127 AND FIRSTLETTER =
                IF      CR DUP  .ID                     24 sp>col
                        WADDR @ .ID                     36 sp>col
                        NAME> >wviewfile
                        2DUP count type                 49 sp>col
                        55 / ."   Page:" 1+ 3 .r
                        CR
                        $file 0=
                        if      1- 0max >line
                                lineread count 2- 0max ?\comment
                                lineread count 2- 0max          type    cr
                                lineread count 2- 0max ?\comment
                                lineread count 2- 0max ?\comment
                        else    drop
                        then
                ELSE    DROP THEN    ;

: .AVOCWORDS    ( A1 --- )      \ A1 = BODY of vocabulary
                DUP HERE 1000 + #THREADS 2* CMOVE
                BODY> >NAME WADDR !
                BEGIN   HERE 1000 + #THREADS LARGEST DUP
                        ?KEYPAUSE
                WHILE   DUP L>NAME W.ANAME Y@ SWAP !
                REPEAT  2DROP ;

: <awords>      ( c1 --- )
                =: firstletter
                VOC-LINK @
                BEGIN   DUP #THREADS 2* - .AVOCWORDS @ DUP 0=
                UNTIL   DROP ;

: ALLWORDS      ( --- )
                ASCII a BL 1+
                DO      CR CR ." Words starting with "  I EMIT CR
                        I <AWORDS>
                LOOP ;

