\ META86.SEQ    The META compiler Source for F-PC.
\  F-PC : Forth-83 with separated heads, handles, and sequential files.
\  Meta compiler.  Loaded by F-PC to produce KERNEL.COM.

\ *************************************************************
\ ***      ORIGINALLY   Based on F83 version 2.1.0 by       ***
\ ***                                                       ***
\ ***    Henry Laxen         and    Michael Perry           ***
\ ***    1259 Cornell Avenue        1125 Bancroft Way       ***
\ ***    Berkeley, California       Berkeley, California    ***
\ ***    94706                      94702                   ***
\ ***                                                       ***
\ *************************************************************
\     Heads separation by:     J. D. Hopper
\                              P.O. Box 2782
\                              Stanford, Ca.  94305

\     Handles and
\     sequential files by:      Tom Zimmer          Hm  (408) 263-8859
\                               292 Falcato Drive   Wk  (408) 432-4643
\                               Milpitas, Ca. 95035

\     Direct Threaded Code
\     conversion by:            Bob Smith and Tom Zimmer
\
\               Contact:        Tom Zimmer          Hm  (408) 263-8859
\                               292 Falcato Drive   Wk  (408) 432-4643
\                               Milpitas, Ca. 95035

DECIMAL

0COMPILER

WARNING OFF
ONLY FORTH ALSO DEFINITIONS

15 TABSIZE !    \ WIDER TABS
78 RMARGIN !    \ WIDER RIGHT MARGIN
 0 LMARGIN !    \ LEFT MARGIN TO LEFT EDGE
?DARK           \ CLEAR SCREEN AND CLEAR #LINE

: .TITLE        CR
                ." Meta Compiled Direct Threaded Forth       "
                .DATE TAB .TIME
                CR CR ;

ONLY FORTH ALSO VOCABULARY META META ALSO META DEFINITIONS

VARIABLE SEG-Y
VARIABLE SEG-X
VARIABLE SEG-C
VARIABLE SEG-S

: ZSAVE ( Addr len | filename -- ) \ Save code from external segment.
        seqhandle+         !HCB
        seqhandle+         HDELETE     DROP
        seqhandle+         HCREATE     ABORT" Save Create ERR!"
        seqhandle+ SEG-C @ EXHWRITE 0= ABORT" Save Write  ERR!"
        seqhandle+         HCLOSE      ABORT" Save Close  ERR!" ;

COMMENT:
          The following constant controls how many threads will be created
        in the target KERNEL.COM. The constant #TTHREADS MUST BE a binary
        multiple of two (2) for the KERNEL.COM to function. Any binary
        multiple of two between and including 2 and 128 is acceptable.

          Higher values of #TTHREADS produces a faster compiler, but
        costs more memory. i.e. from 32 to 64 threads costs 512 bytes
        of code space and increases compile performance by 10%.
        Increasing the number of threads from 64 to 128 costs 1024 bytes
        of code space, and increases compile performance by only 4.5%.
COMMENT;

 64 CONSTANT #TTHREADS

: MEMCHK ABORT" Insufficient Memory" ;

: DOSVER 0 $030 BDOS $0FF AND ;

: DOSCHK  DOSVER 2 < ABORT" Must have DOS >=2" ;

 DOSCHK

comment:
$0FFF CONSTANT SIZESEGS
$0800 CONSTANT HEADSEGS \ 800 hex is 32k decimal bytes
$0800 CONSTANT LISTSEGS
$0FFF CONSTANT CODESEGS \ MUST BE less than $1000 for math in KERNEL4 to work
                        \ Create and erase the buffers
HEADSEGS ALLOC 8 = MEMCHK NIP DUP SEG-Y ! 0 HEADSEGS $010 * 0 LFILL
LISTSEGS ALLOC 8 = MEMCHK NIP DUP SEG-X ! 0 LISTSEGS $010 * 0 LFILL
CODESEGS ALLOC 8 = MEMCHK NIP DUP SEG-C ! 0 CODESEGS $010 * 0 LFILL

\ Un-comment out the following line if you want kernel words sizes saved.
\ SIZESEGS ALLOC 8 = MEMCHK NIP DUP SEG-S ! 0 SIZESEGS $010 * 0 LFILL
comment;

\ Un-comment out the following 2 lineS if you want kernel words sizes saved.
\ $FFF0 CONSTANT SIZEBYTES
\ SIZEBYTES 0 POINTER SSEG SSEG DUP SEG-S ! 0 SIZEBYTES 0 LFILL

$8000 CONSTANT HEADBYTES \ 8000 hex is 32k decimal bytes
HEADBYTES 0 POINTER HSEG HSEG DUP SEG-Y ! 0 HEADBYTES 0 LFILL
$8000 CONSTANT LISTBYTES
LISTBYTES 0 POINTER LSEG LSEG DUP SEG-X ! 0 LISTBYTES 0 LFILL
$FFF0 CONSTANT CODEBYTES \ MUST BE less than $FFFF for math in KERNEL4 to work
                         \ Create and erase the buffers
CODEBYTES 0 POINTER CSEG CSEG DUP SEG-C ! 0 CODEBYTES 0 LFILL

: NYTH ( cfa -- ythread) 512 / 2* ;

: ?NEWPAGE      ( --- )
                PRINTING @ 0= IF EXIT THEN
                #LINE @ 57 >
                IF      CR
                        12 SP@ 1 TYPE DROP #LINE OFF
                        CR .TITLE
                THEN    ;

VARIABLE LABELS         LABELS OFF      \ DEFAULT TO NOT DISPLAY MAP

: ?LABELS       ( --- )
                CR CR ." Do you want LABELS printed Y/N [N]? "
                KEY BL OR 'y' = DUP LABELS !
                IF      ." Y"
                ELSE    ." N"   THEN CR .TITLE TIME-RESET ;

?LABELS

: ?BEHEADABLE   ( --- )
                cr beheadable
                if      ." Some words WILL BE HEADERLESS"
                else    ." NO words will be made headerless"
                then    cr cr ;
?BEHEADABLE

HANDLE SIZEHNDL         SIZEHNDL !HCB KERNEL.SIZ

: SIZE-SAVE     ( --- )                 \ save the CFA sizes file
                SEG-S @ 0= ?EXIT
                CR ." Saving 64k image of CFA sizes.."
                SIZEHNDL HCREATE ABORT" Could not create SIZE file"
                0 $FF00 SIZEHNDL SEG-S @ EXHWRITE $FF00 -
                ABORT" Write error to SIZE file"
                SIZEHNDL HCLOSE DROP CR ;

3 CONSTANT BODY_SIZE                    \ SIZE OF BODY FIELD IN BYTES

: >BODY-T       ( A1 --- A2 )           \ Move to body of target
                BODY_SIZE + ;

VARIABLE DP-T

: [FORTH]        FORTH    ; IMMEDIATE

: [META]         META     ; IMMEDIATE

: [ASSEMBLER]    ASSEMBLER     ; IMMEDIATE

: SWITCH   ( -- )
   NOOP    ( Context )   NOOP ( Current )
   DOES>   @ +XSEG DUP 0 @L CONTEXT @   SWAP CONTEXT !   OVER 0 !L
                   DUP 2 @L CURRENT @   SWAP CURRENT !   SWAP 2 !L  ;
   SWITCH  ( Redefine itself )

: CS:     ( taddr -- taddr tseg ) SEG-C @ SWAP ;

: CS:ERASE ( A1 N1 --- )      >R CS: R> 0 LFILL ;

0 CONSTANT TARGET-ORIGIN
: THERE   ( taddr -- addr )   TARGET-ORIGIN +   ;
: C@-T    ( taddr -- char )   THERE CS: C@L ;
: @-T     ( taddr -- n )      THERE CS: @L  ;
: C!-T    ( char taddr -- )   THERE CS: C!L ;
: !-T     ( n taddr -- )      THERE CS: !L  ;
: HERE-T  ( -- taddr )   DP-T @   ;
: ALLOT-T ( n -- )       DP-T +!   ;
: C,-T    ( char -- )   HERE-T C!-T   1 ALLOT-T   ;
: ,-T     ( n -- )      HERE-T  !-T   2 ALLOT-T   ;
: S,-T    ( addr len -- )
   0 ?DO   COUNT C,-T   LOOP   DROP   ;

: ,"-T    ( | text" -- ) '"' PARSE  DUP C,-T  S,-T  ALIGN  ;

: XS:     ( taddr -- taddr tseg ) SEG-X @ SWAP ;
VARIABLE DP-X           0 DP-X !
VARIABLE DPSEG-X        SEG-X @ DPSEG-X !

: PARAGRAPH-X   ( N1 --- SEG-DELTA ) 15 + U16/ ;
: >XREL         ( SEG OFFSET --- OFFSET )     \ RELATIVE TO SEG-X
                SWAP SEG-X @ - 16 * + ;

: C@-X    ( taddr -- char )   XS: C@L ;
: @-X     ( taddr -- n )      XS: @L  ;
: C!-X    ( char taddr -- )   XS: C!L ;
: !-X     ( n taddr -- )      XS: !L  ;
: HERE-X  ( -- XDPSEG taddr )   DPSEG-X @ DP-X @   ;
: ALLOT-X ( n -- )       DP-X +!   ;
: C,-X    ( char -- )   HERE-X C!L   1 ALLOT-X   ;
: ,-X     ( n -- )      HERE-X  !L   2 ALLOT-X   ;
: S,-X    ( addr len -- )
        0 ?DO   COUNT C,-X   LOOP   DROP   ;

: ALIGN-X       ( --- )
                HERE-X NIP 1 AND IF 0 C,-X THEN ;

: YS:   SEG-Y @ SWAP ;
VARIABLE DP-Y    256 DP-Y !
: C@-Y    ( yaddr -- char )   YS: C@L  ;
: @-Y     ( yaddr -- n )      YS: @L  ;
: C!-Y    ( char yaddr -- )   YS: C!L ;
: !-Y     ( n yaddr -- )      YS: !L  ;
: HERE-Y  ( -- yaddr )        DP-Y @ ;
: ALLOT-Y ( n -- )    DP-Y +! ;
: C,-Y    ( char -- ) HERE-Y C!-Y  1 ALLOT-Y  ;
: ,-Y     ( n -- )    HERE-Y  !-Y  2 ALLOT-Y  ;
: S,-Y    ( addr len )  0 ?DO COUNT C,-Y  LOOP  DROP  ;
: CSET-Y  ( byte yaddr -- )  TUCK C@-Y OR SWAP C!-Y ;

: SVXSEG        ( - xstart )
                SEG-X @ 0 SEG-C @ HERE-T  DUP >R THERE
                HERE-X PARAGRAPH-X + SEG-X @ - 16 *
                CMOVEL R> ;

: SVYSEG        ( - ystart )
                SEG-Y @ 0 SEG-C @ HERE-T  DUP >R THERE HERE-Y
                CMOVEL R> ;

: CNHASH ( CFA-YA )  $0FE00 AND FLIP ;

VARIABLE UNRESOLVED

: .UNRESOLVEPAUSE   ( --- )
                UNRESOLVED @
                IF      BEEP >NORM
                        CR ." There were UNRESOLVED references,"
                        CR >REV ." press a key to acknoledge." KEY DROP >NORM
                        CR
                ELSE    >NORM ."  **** ALL REFERENCES RESOLVED **** "
                THEN    ;

VOCABULARY TARGET
VOCABULARY TRANSITION
VOCABULARY FORWARD
VOCABULARY USER

ONLY DEFINITIONS FORTH ALSO META ALSO

: META          META  ;
: TARGET        TARGET  ;
: TRANSITION    TRANSITION  ;
: FORWARD       FORWARD  ;
: USER          USER    ;
: ASSEMBLER     ASSEMBLER  ;

ONLY FORTH ALSO META ALSO DEFINITIONS

: X?>MARK       ( -- f addr )   TRUE   HERE-X NIP 0 ,-X   ;
: X?>RESOLVE    ( f addr -- )   HERE-X -ROT SWAP !L   ?CONDITION  ;
: X?<MARK       ( -- f addr )   TRUE   HERE-X NIP ;
: X?<RESOLVE    ( f addr -- )   ,-X   ?CONDITION   ;

: AM?>MARK      ( -- f addr )   TRUE   HERE-T   0 C,-T   ;
: AM?>RESOLVE   ( f addr -- )   HERE-T OVER 1+ - SWAP C!-T   ?CONDITION   ;
: AM?<MARK      ( -- f addr )   TRUE   HERE-T   ;
: AM?<RESOLVE   ( f addr -- )   HERE-T 1+ - C,-T   ?CONDITION   ;

'   C,-T        ASSEMBLER IS  C,
'    ,-T        ASSEMBLER IS   ,
'   C@-T        ASSEMBLER IS TC@
'   C!-T        ASSEMBLER IS TC!
' HERE-T        ASSEMBLER IS HERE
' AM?>MARK      ASSEMBLER IS ?>MARK
' AM?>RESOLVE   ASSEMBLER IS ?>RESOLVE
' AM?<MARK      ASSEMBLER IS ?<MARK
' AM?<RESOLVE   ASSEMBLER IS ?<RESOLVE

: ?CLEAR-LABS   ( --- )
                [ ASSEMBLER ALSO FORTH ]
                LL-GLOBAL? 0=
                IF      LLAB-INIT               \ IN CASE LABELS USED
                THEN    ;

ONLY FORTH ALSO META ALSO DEFINITIONS

: SIZE-SET      ( --- )
                SEG-S @ 0= ?EXIT                \ leave if not saving sizes
                SEG-S @ DUP>R 0 @L              \ If non-zero then
                IF      HERE-T R@ 0 @L -        \ calculate actual length
                        R@ DUP 0 @L !L          \ fill in CODE length WORD
                        DP-X @                  \ length of list
                        R@ DUP 0 @L 2+ !L       \ fill in LIST length WORD
                        HERE-X PARAGRAPH-X + DPSEG-X ! DP-X OFF
                                                \ Round up LIST segment
                THEN    HERE-T R> 0 !L ;

: LABEL         ( | NAME -- )
                @> RUN =: ARUNSAVE
                0 ['] DROP A;!
                ['] RUN-A; IS RUN
                ASSEMBLER DEFINITIONS
                ?CLEAR-LABS
                >IN @ >R HERE-T CONSTANT
                LABELS @
                IF      R> >IN !
                        BL WORD DUP C@ 5 + ?LINE
                        HERE-T H.
                        COUNT TYPE TAB
                        ?NEWPAGE
                ELSE    R>DROP
                THEN    !CSP  ;

: XLABEL        ( | NAME -- )
                @> RUN =: ARUNSAVE
                0 ['] DROP A;!
                ['] RUN-A; IS RUN
                ASSEMBLER DEFINITIONS
                ?CLEAR-LABS
                >IN @ >R HERE-X >XREL CONSTANT
                LABELS @
                IF      R> >IN !
                        BL WORD DUP C@ 5 + ?LINE
                        HERE-T H.
                        COUNT TYPE TAB
                        ?NEWPAGE
                ELSE    R>DROP  THEN !CSP ;

: MAKE-CODE     ( PFA -- ) @ ,-X   ;                    \ Absolute address
: MAKE-CODE-REL ( PFA -- ) @ HERE-T 2+ - ,-T   ;        \ Relative offset

: IN-TARGET     ( -- )          ONLY TARGET DEFINITIONS    ;
: IN-TRANSITION ( -- )          ONLY FORWARD ALSO TARGET DEFINITIONS
                                ALSO TRANSITION    ;
: IN-META       ( -- )          ONLY FORTH ALSO META DEFINITIONS ALSO  ;
: IN-FORWARD    ( -- )          FORWARD DEFINITIONS    ;
: LINK-BACKWARDS     ( PFA -- ) HERE-X >XREL OVER @ ,-X   SWAP !   ;
: LINK-BACKWARDS-REL ( PFA -- ) HERE-T OVER @ ,-T   SWAP !   ;
: RESOLVED?     ( pfa -- f )    2+ @   ;

: FORWARD-CODE  ( pfa -- )      DUP RESOLVED?
                                IF      MAKE-CODE
                                ELSE    LINK-BACKWARDS  THEN ;

: FORWARD-CODE-REL ( pfa -- )   DUP RESOLVED?
                                IF      MAKE-CODE-REL
                                ELSE    LINK-BACKWARDS-REL  THEN ;

: FORWARD:      ( -- )
                SWITCH   FORWARD DEFINITIONS
                CREATE SWITCH  0 , 0 , DOES>   FORWARD-CODE   ;

: FORWARD_REL:  ( -- )
                SWITCH   FORWARD DEFINITIONS
                CREATE SWITCH  0 , 0 , DOES>   FORWARD-CODE-REL ;

VARIABLE LAST-T
VARIABLE CONTEXT-T
VARIABLE CURRENT-T

VARIABLE WIDTH-T        31 WIDTH-T !
VARIABLE WIDTH-SAVE     31 WIDTH-SAVE !

\ Use the normal HWORDS+ and HWORDS- to enable and disable the BEHEAD
\ mechanism.  Use HEADERLESS and HEADERS as you would in regular Forth.
\ BEHEAD while available is not needed in the meta compiler.

: HEADERLESS    ( --- )         \ disable generation of headers starting here
                beheadable WIDTH-T @ and
                if      WIDTH-T @ WIDTH-SAVE !
                        WIDTH-T OFF
                then    ;

: HEADERS       ( --- )         \ re-enable the generation of headers here
                beheadable
                if      WIDTH-SAVE @ WIDTH-T !
                then    ;

: BEHEAD        ( --- ) ;       \ does NOTHING in the meta compiler

: HASH          ( str-addr voc-addr -- thread )
                SWAP
                DUP C@ SWAP 1+ DUP C@ 2* SWAP 1+ C@ + 2* +
                #TTHREADS 1- AND 2* +   ;

: HEADER        ( -- )
                BL WORD C@ 1+ WIDTH-T @ MIN   ?DUP
        IF      ( HERE-Y 2- )   ( for ylink at end)
                ALIGN
                HERE-Y 2- @-Y CNHASH HERE-T CNHASH <> IF
                HERE-Y HERE-T CNHASH !-Y THEN  ( >NAME hash entry )
                LOADLINE @ ,-Y
                HERE CURRENT-T @ HASH DUP @-T ,-Y ( link )
                HERE-Y 2- SWAP !-T      ( point voc thread to link field )
                HERE-Y HERE ROT S,-Y   ALIGN   DUP LAST-T !
                128 SWAP CSET-Y   128 HERE-Y 1- CSET-Y
                HERE-T ,-Y              ( cfa ptr )
                HERE-Y HERE-T CNHASH 2+ !-Y     ( stopper >NAME hash entry )
        THEN    ;

: TARGET-CREATE ( -- )
                >IN @ HEADER DUP >IN !
                LABELS @
                IF      BL WORD DUP C@ 5 + ?LINE
                        HERE-T H.
                        COUNT TYPE TAB ?NEWPAGE
                THEN    >IN !
                IN-TARGET CREATE IN-META  HERE-T , TRUE ,
                SIZE-SET
                DOES>   MAKE-CODE   ;

: RECREATE      ( -- )  >IN @   TARGET-CREATE   >IN !   ;


FORTH DEFINITIONS

: CODE          ( NAME --- )
                @> RUN =: ARUNSAVE
                0 ['] DROP A;!
                ['] RUN-A; IS RUN
                TARGET-CREATE ASSEMBLER ?CLEAR-LABS !CSP  ;

: INLINE        ( --- )
                @> RUN =: ARUNSAVE
                0 ['] DROP A;!
                ['] RUN-A; IS RUN
                ASSEMBLER ?CLEAR-LABS !CSP HERE-T ,-X ;


ASSEMBLER ALSO DEFINITIONS

: END-CODE      [ FORTH ]
                ll-global? 0=
                if      ll-errs?        \ check for local label errors
                then
                ARUNSAVE IS RUN
                A; IN-META ?CSP  ;

: END-INLINE    [ FORTH ]
                ll-global? 0=
                if      ll-errs?        \ check for local label errors
                then
                ARUNSAVE IS RUN
                A; IN-META ?CSP  ;

: C;            [ FORTH ]
                ll-global? 0=
                if      ll-errs?        \ check for local label errors
                then
                ARUNSAVE IS RUN
                A; IN-META ?CSP  ;

META IN-META

: 'T            ( -- cfa )
                CONTEXT @   TARGET DEFINED   ROT CONTEXT !
                0= ?MISSING   ;

: [TARGET]      ( -- )          'T X, ;   IMMEDIATE

: 'F            ( -- cfa )
                CONTEXT @   FORWARD DEFINED   ROT CONTEXT !
                0= ?MISSING   ;

: [FORWARD]     ( -- )  'F X, ;   IMMEDIATE

: T:            ( -- )
                SWITCH  TRANSITION DEFINITIONS
                CREATE  XHERE PARAGRAPH + DUP XDPSEG ! XSEG @ - , XDP OFF
                SWITCH   ]
                DOES>   @ +XSEG >R 0 >R ;

: T;            ( -- )
                SWITCH   TRANSITION DEFINITIONS   [COMPILE] ;    SWITCH   ;
                IMMEDIATE

: DIGIT?        ( CHAR -- F )   BASE @ DIGIT NIP   ;

: PUNCT?        ( CHAR -- F )
                '.' OVER = SWAP   '-' OVER = SWAP
                '/' OVER = SWAP   DROP OR OR ;

: NUMERIC?      ( ADDR LEN -- F )
                BASE @ >R
                OVER C@ '$' =
                IF      1- SWAP 1+ SWAP HEX
                THEN    DUP 1 =
                IF      DROP C@ DIGIT?
                ELSE    1 -ROT   0 ?DO   DUP C@   DUP DIGIT? SWAP PUNCT? OR
                        ROT AND SWAP 1+   LOOP   DROP
                THEN    R> BASE ! ;

T: (    [COMPILE] (     T;
T: (    [COMPILE] (     T;
T: \    [COMPILE] \     T;

: STRING,-T     ( -- )
                '"' PARSE  DUP C,-T  S,-T  ALIGN  ;

: STRING,-X     ( -- )
                '"' PARSE  DUP C,-X  S,-X  ALIGN-X ;

                FORWARD: <(.")>
T: ."           [FORWARD]  <(.")>  STRING,-X   T;

                FORWARD: <(")>
T: "            [FORWARD] <(")>    HERE-T ,-X STRING,-T   T;

                FORWARD: <(ABORT")>
T: ABORT"       [FORWARD] <(ABORT")> STRING,-X   T;

                FORWARD_REL: <VARIABLE>
: CREATE        RECREATE
                232 C,-T
                [FORWARD] <VARIABLE>   HERE-T CONSTANT   ;

: VARIABLE      ( | name -- ) CREATE   0 ,-T   ;

                FORWARD_REL: <DEFER>
: DEFER         ( -- )
                TARGET-CREATE
                232 C,-T                        \ CALL instruction
                [FORWARD] <DEFER>   0 ,-T   ;

FORTH

VARIABLE #USER-T

META ALSO USER DEFINITIONS

: ALLOT         ( n -- )
                #USER-T +!   ;

                FORWARD_REL: <USER-VARIABLE>
: VARIABLE      ( -- )
                SWITCH   RECREATE
                232 C,-T
                [FORWARD] <USER-VARIABLE>   #USER-T @
                DUP ,-T   2 ALLOT   META DEFINITIONS   CONSTANT   SWITCH   ;

                FORWARD_REL: <USER-DEFER>
: DEFER         ( -- )
                SWITCH   TARGET-CREATE
                232 C,-T
                [FORWARD] <USER-DEFER>   SWITCH
                #USER-T @ ,-T   2 ALLOT   ;

ONLY FORTH ALSO META ALSO DEFINITIONS

FORTH
VARIABLE VOC-LINK-T
VARIABLE PHEAD-T
META

                FORWARD_REL: <VOCABULARY>
: VOCABULARY    ( -- )
                RECREATE
                232 C,-T                \ CALL instruction to DOVOC
                [FORWARD] <VOCABULARY>
                HERE-T   #TTHREADS 0 DO  0 ,-T  LOOP
                HERE-T VOC-LINK-T @ ,-T   VOC-LINK-T !
                CONSTANT DOES> @ CONTEXT-T !   ;

                FORWARD_REL: <POINTER>
: POINTER       ( d1 | name -- ) \ make a pointer of double d1 with name
                RECREATE
                232 C,-T                \ CALL instruction to DOPOINTER
                [FORWARD] <POINTER>
                HERE-T -ROT 0 ,-T
                HERE-T PHEAD-T @ ,-T PHEAD-T !
                15. D+
                D2/ D2/ D2/ D2/ DROP ,-T        \ compile paragraphs needed
                PHEAD-T @ ,-T
                CONSTANT DOES> DROP
                TRUE ABORT" Can't use POINTERS interpretively" ;

: IMMEDIATE     ( -- )
                WIDTH-T @
                IF ( Headers present? )
                64 ( Precedence Bit )   LAST-T @   CSET-Y   THEN   ;

FORWARD: <(;USES)>

FORTH

VARIABLE STATE-T

META

T: ;USES        ( -- )
                [FORWARD] <(;USES)>   IN-META ASSEMBLER
                !CSP   STATE-T OFF   T;

T: [COMPILE]    'T EXECUTE    T;

                FORWARD: <(IS)>
T: IS           [FORWARD] <(IS)>    T;
:  IS           'T  ( CR HERE COUNT TYPE TAB OVER H. )
                >BODY @ >BODY-T !-T ;

T: ALIGN   T;

T: EVEN    T;

: .SYMBOLS      ( -- )
                TARGET   CONTEXT @ HERE #TTHREADS 2* CMOVE  CR
                BEGIN   HERE 4 LARGEST  DUP
                WHILE   DUP L>NAME  DUP Y@ 31 AND 2+ ?LINE
                        ."  /  "  DUP .ID
                        NAME> >BODY @ U.
                        Y@ SWAP !
                        KEY? IF   EXIT   THEN
                REPEAT  2DROP   IN-META   ;

: .UNRESOLVED   ( -- )
                UNRESOLVED OFF
                FORWARD CONTEXT @ HERE #THREADS 2* CMOVE
                BEGIN   HERE #THREADS LARGEST   DUP
                WHILE   ?CR DUP L>NAME NAME> >BODY
                        RESOLVED? 0=
                        IF      >ATTRIB4 DUP L>NAME .ID >NORM SPACE
                                UNRESOLVED ON
                        THEN
                        Y@  SWAP !
                REPEAT  2DROP .UNRESOLVEPAUSE IN-META ;

: FIND-UNRESOLVED ( -- cfa f )  'F    DUP  >BODY RESOLVED?     ;

DECIMAL

: RESOLVE       ( taddr cfa -- )        \ resolve for CODE space
                >BODY   2DUP   TRUE OVER 2+ !   @
                BEGIN   DUP
                WHILE   2DUP @-T   -ROT SWAP
                        DUP 1-  C@-T 232 =            \ IF PRECEEDED BY CALL
                        IF      DUP 2+ ROT SWAP - SWAP \ SWITCH TO RELATIVE
                        THEN    !-T
                REPEAT  2DROP  ! ;

: RESOLVES      ( taddr -- )
                FIND-UNRESOLVED
                IF      CR >NAME .ID ." Already Resolved" DROP
                ELSE    RESOLVE   THEN   ;

: :RESOLVE      ( taddr cfa -- )        \ resolve for LIST space
                >BODY   2DUP   TRUE OVER 2+ !   @
                BEGIN   DUP
                WHILE   2DUP @-X   -ROT SWAP !-X
                REPEAT  2DROP  ! ;

: :RESOLVES     ( taddr -- )
                FIND-UNRESOLVED
                IF      CR >NAME .ID ." Already Resolved" DROP
                ELSE    :RESOLVE   THEN   ;

: H:    [COMPILE] :   ;

H: '     'T >BODY @   ;
H: ,    ,-T ;
H: C,  C,-T ;
H: X,   ,-X ;
H: XC, C,-X ;

H: HERE         HERE-T ;
H: XHERE        ( HERE-X ) TRUE ABORT" Used HERE-X" ;
H: ALLOT        ALLOT-T   ;
H: DEFINITIONS  DEFINITIONS   CONTEXT-T @ CURRENT-T !    ;

ONLY FORTH DEFINITIONS ALSO

.( Meta Compiler Loaded )

CR .ELAPSED CR

FLOAD KERNEL1.SEQ
FLOAD VIDEO.SEQ
FLOAD KERNEL2.SEQ
FLOAD VIDEO2.SEQ
FLOAD KERNEL3.SEQ
FLOAD EXPAND.SEQ
FLOAD EMMEXEC.SEQ
FLOAD POINTER.SEQ
FLOAD EQUCOLON.SEQ
FLOAD SAVEREST.SEQ
FLOAD HANDLES.SEQ
FLOAD SEQREAD.SEQ
FLOAD FPATH.SEQ
FLOAD DEFAULT.SEQ
FLOAD HCRITICA.SEQ
FLOAD KERNEL4.SEQ       \ 05/25/90 tjz

ALSO META

SIZE-SAVE       \ Write the 64k image of CFA sizes to KERNEL.SIZ

PREVIOUS

CAPS ON
 8 TABSIZE !    \ RESTORE TABS
70 RMARGIN !    \ RESTORE RIGHT MARGIN
?PAGE           \ NEW PAGE
PRINTING OFF    \ NO PRINTING ANY MORE
0 24 AT           \ Go back there.
CR CR
