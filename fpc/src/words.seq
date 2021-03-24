\ WORDS.SEQ     The WORDS definition       Enhancements by Tom Zimmer

headerless

VARIABLE VYET   \ DID WE PRINT VOCABULARY YET
VARIABLE VADDR  \ VOCABULARY NAME ADDRESS

: .VYET         ( --- ) VYET @ IF EXIT THEN
                VADDR @ CR ." --[ " .ID ."  ]--" VYET ON
                CR LMARGIN @ SPACES  ;

VARIABLE TOTALWORDS
DEFER W.ID ' .ID IS W.ID

CREATE W$ 64 ALLOT
       W$ 64 ERASE

: ?INNAME       ( NFA --- F1 )
                @> YSEG SWAP ?CS: HERE 31 CMOVEL
                $C0 HERE CRESET                 \ mask to real count only
                $80 HERE COUNT + DUP OFF        \ clear end of name to NULL
                1- CRESET                       \ mast high bit of last char
                W$      COUNT HERE COUNT SEARCH NIP
                W$ 32 + COUNT HERE COUNT SEARCH NIP AND ;

: ?CODENAME     ( NFA --- F1 )
                NAME> C@ DUP ( CALL) 232 <> SWAP ( JMP) 233 <> AND ;

0 VALUE WORDTYPE

: ?WORDTYPE     ( NFA --- F1 )
                NAME> @REL>ABS WORDTYPE = ;

: ?TOTALWORDS   ( NFA --- FALSE )
                DROP FALSE
                TOTALWORDS INCR ;

DEFER ?W.NAME

: <W.NAME>      ( NFA --- )     \ Print name filtered by HERE
                DUP ?W.NAME
                IF      .VYET 17 ?LINE W.ID
                        #OUT @ COLS 16 - < IF TAB THEN
                        TOTALWORDS INCR
                ELSE    DROP THEN    ;

DEFER W.NAME    ' <W.NAME> IS W.NAME

: .VOCWORDS     ( A1 --- )
                DUP HERE 500 + #THREADS 2* CMOVE
                BODY> >NAME VADDR !     VYET OFF
                BEGIN   HERE 500 + #THREADS LARGEST DUP
                        ?KEYPAUSE
                WHILE   DUP L>NAME W.NAME Y@ SWAP !
                REPEAT  2DROP ;

DEFER ?W.TEST   ' NOOP IS ?W.TEST

headers

: ?*.*          ( --- )
                W$ 1+ " *.*" COMP 0=
                IF      ['] NOOP IS ?W.NAME
                THEN    ;

: ?CODE.*       ( --- )
                W$ 1+ " CODE.*" CAPS-COMP 0=
                IF      ['] ?CODENAME IS ?W.NAME
                THEN    ;

: ?:.*          ( --- )
                W$ 1+     " :.*" CAPS-COMP 0=
                W$ 1+ " COLON.*" CAPS-COMP 0= OR
                IF      ['] ?*.* @REL>ABS =: WORDTYPE
                        ['] ?WORDTYPE IS ?W.NAME
                THEN    ;

: ?VARIABLE.*   ( --- )
                W$ 1+ " VARIABLE.*" CAPS-COMP 0=
                IF      ['] TOTALWORDS @REL>ABS =: WORDTYPE
                        ['] ?WORDTYPE IS ?W.NAME
                THEN    ;

: ?USER-VARIABLE.* ( --- )
                W$ 1+ " USER-VARIABLE.*" CAPS-COMP 0=
                IF      ['] BASE @REL>ABS =: WORDTYPE
                        ['] ?WORDTYPE IS ?W.NAME
                THEN    ;

: ?CONSTANT.*   ( --- )
                W$ 1+ " CONSTANT.*" CAPS-COMP 0=
                IF      ['] BL       @REL>ABS =: WORDTYPE
                        ['] ?WORDTYPE IS ?W.NAME
                THEN    ;

: ?VALUE.*      ( --- )
                W$ 1+ " VALUE.*" CAPS-COMP 0=
                IF      ['] WORDTYPE @REL>ABS =: WORDTYPE
                        ['] ?WORDTYPE IS ?W.NAME
                THEN    ;

: ?DEFERED.*    ( --- )
                W$ 1+ " DEFERED.*" CAPS-COMP 0=
                IF      ['] ?W.TEST @REL>ABS =: WORDTYPE
                        ['] ?WORDTYPE IS ?W.NAME
                THEN    ;

: ?USER-DEFERED.* ( --- )
                W$ 1+ " USER-DEFERED.*" CAPS-COMP 0=
                IF      ['] EMIT @REL>ABS =: WORDTYPE
                        ['] ?WORDTYPE IS ?W.NAME
                THEN    ;

: ?TOTAL.*      ( --- )
                W$ 1+ " TOTAL.*" CAPS-COMP 0=
                IF      CR ." Not displaying, just counting the TOTAL "
                        ['] ?TOTALWORDS IS ?W.NAME
                THEN    ;

headerless

FALSE VALUE CONTEXTONLY         \ display only words in context vocabulary

headers

DEFER PREWORDS  ' NOOP IS PREWORDS

\       ******** WORDS enhancements by Tom Zimmer ********

\ WORDS *.*             display all words of all vocabularies.
\ WORDS CODE.*          display CODE words of all vocabularies.
\ WORDS :.*             display : (colon) words of all vocabularies.
\ WORDS VARIABLE.*      display VARIABLEs of all vocabularies.
\ WORDS CONSTANT.*      display CONSTANTs of all vocabularies.
\ WORDS VALUE.*         display VALUEs of all vocabularies.
\ WORDS DEFERED.*       display DEFEREDs of all vocabularies.
\ WORDS USER-VARIABLE.* display USER VARIABLEs of all vocabularies.
\ WORDS USER-DEFERED.*  display USER DEFEREDs of all vocabularies.
\ WORDS TOTAL.*         display the total count of all words in all vocs.
\ WORDS <return>        display words in current vocabulary.
\ WORDS <string>        display words containing string in all vocabularies.
\ WORDS <str1> <str2>   display words containing str1 & str2 of all vocs.
\ THESE WORDS <string>  display words containing string in this vocabulary.

 : WORDS       ( <t1> -- )
                TOTALWORDS OFF
                SAVESTATE
                COLS 2- RMARGIN !
                15 TABSIZE !
                LMARGIN OFF
                CR ."  ** Press SPACE to pause, or ESC to exit ** "
                PREWORDS
                >IN @ #TIB @ <>
                IF      ['] ?INNAME IS ?W.NAME
                        BL WORD W$      OVER C@ 1+ 32 MIN CMOVE
                        BL WORD W$ 32 + OVER C@ 1+ 32 MIN CMOVE
                        ?*.*            ?CODE.*         ?:.*
                        ?VARIABLE.*     ?CONSTANT.*     ?DEFERED.*
                        ?VALUE.*        ?USER-VARIABLE.*
                        ?USER-DEFERED.* ?TOTAL.*
                        CONTEXTONLY
                        FALSE =: CONTEXTONLY
                        IF      CONTEXT @ .VOCWORDS
                        ELSE    VOC-LINK @
                                BEGIN   DUP #THREADS 2* - .VOCWORDS
                                        @ DUP 0=
                                UNTIL   DROP
                        THEN
                ELSE    ['] NOOP IS ?W.NAME
                        FALSE =: CONTEXTONLY
                        CONTEXT @ .VOCWORDS
                THEN    CR TOTALWORDS @ U. ." Words displayed" CR
                RESTORESTATE ;

\       Example:  THESE WORDS XYZ <enter>
\       will display all words in the CONTEXT vocabulary containing XYZ

: THESE         ( --- )         \ Preceeds WORDS to subset CONTEXT vocabulary
                TRUE !> CONTEXTONLY ;

ROOT DEFINITIONS

' WORDS ALIAS WORDS

FORTH DEFINITIONS

