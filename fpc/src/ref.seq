\ REF.SEQ       Reference word REF              by Leon Dent

comment:

  Used in the form  REF <word>

  REF hunts out references of <word> in all occurences of colon words

  Much of this file has been cannibalized from the files DECOM.SEQ and
  WORDS.SEQ, also a few words ( .VYET, EXECUTION-CLASS, ?KEYPAUSE etc )
  are re-used so the above files must be loaded.

  REF           captures CFA of word
  (REF)         searches from vocabulary to vocabulary
  .RVOCWORDS    searches within each vocabulary
  R.NAME        searches within each colon word, prints out matches

   ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
   ³ Modified & optimized by Tom Zimmer for F-PC ³
   ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

comment;

only forth also hidden also definitions

headerless

0 value colseg

0 value REFCFA                  \ holds cfa we're hunting for

: %r.name       ( link cfa --- link cfa )
                .VYET 17 ?LINE over L>NAME  .ID         \ found it
                #OUT @ 64 < IF TAB THEN
                TOTALWORDS INCR ;

: R.NAME  ( LINK -- )   \ looks at COLON, DEFER, an USER DEFER words
        DUP LINK> DUP @REL>ABS 'DOCOL = \ look through ":" defs
        IF      dup >BODY @ +XSEG =: sseg
                                \ first look for end of definition
                0 $140 ['] unnest scanw 0= if drop $40 then 2/
                                \ then look for word we are referencing
                0 swap refcfa scanw nip
                if      %r.name
                then    ?cs: =: sseg
        ELSE    dup @rel>abs ['] bgstuff @rel>abs =     \ and DEFERed words
                if      dup >body @ refcfa =
                        if      %r.name
                        then
                else    dup @rel>abs ['] key @rel>abs = \ and USER DEFERed
                        if      dup >is @ refcfa =
                                if      %r.name
                                then
                        then
                then
        THEN    2DROP   ;

: .RVOCWORDS  ( ADDR -- )
        DUP HERE 500 + #THREADS 2* CMOVE   \ copy threads
        BODY> >NAME VADDR ! VYET OFF
        BEGIN   HERE 500 + #THREADS LARGEST DUP  \ search thread copy
                ?KEYPAUSE
        WHILE   DUP R.NAME Y@ SWAP !    \ insert last link into thread
        REPEAT
        2DROP  ;

: (REF) ( -- )
        TOTALWORDS OFF
        savestate
        COLS 2- RMARGIN !
        15 TABSIZE !
        2  LMARGIN !
        CR  ."  *  Press SPACE to pause, or ESC to exit  *"
        VOC-LINK @
        BEGIN
                DUP #THREADS 2* -
                .RVOCWORDS
                @ DUP 0=
        UNTIL
        DROP
        CR TOTALWORDS @ U. ." Words printed" CR
        restorestate ;

headers

forth definitions

: REF   ( | name --- )
        ' =: REFCFA (REF)  ;

' ref alias XREF
' ref alias USEDIN
' ref alias CALLS

only forth also definitions

