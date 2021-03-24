\ DECOM.SEQ     The F-PC decompiler         Enhancements by Tom Zimmer

\    A Forth decompiler is a utility program that translates
\ executable forth code back into source code.  Normally this is
\ impossible, since traditional compilers produce more object
\ code than source, but in Forth it is quite easy.  The decompiler
\ is almost one to one, failing only to correctly decompile the
\ various Forth control stuctures and special compiling words.
\ It was written with modifiability in mind, so if you add your
\ own special compiling words, it will be easy to change the
\ decompiler to include them.  This code is highly implementation
\ dependant, and will NOT work on other Forth system.  To invoke
\ the decompiler, use the word SEE <name> where <name> is the
\ name of a Forth word.

ONLY FORTH ALSO DEFINITIONS HIDDEN ALSO

0 value showbase                \ base to use while showing a number

: DECOM.        ( n1 -- )       \ display number n1 in a selected base
                save> base
                showbase ?dup
                if      27 min 2 max base !
                then    base @ $0A =
                if      .
                else    u.
                then
                restore> base ;

: 0TAB          ( -- )          \ left margin goes to left edge of screen
                0 LMARGIN ! ;

: +TAB          ( --- )
                LMARGIN @ TABSIZE @ + DUP LMARGIN !
                RMARGIN @ TABSIZE @ - >
                IF      TABSIZE @ LMARGIN !
                THEN    ;

: -TAB          ( --- )
                LMARGIN @ TABSIZE @ - 0MAX DUP LMARGIN !
                TABSIZE @ <
                IF      RMARGIN @ TABSIZE @ - 0MAX LMARGIN !
                THEN    ;

: CRTAB         RMARGIN @ ?LINE ;

ONLY FORTH ALSO HIDDEN DEFINITIONS ALSO

headerless

 0 VALUE DECOMSEG
 0 VALUE ?DEBUG
19 VALUE SPLIT-L#
19 VALUE SCROLL-L#
 0 value PREVLIST

: SRCEEOLCR    EEOL CRLF ;

: MAX-SPLIT     ( -- n1 )
                ROWS 4 - ;

: INIT-SPLIT    ( --- )         \ initialize the split line as 6 lines up
                DEFERS INITSTUFF
                ROWS DUP 4 / - DUP =: SPLIT-L# =: SCROLL-L# ;

' INIT-SPLIT IS INITSTUFF

: DECOMSEG@     ( N1 --- )
                DECOMSEG SWAP @L ;

: ASSOCIATIVE:
   CONSTANT
   DOES>         ( N -- INDEX )
      DUP @ ( N PFA CNT )   -ROT DUP @ 0 ( CNT N PFA CNT 0 )
      DO   2+   2DUP @ = ( CNT N PFA' BOOL )
         IF 2DROP DROP   I 0 0   LEAVE   THEN
            ( CLEAR STACK AND RETURN INDEX THAT MATCHED )
      LOOP   2DROP   ;

: .WORD         ( IP -- IP' )
                DUP DECOMSEG@ >NAME YC@ 64 AND
                IF      DUP YC@ 31 AND 10 + ?LINE
                        ." [COMPILE] "
                THEN    DUP DECOMSEG@ >NAME.ID   2+   ;

: (LIT+)        ( IP -- IP' )    6 ?LINE 4 + ;

: .LIT          ( IP -- IP' )    (LIT+) DUP 2- DECOMSEG@ DECOM. ;

: .[']          ( IP -- IP' )    CRTAB ." ['] " 2+ ;

: .IS           ( IP -- IP' )    ." IS " 2+ ;

: .IF           ( IP -- IP' )    CRTAB ." IF " (LIT+) TAB +TAB ;

: .ELSE         ( IP -- IP' )    -TAB CRTAB ." ELSE " (LIT+) TAB +TAB ;

: .CASE         ( IP -- IP' )    CRTAB ." CASE " 2+ TAB ;

: .OF           ( IP -- IP' )    CRTAB ." OF " (LIT+) TAB +TAB ;

: .ENDOF        ( IP -- IP' )    -TAB CRTAB ." ENDOF " (LIT+) TAB ;

: .ENDCASE      ( IP -- IP' )    CRTAB ." ENDCASE " 2+ TAB ;

: .DO           ( IP -- IP' )    CRTAB ." DO  " (LIT+) TAB +TAB ;

: .?DO          ( IP -- IP' )    CRTAB ." ?DO  " (LIT+) TAB +TAB ;

: .LOOP         ( IP -- IP' )    -TAB CRTAB ." LOOP " (LIT+) TAB ;

: .+LOOP        ( IP -- IP' )    -TAB CRTAB ." +LOOP " (LIT+) TAB ;

: .WHILE        ( IP -- IP' )    -TAB CRTAB ." WHILE " (LIT+) TAB +TAB ;

: .REPEAT       ( IP -- IP' )    -TAB CRTAB ." REPEAT " (LIT+) TAB ;

: .UNTIL        ( IP -- IP' )    -TAB CRTAB ." UNTIL " (LIT+) TAB ;

: .AGAIN        ( IP -- IP' )    -TAB CRTAB ." AGAIN " (LIT+) TAB ;

: .BEGIN        ( IP -- IP' )    CRTAB 2+ ." BEGIN " TAB +TAB ;

: .THEN         ( IP -- IP' )    -TAB CRTAB 2+ ." THEN " TAB ;

: .QUOTE        ( IP -- IP' )    .WORD   .WORD ;

                \ Print the string at offset n1, and adjust n1 to the
                \ end of the string, while aligning it. Prepend a "
                \ space, and append a " space to the string
: ."X$"         ( N1 --- N1+LEN )
                DUP '"' FEMIT SPACE
                DECOMSEG SWAP 2DUP C@L 1+ >R ?CS: "BUF R@ CMOVEL
                R> DUP 1 AND + + "BUF COUNT TYPE '"' FEMIT SPACE ;

: .STRING."     ( IP -- IP' )
                2+ DECOMSEG OVER C@L 5 + ?LINE
                '.' FEMIT ."X$" ;

: .STRING"      ( IP -- IP' )
                2+ DUP 2+ SWAP DECOMSEG@ DUP C@ 4 + ?LINE
                '"' FEMIT SPACE
                COUNT TYPE
                '"' FEMIT SPACE ;

: .STRING""     ( IP -- IP' )
                2+ DECOMSEG OVER C@L 5 + ?LINE
                '"' FEMIT ."X$" ;

: .ABORT"       ( IP -- IP' )
                2+ DUP DECOMSEG@ C@ 10 + ?LINE
                ." ABORT" ."X$" ;

: .(;CODE)    ( IP -- IP' )
                .WORD   DOES?
                IF  ." DOES> "
                ELSE  DROP FALSE  THEN  ;

: .UNNEST     ( IP -- IP' )
                ." ; "   DROP   0   ;

: .FINISH     ( IP -- IP' )
                .WORD   DROP   0   ;

27 ASSOCIATIVE: EXECUTION-CLASS
   (  0 ) '   (LIT)        ,         (  1 ) '   ?BRANCH      ,
   (  2 ) '   BRANCH       ,         (  3 ) '   (LOOP)       ,
   (  4 ) '   (+LOOP)      ,         (  5 ) '   (DO)         ,
   (  6 ) '   COMPILE      ,         (  7 ) '   (.")         ,
   (  8 ) '   (ABORT")     ,         (  9 ) '   (;CODE)      ,
   ( 10 ) '   UNNEST       ,         ( 11 ) '   (")          ,
   ( 12 ) '   (?DO)        ,         ( 13 ) '   (;USES)      ,
   ( 14 ) '   ?UNTIL       ,         ( 15 ) '   ?WHILE       ,
   ( 16 ) '   DOAGAIN      ,         ( 17 ) '   DOREPEAT     ,
   ( 18 ) '   DOBEGIN      ,         ( 19 ) '   DOTHEN       ,
   ( 20 ) '   (X")         ,         ( 21 ) '   <'>          ,
   ( 22 ) '   (IS)         ,         ( 23 ) '   (OF)         ,
   ( 24 ) '   DOENDOF      ,         ( 25 ) '   DOCASE       ,
   ( 26 ) '   DOENDCASE    ,

: .EXECUTION-CLASS      ( N1 --- )
                0MAX 27 MIN EXEC:
                (  0 )     .LIT         (  1 )     .IF
                (  2 )     .ELSE        (  3 )     .LOOP
                (  4 )     .+LOOP       (  5 )     .DO
                (  6 )     .QUOTE       (  7 )     .STRING."
                (  8 )     .ABORT"      (  9 )     .(;CODE)
                ( 10 )     .UNNEST      ( 11 )     .STRING"
                ( 12 )     .?DO         ( 13 )     .FINISH
                ( 14 )     .UNTIL       ( 15 )     .WHILE
                ( 16 )     .AGAIN       ( 17 )     .REPEAT
                ( 18 )     .BEGIN       ( 19 )     .THEN
                ( 20 )     .STRING""    ( 21 )     .[']
                ( 22 )     .IS          ( 23 )     .OF
                ( 24 )     .ENDOF       ( 25 )     .CASE
                ( 26 )     .ENDCASE     ( 27 )     .WORD      ;

0 VALUE PFALINE
0 VALUE DIDPFA
0 VALUE TOPCRS
0 VALUE DUMMYCRS
0 VALUE #EMPTY

: TOPCR         ( --- )
                DUMMYCRS
        IF      DECR> DUMMYCRS
                OFF> #OUT
        ELSE    #LINE @ SPLIT-L# 2- >=
                IF      SPLIT-L# 1- SAVE!> ROWS \ save ROWS and set to split
                        0 2 AT                  \ move to third line
                        -LINE                   \ scroll upper portion
                        RESTORE> ROWS           \ restore ROWS
                        0 SPLIT-L# 2- AT        \ move to split line
                ELSE    SRCEEOLCR
                THEN
        THEN    INCR> TOPCRS ;

: .PFA          ( LIST_SEGMENT -- )
                DUP PREVLIST <>                 \ reset when new definition
                IF      OFF> #EMPTY
                        DUP !> PREVLIST
                THEN
                >BODY   @ +XSEG =: DECOMSEG 0
                SAVESTATE
                0TAB +TAB
                COLS 10 - RMARGIN !
                #LINE @ =: TOPCRS
                SAVE> CR
                ?DEBUG
                IF      ['] TOPCR IS CR
                        #EMPTY =: DUMMYCRS
                THEN
                OFF> PFALINE
                OFF> DIDPFA
                BEGIN   ?CR
                        DUP PFASAV @ OVER = ?DEBUG AND
                        IF      >ATTRIB4 ON> ?DEFATTRIB
                                TOPCRS =: PFALINE
                                PFALINE SPLIT-L# 4 - 0MAX - 0MAX !> #EMPTY
                                ON> DIDPFA
                        THEN
                        DECOMSEG@ EXECUTION-CLASS .EXECUTION-CLASS >NORM
                        OFF> ?DEFATTRIB
                        DUP 0= KEY? OR
                        ?DEBUG
                        IF
                                #LINE @ SPLIT-L# 2- >=  \ hit bottom
                                TOPCRS pfaline 3 + > and
                                didpfa and
                                IF      drop true
                                THEN
                        THEN
                UNTIL   DROP
                RESTORE> CR
                RESTORESTATE ;

: .IMMEDIATE   ( CFA -- )
                >NAME YC@ 64 AND
                IF      ." IMMEDIATE"   THEN   ;

: .CONSTANT     ( CFA -- )
                DUP >BODY ?   ." CONSTANT "   >NAME.ID   ;

: .VALUE        ( CFA -- )
                DUP >BODY ?   ." VALUE "      >NAME.ID   ;

: .VARIABLE     ( CFA -- )
                DUP C@ 232 =
                IF      DUP >BODY .   ." VARIABLE "   DUP >NAME.ID
                        ." Value = " >BODY ?
                ELSE    >NAME.ID  THEN ;

: .:            ( CFA -- )
                ." : "  DUP >NAME .ID CR TAB .PFA   ;

: .DOES>        ( BODY -- )
                DUP>R BODY> @REL>ABS DUP R@ 2+ =  \ Self defining word
                IF      R@ @ >NAME .ID
                ELSE    DUP >.ID
                THEN    R>DROP ." DOES> " .PFA   ;

: .USER-VARIABLE   ( CFA -- )
                DUP >BODY ?   ." USER VARIABLE "   DUP >NAME.ID
                ." Value = "   >IS  ?   ;


: .DEFER        ( CFA -- )
                ." DEFERRED " DUP >NAME.ID   ." IS "  >IS @ (SEE)  ;

: .USER-DEFER   ( cfa -- )
   ." USER DEFERRED "   DUP >NAME.ID  ." IS "  >IS @ (SEE)  ;

: .OTHER   ( CFA -- )
        DUP     >NAME.ID
        DUP C@  232 <>                  \ cfa doesn't contain a call for code
        IF      DROP    ." is Code, load DISASSEM to see it."
                                        EXIT
        THEN
        DUP DOES?                       \ Is this a DOES> word?
        IF      .DOES>  DROP            EXIT
        THEN    2DROP   ." is Unknown"   ;

headers

7 CONSTANT MAX-CLASSES

MAX-CLASSES ASSOCIATIVE: DEFINITION-CLASS
   ( 0 )   '      QUIT @REL>ABS ,   ( 1 )   '  #VOCS @REL>ABS ,
   ( 2 )   '     STATE @REL>ABS ,   ( 3 )   '   BASE @REL>ABS ,
   ( 4 )   '        CR @REL>ABS ,   ( 5 )   '   EMIT @REL>ABS ,
   ( 6 )   '  DECOMSEG @REL>ABS ,

: .DEFINITION-CLASS     ( N1 --- )
                0MAX MAX-CLASSES MIN EXEC:
                ( 0 )     .:            ( 1 )     .CONSTANT
                ( 2 )     .VARIABLE     ( 3 )     .USER-VARIABLE
                ( 4 )     .DEFER        ( 5 )     .USER-DEFER
                ( 6 )     .VALUE        ( 7 )     .OTHER      ;

: ((SEE))       ( Cfa -- )
                SAVE> ATTRIB
                CR   DUP DUP @REL>ABS
                DEFINITION-CLASS
                .DEFINITION-CLASS
                .IMMEDIATE
                RESTORE> ATTRIB ;

' ((SEE)) IS (SEE)

FORTH DEFINITIONS

: SEE           ( | name -- )
                '   (SEE) ;

