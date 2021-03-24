\ KERNEL3.SEQ   More kernel stuff

FILES DEFINITIONS

VARIABLE KERNEL3.SEQ

FORTH DEFINITIONS

: >TYPE         ( adr len -- )
                TUCK PAD SWAP CMOVE   PAD SWAP TYPE  ;

: .(            ( -- )  ASCII ) PARSE >TYPE  ; IMMEDIATE

: (             ( -- )  ASCII ) PARSE 2DROP  ; IMMEDIATE

CODE TRAVERSE   ( addr direction -- addr' )
                POP CX          POP BX
                ADD BX, CX      PUSH ES
                MOV ES, YSEG
          BEGIN
                MOV ES: AL, 0 [BX]      AND AL, # 128
       0= WHILE
                ADD BX, CX
          REPEAT
                POP ES          PUSH BX
                NEXT            END-CODE

: PLACEL        ( seg off len a1 -- )           \ get string from far mem
                                                \ make into counted string
                2DUP C! 1+ ?DS: SWAP ROT CMOVEL ;

: +PLACEL       ( seg off len a1 -- )           \ append far string to a1
                2DUP 2>R COUNT + ?DS: SWAP ROT CMOVEL 2R> C+! ;

CODE DONE?      ( n -- f )
                POP AX
                CMP AX, STATE
            0<> IF
                        MOV END? # 0 WORD
                        MOV AX, # -1
                        1PUSH
                THEN
                PUSH END?
                MOV END? # 0 WORD
                NEXT
                END-CODE

\ : DONE?         ( n -- f )
\                 STATE @ <>   END? @ OR   END? OFF   ;

: CNHASH        ( cfa -- ya )
                $0FE00 AND FLIP ;

CODE CNSRCH     ( cfa ya maxya -- nfa failf )
                pop dx          \ maxya
                pop bx          \ ya
                add bx, # 4
                pop di          \ cfa
                mov ds, yseg
\        HERE    cmp dx, bx
        0 $:    cmp dx, bx
             U> IF      mov ax, 0 [bx]
                        and ax, # 31
                        add bx, ax
                        inc bx
                        mov ax, 0 [bx]
                        cmp ax, di      \ if they match, then we found it
                     0= if      sub bx, # 2             \ 1 before last chr
                                begin   mov al, 0 [bx]  \ test high bit
                                        and al, # 128   \ loop till high set
                             0= while   dec bx          \ backup one char
                                repeat
                                push bx                 \ push pointer to chr
                                mov ax, cs              \ restore DS
                                mov ds, ax
                                mov ax, # false         \ push false flag
                                1push
                        then
                        add bx, # 6     \ step to next header
\                        JMP ROT         \ bring HERE around Branch resolution
                        jmp 0 $
                                        \ used by IF and THEN
                THEN
                mov ax, cs      mov ds, ax
                mov ax, # true
                push ax
                1push           end-code

: N>LINK        ( anf -- alf)
                2-   ;

: L>NAME        ( alf -- anf )
                2+   ;

: BODY>         ( apf -- acf )
                3 -  ;

: NAME>         ( anf -- acf )
                1 TRAVERSE   1+ Y@  ;

: LINK>         ( alf -- acf )
                L>NAME   NAME>   ;

: >BODY         ( acf -- apf )
                3 +  ;

HERE-Y 4 +     \ Step from view field to name field

: NO-NAME       ( -- )  
                ;

: >NAME         ( cfa -- nfa )
                DUP CNHASH DUP Y@ SWAP
                2+ Y@ ( cfa sya mxya ) CNSRCH
                IF      DROP (LIT) [ ROT ,-X ] THEN    ;

: >LINK         ( acf -- alf )
                >NAME   N>LINK   ;

: >VIEW         ( acf -- avf )
                >LINK   2-   ;

: VIEW>         ( avf -- acf )
                2+   LINK>   ;

COMMENT:

  The hash algorithm used is as follows:

        ((firstchar*2)+secondchar)*2)+count

  This seems to provide a good distribution across the 64 threads in
1000 word FORTH vocabulary.

COMMENT;

CODE HASH       ( str-addr voc-ptr -- thread )
                POP CX          POP BX
                MOV AX, 1 [BX]          \ Get first and second chars
                SHL AL, # 1             \ Shift first char left one
                ADD AL, AH              \ Plus second char
                SHL AX, # 1             \ The sum shifted left one again
                ADD AL, 0 [BX]          \ Plus count byte
                AND AX, # #THREADS 1-
                SHL AX, # 1     ADD AX, CX
                1PUSH           END-CODE

CODE (FIND)     ( here alf -- cfa flag | here false )
                POP BX
                OR BX, BX
             0= IF
                        SUB AX, AX
                        1PUSH
                THEN
                POP CX
                PUSH ES
                MOV ES, YSEG
                MOV DI, CX
            BEGIN
                MOV ES: AX, 2 [BX]
                XOR AX, 0 [DI]
                AND AX, # ( 63 ) $7F3F
             0= IF
                        MOV DX, BX
                        ADD BX, # 2
                        BEGIN
                                INC BX  INC DI
                                MOV ES: AL, 0 [BX]
                                XOR AL, 0 [DI]
                    0<> UNTIL
                        AND AL, # 127
                     0= IF
                                MOV ES: CX, 1 [BX]      \ pick up CFA
                                MOV BX, DX
                                MOV ES: AL, 2 [BX]
                                AND AL, # 64
                                0<> IF
                                    MOV AX, # 1
                                ELSE
                                    MOV AX, # -1
                                THEN
                                POP ES
                                PUSH CX
                                1PUSH
                        THEN
                        MOV BX, DX
                        MOV DI, CX
                THEN
                MOV ES: BX, 0 [BX]
                OR BX, BX
        0= UNTIL
                POP ES
                PUSH CX
                SUB AX, AX
                1PUSH           END-CODE

HEADERLESS      \ Disable generation of headers

CODE DROP.CONTEXT.I2*+@DUP   ( a1 -- n1 )
                ADD SP, # 2
                MOV AX, 0 [RP]
                ADD AX, 2 [RP]
                SHL AX, # 1
                MOV BX, # CONTEXT
                ADD BX, AX
                MOV AX, 0 [BX]
                PUSH AX
                1PUSH
                END-CODE

                                \ DUP PRIOR @ OVER PRIOR ! =
CODE PRIOR.CHECK ( n1 -- n1 f1 )
                MOV   BX, SP
                MOV   AX, 0 [BX]
                MOV BX, PRIOR
                MOV PRIOR AX
                CMP BX, AX
            0<> IF
                        SUB AX, AX
                        1PUSH
                THEN
                MOV AX, # TRUE
                1PUSH
                END-CODE

CODE OVER.SWAP.HASH.@     ( n1 n2 -- n1 n3 )
                POP AX
                MOV BX, SP
                MOV BX, 0 [BX]
                MOV CL, 0 [BX]
                MOV BX, 1 [BX]
                SHL BL, # 1
                ADD BL, BH
                SHL BL, # 1
                ADD BL, CL
                AND BX, # #THREADS 1-
                SHL BX, # 1
                ADD BX, AX
                PUSH 0 [BX]
                NEXT            END-CODE

HEADERS         \ Restore generation of TARGET HEADERS

: %%FIND        ( addr false #vocs 0 -- cfa flag | addr false )
                DO      DROP.CONTEXT.I2*+@DUP
                        IF      PRIOR.CHECK
                                IF      DROP FALSE
                                ELSE    OVER.SWAP.HASH.@ (FIND)
                                        DUP ?LEAVE
                                THEN
                        THEN
                LOOP    ;

CODE %FIND      ( addr -- cfa flag | addr false )
                MOV DI, SP
                MOV BX, 0 [DI]
                CMP 0 [BX], # 0 BYTE
            0<> IF
                        MOV PRIOR # 0 WORD                      \ prior off
                        MOV BX, # 0             PUSH BX         \ false
                        MOV CX, # #VOCS         PUSH CX         \ #vocs
                                                PUSH BX         \ 0
                        MOV AX, # ' %%FIND
                        JMP AX
                THEN
                MOV END? # TRUE WORD
                MOV 0 [DI], # ' NOOP WORD
                MOV AX, # 1
                1PUSH           END-CODE

DEFER FIND      ' %FIND IS FIND

CODE SKIP'C'    ( a1 - a1 )             \ conditionally skip following word
                                        \ if word at a1 is 'letter'.
                POP BX
                PUSH BX
                CMP 0 [BX], # ASCII ' FLIP 3 + WORD  \ check cnt & first char
            0<> IF      NEXT
                THEN    CMP 3 [BX], # ASCII ' BYTE   \ check last char is '
            0<> IF      NEXT
                THEN    ADD SI, # $02 WORD           \ skip following word
                NEXT            END-CODE

: DEFINED       ( -- here 0 | cfa [ -1 | 1 ] )
                BL WORD  SKIP'C' ?UPPERCASE  FIND   ;

HEADERLESS

: STACKUNDER    ( -- )
                TRUE ABORT" Stack Underflow" ;

: STACKOVER     ( -- )
                TRUE ABORT" Stack Overflow" ;

: WARNOVER      ( -- )
                CR ."  Running out of CODE memory! " ;

HEADERS

CODE (?STACK)   ( -- )
                MOV CX, SP
                MOV BX, UP
                MOV DX, SP0 [BX]
                CMP DX, CX
             U< IF
                        MOV AX, # ' STACKUNDER
                        JMP AX
                THEN
                MOV DX, DP [BX]
                ADD DX, # 80
                CMP CX, DX
             U< IF
                        MOV AX, # ' STACKOVER
                        JMP AX
                THEN
                ADD DX, # 200
                CMP CX, DX
             U< IF
                        MOV AX, # ' WARNOVER
                        JMP AX
                THEN
                NEXT            END-CODE

DEFER ?STACK    ' (?STACK) IS ?STACK

: INTERP        ( -- )
                BEGIN   ?STACK DEFINED
                        IF     EXECUTE
                        ELSE   NUMBER  DOUBLE? NOT IF  DROP  THEN
                        THEN   FALSE DONE?
                UNTIL   ;

DEFER STATUS    ( -- )

DEFER INTERPRET ' INTERP IS INTERPRET

: PRINT         ( -- ) PRINTING ON INTERPRET PRINTING OFF ;

: ALLOT         ( n -- )      DP +!   ;

CODE ,          ( n -- )
                MOV BX, UP
                MOV AX, DP [BX]
                ADD DP [BX], # 2 WORD
                MOV BX, AX
                POP CX
                MOV 0 [BX], CX
                NEXT
                END-CODE

CODE C,         ( n -- )
                MOV BX, UP
                MOV AX, DP [BX]
                INC DP [BX] WORD
                MOV BX, AX
                POP CX
                MOV 0 [BX], CL
                NEXT
                END-CODE

: ALIGN         ( -- )
                ( HERE 1 AND IF  BL C,  THEN )  ; IMMEDIATE

: EVEN          ( n1 -- n2 )
                ( DUP 1 AND + ) ;  IMMEDIATE

: COMPILE       ( -- )   
                2R@ R> 2+ >R @L X,   ;

: IMMEDIATE     ( -- )   
                64 ( Precedence bit ) LAST @ YCSET  ;

: LITERAL       ( n -- )  
                COMPILE (LIT) X, ; IMMEDIATE

: DLITERAL      ( d# -- ) 
                SWAP [COMPILE] LITERAL [COMPILE] LITERAL ; IMMEDIATE

: ASCII         ( -- n )   
                BL WORD   1+ C@
                STATE @ IF   [COMPILE] LITERAL   THEN   ; IMMEDIATE

: CONTROL       ( -- n )   
                BL WORD   1+ C@  31 AND
                STATE @ IF   [COMPILE] LITERAL   THEN   ; IMMEDIATE

: CRASH         ( -- ) 
                2R@ 2- @L >NAME CR .ID TRUE
                ABORT" <- is an Uninitialized execution vector."  ;

: ?MISSING      ( f -- )
                IF      SPACE HERE COUNT TYPE
                        TRUE ABORT"  <- What? "
                THEN    ;

: '             ( -- cfa )      
                DEFINED 0= ?MISSING   ;

: [']           ( -- )          
                ' COMPILE <'> X, ; IMMEDIATE

: [COMPILE]     ( -- )          
                ' X,   ; IMMEDIATE

VARIABLE  "BUF 132 ALLOT

: XEVEN         ( xdp -- xdp_even ) 
                DUP 1 AND + ;

: XALIGN        ( -- ) 
                XHERE NIP 1 AND XDP +! ;

: X>"BUF        ( -- "buf )
                2R>
                2R@ 2DUP C@L 1+ DUP XEVEN R> + >R
                        ?CS: "BUF ROT CMOVEL
                2>R "BUF ;

: (")           ( -- addr len )
                2R@ @L COUNT R> 2+ >R ;

: (X")           ( -- addr len )
                X>"BUF COUNT ;

: %(.")         ( -- )
                2R@ 2DUP C@L >R 1+ R@ TYPEL R> 1+ XEVEN R> + >R ;

DEFER (.")      ' %(.") IS (.")

: ,"            ( -- )
                ASCII " PARSE TUCK HERE PLACE 1+ ALLOT ;

: X,"           ( -- )
                ASCII " PARSE HERE PLACE
                ?CS: HERE DUP C@ 1+ >R XHERE R@ CMOVEL
                R> XEVEN XDP +! ;

: ."            ( -- )          COMPILE (.") X,"   ;   IMMEDIATE

: "             ( -- )          COMPILE (")  HERE X, ,"   ;   IMMEDIATE

: ""            ( -- )          COMPILE (X")  X,"   ;   IMMEDIATE

: ">$           ( a1 n1 -- a2 )
                DROP 1- ;

VARIABLE FENCE

: TRIM          ( faddr voc-addr -- )
                #THREADS 0
                DO      2DUP @ BEGIN   2DUP U> NOT WHILE Y@ REPEAT
                        NIP OVER ! 2+
                LOOP    2DROP   ;

: (FRGET)       ( code-addr view-addr -- )
                DUP FENCE @ U< ABORT" Below fence"  ( ca va )
                OVER VOC-LINK @ BEGIN   2DUP U< WHILE   @ REPEAT
                DUP VOC-LINK !  ( ca va ca pt ) NIP
                BEGIN   DUP WHILE   2DUP #THREADS 2* - TRIM   @   REPEAT
                DROP    YDP !
                DUP 1+ @ OVER >BODY +
                (LIT)   TRIM DUP 1+ @ SWAP >BODY + =    \ If it's a : def
                IF      DUP >BODY @ +XSEG XDPSEG !   \ Set back XHERE too!
                        XDP OFF
                THEN    DP !  ;

DEFER ?ERROR

                                        \ 07/03/89 TJZ
: (ABORT")      ( f -- )                \ if f1 true, then display inline
                ?DUP                    \ compiled message from LIST space
                IF
                        X>"BUF COUNT ROT ?ERROR
                ELSE    2R@ C@L 1+ XEVEN R> + >R
                THEN    ;

: ABORT"        ( -- )   
                COMPILE (ABORT") X," ;   IMMEDIATE

: ABORT         ( -- )   
                TRUE ABORT" "  ;

: FORGET        ( -- )
                BL WORD ?UPPERCASE DUP CURRENT @ HASH @
                (FIND) 0= ?MISSING DUP >VIEW (FRGET) ;

: ?CONDITION    ( f -- )        
                NOT ABORT" Conditionals Wrong"   ;

: >MARK         ( -- addr )     
                XHERE NIP 0 X,   ;

: >RESOLVE      ( addr -- )     
                XHERE -ROT SWAP !L   ;

: <MARK         ( -- addr )     
                XHERE NIP ;

: <RESOLVE      ( addr -- )     
                X, ;

: ?>MARK        ( -- f addr )   
                TRUE >MARK   ;

: ?>RESOLVE     ( f addr -- )   
                SWAP ?CONDITION >RESOLVE  ;

: ?<MARK        ( -- f addr )   
                TRUE   <MARK   ;

: ?<RESOLVE     ( f addr -- )   
                SWAP ?CONDITION <RESOLVE  ;

comment:
        LEAVE and ?LEAVE could be non-immediate in this system, but the 83
        standard specifies an immediate LEAVE, so they  both are for
        uniformity.
comment;

: LEAVE         ( -- )
                COMPILE (LEAVE)  ; IMMEDIATE

: ?LEAVE        ( f1 -- )
                COMPILE (?LEAVE) ; IMMEDIATE

comment:
        BEGIN, THEN, DO ?DO, LOOP, +LOOP, UNTIL, AGAIN, REPEAT, IF ELSE,
        WHILE: These are the compiling words needed to properly compile the
        Forth Conditional Structures. Each of them is immediate and they
        must compile their runtime routines along withwhatever addresses
        they need. A modest amount of errorchecking is done. If you want to
        rip out the error checking change the ?> and ?< words to > and <
        words, and all of the 2DUPs to DUPs and the 2SWAPs to SWAPs. The
        rest should stay the same.

        DOAGAIN, DOTHEN, DOBEGIN, ?UNTIL & ?WHILE are words that are NOOPs
        , or equivalant to ?BRANCH. They are provided to make it easier for
        the Decompiler to know where the control structures start and end.
comment;

: BEGIN         ( -- )
                COMPILE DOBEGIN ?<MARK                          ; IMMEDIATE

: AGAIN         ( -- ) 
                COMPILE DOAGAIN ?<RESOLVE                       ; IMMEDIATE

: UNTIL         ( n -- )
                COMPILE ?UNTIL  ?<RESOLVE                       ; IMMEDIATE

: WHILE         ( n -- )
                COMPILE ?WHILE ?>MARK 2SWAP ( <- added )        ; IMMEDIATE

: REPEAT        ( -- )       ( 2SWAP removed ) 
                COMPILE DOREPEAT ?<RESOLVE ?>RESOLVE            ; IMMEDIATE

: DO            ( lim start -- )
                COMPILE (DO)   ?>MARK                           ; IMMEDIATE

: ?DO           ( lim start -- ) 
                COMPILE (?DO)  ?>MARK                           ; IMMEDIATE

: LOOP          ( -- )
                COMPILE (LOOP)  2DUP 2+ ?<RESOLVE ?>RESOLVE     ; IMMEDIATE

: +LOOP         ( n -- )
                COMPILE (+LOOP) 2DUP 2+ ?<RESOLVE ?>RESOLVE     ; IMMEDIATE

: IF            ( n -- ) 
                COMPILE  ?BRANCH  ?>MARK                        ; IMMEDIATE

: ELSE          ( -- ) 
                COMPILE  BRANCH ?>MARK  2SWAP ?>RESOLVE         ; IMMEDIATE

: THEN          ( -- ) 
                COMPILE DOTHEN ?>RESOLVE                        ; IMMEDIATE

: FORWARD       ( -- )
                COMPILE BRANCH ?>MARK                           ; IMMEDIATE

: CONTINUE      ( -- )
                2OVER [COMPILE] REPEAT                          ; IMMEDIATE

: BREAK         ( -- ) 
                COMPILE EXIT [COMPILE] THEN                     ; IMMEDIATE

: AFT           ( -- )
                2DROP [COMPILE] FORWARD ?<MARK 2SWAP            ; IMMEDIATE

: FOR           ( n1 -- )
                COMPILE >R ?<MARK                               ; IMMEDIATE

: NEXT          ( -- )
                COMPILE NEXT| ?<RESOLVE                         ; IMMEDIATE

: ,VIEW         ( -- )  
                LOADLINE @ Y, ;

HEADERLESS

: NOHEADROOM    ( -- )
                TRUE ABORT" Out of HEAD memory!" ;

: NOLISTROOM    ( -- )
                TRUE ABORT" Out of LIST memory!" ;

HEADERS

CODE SPCHECK    ( -- f1 f2 )           \ HEAD AND LIST SPACE CHECK
                MOV AX, YDP             \ get head DP
                SHR AX, # 1             \ convert to ssegment
                SHR AX, # 1
                SHR AX, # 1
                SHR AX, # 1
                ADD AX, # 6             \ add 6 segments for headroom
                CMP AX, ' #HEADSEGS >BODY \ are we out of space yet
             >  IF      MOV AX, # ' NOHEADROOM
                        JMP AX
                THEN
                MOV AX, XDPSEG          \ load up LIST segment
                SUB AX, XSEG            \ convert to size of list so far
                ADD AX, # 6             \ add 6 for headroom
                CMP AX, ' #LISTSEGS >BODY \ are we out of space yet
             >  IF      MOV AX, # ' NOLISTROOM
                        JMP AX
                THEN
                NEXT
                END-CODE

: %ALREADY_DEF  ( a1 -- a1 )   \ Is the word at A1 already defined?
                WARNING @
                IF      DUP FIND NIP
                        IF      DUP  CR  COUNT TYPE ."  isn't unique "
                        THEN
                THEN    ;       ( str )

DEFER ?ALREADY_DEF      ' %ALREADY_DEF IS ?ALREADY_DEF

: <"HEADER>     ( str-addr -- )
                SPCHECK
                DUP C@ 31 > ABORT" Name TOO LONG, > 31 chars!"
                ?ALREADY_DEF
                ALIGN  YHERE 2- Y@ CNHASH  HERE CNHASH  <>
                IF      YHERE HERE CNHASH DUP Y@ ROT UMIN SWAP
                        Y! ( >NAME hash entry )
                THEN    ,VIEW
                YHERE OVER CURRENT @ HASH DUP @  Y,  ( link  ) ! ( current )
                YHERE LAST ! ( remember nfa )
                YHERE ?CS: ROT  DUP C@  WIDTH @  MIN 1+ >R  ( yh cs str )
                YHERE YS: R@ CMOVEL ( copy str ) R> YDP +! ALIGN ( nam )
                128 SWAP YCSET   128 YHERE 1- YCSET   ( delimiter Bits )
                HERE Y, ( CFA in header )
                YHERE HERE CNHASH 2+ Y! ( valid stopper in next n hash entry)
                ;

DEFER "HEADER   ' <"HEADER> IS "HEADER

: ,CALL         ( -- )
                232 C, 0 HERE 2+ - , ;        \ Compiles addr 0000 !!!!

: ,JUMP         ( -- )
                233 C, 0 HERE 2+ - , ;

: <HEADER>      ( | name -- )
                BL WORD ?UPPERCASE "HEADER ;

DEFER HEADER    ' <HEADER> IS HEADER

: "CREATE       ( str-addr -- )
                "HEADER ,CALL ;USES >NEXT ,-X

: CREATE        ( | name -- )  
                HEADER ,CALL ;USES >NEXT ,-X

: !CSP          ( -- )  
                SP@ CSP !   ;

: ?CSP          ( -- )  
                SP@ CSP @ <> ABORT" Stack Changed"   ;

: HIDE          ( -- )  
                LAST @ DUP N>LINK Y@ SWAP CURRENT @ YHASH ! ;

: REVEAL        ( -- )  
                LAST @ DUP N>LINK    SWAP CURRENT @ YHASH ! ;

: (;USES)       ( -- )
                2R> @L LAST @ NAME> dup>r 3 + - R> 1+ ! ;

: (;CODE)       ( -- )
                2R> @L LAST @ NAME>
                dup>r 232 ( CALL ) R@ C!       \ Make a CALL not JUMP
                3 + - R> 1+ !  ;

: DOES>         ( -- )
                COMPILE (;CODE) HERE X, 232 ( CALL ) C,
                [ [FORTH] ASSEMBLER DODOES META ] LITERAL
                HERE 2+ - , XHERE PARAGRAPH + DUP XDPSEG !
                XSEG @ - , XDP OFF ; IMMEDIATE

VOCABULARY ASSEMBLER

DEFER SETASSEM  \ Setup for assembly stuff to follow

' NOOP IS SETASSEM

: [             ( -- )  
                STATE OFF   ;   IMMEDIATE

: ;USES         ( -- )  
                ?CSP   COMPILE  (;USES)
                [COMPILE] [   REVEAL   ASSEMBLER   ; IMMEDIATE

: ;CODE         ( -- )  
                ?CSP   COMPILE  (;CODE) HERE X,
                [COMPILE] [   REVEAL   SETASSEM ; IMMEDIATE

: (])           ( -- )
                STATE ON
                BEGIN   ?STACK  DEFINED DUP
                        IF      0>
                                IF      EXECUTE   ELSE   X,   THEN
                        ELSE    DROP    NUMBER  DOUBLE?
                                IF           [COMPILE] DLITERAL
                                ELSE    DROP [COMPILE] LITERAL
                                THEN
                        THEN    TRUE    DONE?
                UNTIL   ;

DEFER ]         ' (]) IS ]

: MAKEDUMMY     ( name -- )
                HEADER ,JUMP
                XHERE PARAGRAPH +       \ absolute paragraph of new def
                DUP XDPSEG !            \ set new XHERE segment
                XSEG @ - ,              \ compile relative paragraph of def
                XDP OFF
                COMPILE UNNEST
                ;USES  NEST ,-X

: ANEW          ( name -- )
                >IN @ >R DEFINED NIP  R@ >IN !
                IF      FORGET
                THEN    R> >IN !  MAKEDUMMY   ;
                                                        \ Add if needed
: (:)           ( -- )
                !CSP   CURRENT @ CONTEXT !
                HEADER ,JUMP
                XHERE PARAGRAPH +
                DUP XDPSEG !
                XSEG @ - ,
                XDP OFF
                HIDE
                ;USES   NEST ,-X

: :             ( -- )
                (:) ] ;

: ;             ( -- )
                STATE @ 0= ABORT" Not Compiling!"
                ?CSP   COMPILE UNNEST   REVEAL   [COMPILE] [  ; IMMEDIATE

: RECURSIVE     ( -- )   
                REVEAL ;   IMMEDIATE

: CONSTANT      ( n -- ) 
                HEADER ,JUMP ,     ;USES DOCONSTANT ,-X

: VALUE         ( n -- ) 
                HEADER ,JUMP ,     ;USES DOVALUE    ,-X

: VARIABLE      ( -- )   
                CREATE 0 ,         ;USES >NEXT      ,-X

: ARRAY         ( n1 -- )
                CREATE ALLOT       ;USES >NEXT      ,-X

: DEFER         ( -- )
                CREATE ['] CRASH , ;USES DODEFER    ,-X

DODEFER RESOLVES <DEFER>

: VOCABULARY    ( -- )  
                CREATE   #THREADS 0 DO   0 ,  LOOP
                HERE  VOC-LINK @ ,  VOC-LINK !
                DOES> CONTEXT !  ;

 RESOLVES <VOCABULARY>

: DEFINITIONS   ( -- ) 
                CONTEXT @ CURRENT !   ;

: 2CONSTANT     ( d1 | <name> -- )
                CREATE   , ,    ( d# -- )
                DOES> 2@   ;    ( -- d# )   DROP

: 2VARIABLE     ( | <name> -- )
                0 0 2CONSTANT   ( -- )
                DOES> ;         ( -- addr )   DROP

: <RUN>         ( -- )
        STATE @ IF      ]
                        STATE @ NOT
                        IF   INTERPRET   THEN
                ELSE    INTERPRET   THEN   ;

DEFER RUN       ' <RUN> IS RUN

DEFER ERRFIX    ' NOOP IS ERRFIX

: (?ERROR)      ( adr len f -- )
                IF      ['] <RUN> IS RUN ERRFIX
                        2>R SP0 @ SP!   PRINTING OFF
                        2R> SPACE TYPE SPACE   QUIT
                ELSE    2DROP  THEN  ;

' (?ERROR) IS ?ERROR

