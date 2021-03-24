\ PASM.SEQ    PREFIX & POSTFIX assembler by Robert L. Smith & Tom Zimmer

comment:

  An assembler for the 8086/8088, with both Prefix and Postfix syntax.

  PASM defaults to Prefix notation, but can be switched to F83 style
Postfix notation with the word POSTFIX. To revert back to Prefix notation,
use PREFIX.

  See the file ASSEM.TXT for a further description of the syntax.

comment;

: %"ERR         ( a1 n1 -- )    \ display the error message
                true ?error ;

DEFER "ERR      ( a1 n1 -- )    ' %"ERR IS "ERR

0 VALUE ?LISTING
0 VALUE LRUNSAVE
0 VALUE LINESTRT
DEFER LIHERE    ' HERE IS LIHERE
DEFER LIC@      ' C@   IS LIC@

: <LRUN>        ( -- )
                LIHERE =: LINESTRT
                <RUN>
                BASE @ >R HEX
                CR LINESTRT 4 U.R SPACE
                LIHERE
                IF      LINESTRT LIHERE OVER - 5 MIN BOUNDS
                        ?DO     I LIC@ 0 <# # # BL HOLD #> TYPE
                        LOOP
                THEN    22 #OUT @ - SPACES
                TIB #TIB @ TYPE
                R> BASE ! ;

: /LISTING      ( -- )
                ON> ?LISTING
                LRUNSAVE ABORT" Already LISTING!"
                @> RUN =: LRUNSAVE
                ['] <LRUN> IS RUN ;

: /NOLISTING    ( -- )
                OFF> ?LISTING
                LRUNSAVE IS RUN
                OFF> LRUNSAVE ;

DEFER .INST     ' NOOP IS .INST

\ The ASSEMBLER follows:
ONLY FORTH ALSO ASSEMBLER DEFINITIONS ALSO

2VARIABLE APRIOR  4 ALLOT

        ' DROP APRIOR ! ' DROP APRIOR 4 + !

: <A;!>         ( A1 A2 --- )           \ Set up assembly instruction
                APRIOR 4 + 2! ;         \ completion function

: <A;>          ( --- )
                APRIOR 2@ EXECUTE       \ perform assembly completion
                APRIOR 4 + 2@ APRIOR 2! \ SET UP FOR NEXT PREVIOUS
                ['] DROP APRIOR 4 + !   \ Make it not care if it is redone.
                .INST ;

: <RUN-A;>      ( --- )                 \ make sure we complete instruction
                ?LISTING
                IF      LIHERE =: LINESTRT
                        <RUN> <A;>
                        BASE @ >R HEX
                        CR LINESTRT 4 U.R SPACE
                        LIHERE
                        IF      LINESTRT LIHERE OVER - 5 MIN BOUNDS
                                ?DO     I LIC@ 0 <# # # BL HOLD #> TYPE
                                LOOP
                        THEN    22 #OUT @ - SPACES
                        TIB #TIB @ TYPE
                        R> BASE !
                ELSE    <RUN> <A;>      \ at the end of each line.
                THEN    ;

VARIABLE POSTVAR                        \ is this post fix notation?

FORTH DEFINITIONS

DEFER A;!       ' <A;!>    IS A;!
DEFER A;        ' <A;>     IS A;
DEFER RUN-A;    ' <RUN-A;> IS RUN-A;

: PREFIX        ( --- )
                ['] <A;!>    IS A;!
                ['] <A;>     IS A;
                ['] <RUN-A;> IS RUN-A;  POSTVAR OFF ;

: POSTFIX       ( --- )
                ['] EXECUTE  IS A;!
                ['] NOOP     IS A;
                ['] <RUN>    IS RUN-A;  POSTVAR ON ;

PREFIX          \ Default is PREFIX assembler.

: >PRE          2R> POSTVAR @ >R 2>R PREFIX ;    \ SAVE AND SET PREFIX

: PRE>          2R> R> IF POSTFIX THEN 2>R ;     \ RESTORE PREVIOUS FIX

ASSEMBLER DEFINITIONS

DEFER C,        FORTH ' C,      ASSEMBLER IS C,
DEFER ,         FORTH ' ,       ASSEMBLER IS ,
DEFER HERE      FORTH ' HERE    ASSEMBLER IS HERE       ' HERE IS LIHERE
DEFER TC!       FORTH ' C!      ASSEMBLER IS TC!
DEFER TC@       FORTH ' C@      ASSEMBLER IS TC@        ' TC@  IS LIC@
DEFER T!        FORTH ' !       ASSEMBLER IS T!
DEFER IC,             ' C,                IS IC,        \ instruction C,
DEFER CC,             ' C,                IS CC,        \ CALL C,
DEFER OC,             ' C,                IS OC,
DEFER O,              ' ,                 IS O,

DEFER ?>MARK
DEFER ?>RESOLVE
DEFER ?<MARK
DEFER ?<RESOLVE

FORTH DEFINITIONS

' <RUN> VALUE ARUNSAVE

DEFER ASMSTUFF  ' NOOP IS ASMSTUFF

: DOASSEM       ( --- )
                @> RUN =: ARUNSAVE
                  ['] RUN-A; IS RUN
                0 ['] DROP A;!
                APRIOR 4 + 2@ APRIOR 2!
                LIHERE =: LINESTRT
                ASMSTUFF
                ALSO ASSEMBLER ;

' DOASSEM IS SETASSEM

: LABEL         ( NAME --- )            \ Really just a constant addr
                SETASSEM CREATE ;

: CODE          ( NAME --- )
                LABEL -3 DP +! HIDE ;

0 VALUE ASM->IN                 \ a place to save >IN for current instruction

ASSEMBLER DEFINITIONS

: ISETUP!       ( a1 a2 -- )    \ setup current instruction & assemble prev
                A;! A;
                >IN_WORD @ !> ASM->IN ;

DEFER END-ASMSTUFF      ' NOOP IS END-ASMSTUFF  \ stuff to do at end of assy

: END-CODE
                END-ASMSTUFF
                ARUNSAVE IS RUN
                PREVIOUS A; REVEAL ;

' END-CODE ALIAS C;

\ 8088 Assembler, based on Ray Duncan's Dr. Dobb's article.

I: ERROR3       ( -- )
                ['] DROP APRIOR 4 + !   \ Make it not care if it is redone.
                TRUE ABORT"  Illegal Operand "  ;

I: ?ORDERERROR  ( f1 -- )
                IF      ['] DROP APRIOR 4 + !
                        " Wrong Operand Order! " "err abort
                THEN    ;

headerless

VARIABLE <TD>  VARIABLE <TS>   VARIABLE <RD>   VARIABLE <RS>
VARIABLE <W>   VARIABLE <WD>   VARIABLE <OD>   VARIABLE <OS>   VARIABLE <D>
VARIABLE <FR>  VARIABLE <AO>   VARIABLE <ND>   VARIABLE <DST>
VARIABLE <SST> VARIABLE <WS>   VARIABLE <ID>

: D>S           ( --- )                 \ Move destination to source.
                <TD> @ <TS> !
                <RD> @ <RS> !
                <OD> @ <OS> ! ;

: ?D>S          ( --- )                 \ Move Dest to Src if postfix
                <TS> @ 0=               \ If no source specified
                POSTVAR @ 0<> AND       \ and we are in postfix mode
                IF      D>S             \ Move destination to source
                THEN    ;

: ?D><S         ( --- )                 \ If no destinatiion specified
                <DST> @                 \ yet, then swap source and dest.
                IF      <TD> <TS> 2DUP @ SWAP @ ROT ! SWAP !
                        <RD> <RS> 2DUP @ SWAP @ ROT ! SWAP !
                        <OD> <OS> 2DUP @ SWAP @ ROT ! SWAP !
                THEN    <DST> OFF ;

: <SREG>        ( A1 --- )
                POSTVAR @
                IF      <DST> OFF       \ Only reset dest if postfix
                THEN    <SST> ON
                DUP C@ DUP $0FF = IF DROP ELSE DUP <W> ! <WS> ! THEN
                1+ DUP C@ <TS> !
                1+ C@ <RS> !  <TS> @ 4 = IF <OS> ! THEN ;

: <DREG>        ( A1 --- )
                <DST> ON
                DUP C@ DUP $0FF = IF DROP ELSE DUP <W> !  <WD> ! THEN
                1+ DUP C@ <TD> !  1+ C@ <RD> !
                <TD> @ 4 = IF <OD> ! THEN ;

HEADERS         \ 05/28/90 21:20:16.87 TJZ

\ Destination Register processing.

: DREG          CREATE C, C, C, DOES> POSTVAR @
                IF      <SREG>
                ELSE    <DREG>
                THEN    ;

\ Source Register processing.

: SREG          CREATE C, C, C, DOES> POSTVAR @
                IF      <SST> @ IF <DREG> ELSE <SREG> THEN
                ELSE    <SREG>
                THEN    ;

\ Source Register Definitions

\    Reg  Type W        Name    Reg  Type W        Name
     0    2    0  SREG  AL      0    3    1  SREG  AX
     1    2    0  SREG  CL      1    3    1  SREG  CX
     2    2    0  SREG  DL      2    3    1  SREG  DX
     3    2    0  SREG  BL      3    3    1  SREG  BX
     4    2    0  SREG  AH      4    3    1  SREG  SP
     5    2    0  SREG  CH      5    3    1  SREG  BP   ' BP ALIAS RP
     6    2    0  SREG  DH      6    3    1  SREG  SI   ' SI ALIAS IP
     7    2    0  SREG  BH      7    3    1  SREG  DI


     0    4    -1 SREG  [BX+SI]         ' [BX+SI]  ALIAS [SI+BX]
                                        ' [BX+SI]  ALIAS [BX+IP]
                                        ' [BX+SI]  ALIAS [IP+BX]
     1    4    -1 SREG  [BX+DI]         ' [BX+DI]  ALIAS [DI+BX]
     2    4    -1 SREG  [BP+SI]         ' [BP+SI]  ALIAS [SI+BP]
                                        ' [BP+SI]  ALIAS [BP+IP]
                                        ' [BP+SI]  ALIAS [IP+BP]
                                        ' [BP+SI]  ALIAS [RP+IP]
                                        ' [BP+SI]  ALIAS [IP+RP]
                                        ' [BP+SI]  ALIAS [RP+SI]
                                        ' [BP+SI]  ALIAS [SI+RP]
     3    4    -1 SREG  [BP+DI]         ' [BP+DI]  ALIAS [DI+BP]
                                        ' [BP+DI]  ALIAS [DI+RP]
                                        ' [BP+DI]  ALIAS [RP+DI]
     4    4    -1 SREG  [SI]            ' [SI] ALIAS [IP]
     5    4    -1 SREG  [DI]
     6    4    -1 SREG  [BP]            ' [BP] ALIAS [RP]
     7    4    -1 SREG  [BX]

     0    5    -1 SREG  ES
     1    5    -1 SREG  CS
     2    5    -1 SREG  SS
     3    5    -1 SREG  DS
                                                      
\ Destination Register Definitions                    

     0    5    -1 DREG  ES,
     1    5    -1 DREG  CS,
     2    5    -1 DREG  SS,
     3    5    -1 DREG  DS,
                                                 
     0    2    0  DREG  AL,
     1    2    0  DREG  CL,
     2    2    0  DREG  DL,
     3    2    0  DREG  BL,
     4    2    0  DREG  AH,
     5    2    0  DREG  CH,
     6    2    0  DREG  DH,
     7    2    0  DREG  BH,

     0    3    1  DREG  AX,
     1    3    1  DREG  CX,
     2    3    1  DREG  DX,
     3    3    1  DREG  BX,
     4    3    1  DREG  SP,
     5    3    1  DREG  BP,             ' BP, ALIAS RP,
     6    3    1  DREG  SI,             ' SI, ALIAS IP,
     7    3    1  DREG  DI,

     0    4    -1 DREG  [BX+SI],        ' [BX+SI], ALIAS [SI+BX],
                                        ' [BX+SI], ALIAS [BX+IP],
                                        ' [BX+SI], ALIAS [IP+BX],
     1    4    -1 DREG  [BX+DI],        ' [BX+DI], ALIAS [DI+BX],
     2    4    -1 DREG  [BP+SI],        ' [BP+SI], ALIAS [SI+BP],
                                        ' [BP+SI], ALIAS [BP+IP],
                                        ' [BP+SI], ALIAS [IP+BP],
                                        ' [BP+SI], ALIAS [RP+SI],
                                        ' [BP+SI], ALIAS [SI+RP],
                                        ' [BP+SI], ALIAS [RP+IP],
                                        ' [BP+SI], ALIAS [IP+RP],
     3    4    -1 DREG  [BP+DI],        ' [BP+DI], ALIAS [DI+BP],
                                        ' [BP+DI], ALIAS [DI+RP],
                                        ' [BP+DI], ALIAS [RP+DI],
     4    4    -1 DREG  [SI],           '    [SI], ALIAS [IP],
     5    4    -1 DREG  [DI],
     6    4    -1 DREG  [BP],           '    [BP], ALIAS [RP],
     7    4    -1 DREG  [BX],

\ Miscellaneous Operators
: TS@     <TS> @ ;
: TD@     <TD> @ ;
: RD@     <RD> @ ;
: RS@     <RS> @ ;

HEADERLESS      \ 05/28/90 21:20:52.57 TJZ

: +W      <W> @ + ;
: +RD     <RD> @ + ;
: +RS     <RS> @ + ;
: MOD1    $03F AND $040 OR ;
: MOD2    $03F AND $080 OR ;
: MOD3    $03F AND $0C0 OR ;

\ Equates to Addressing Modes

0 CONSTANT DIRECT       1 CONSTANT IMMED     2 CONSTANT REG8
3 CONSTANT REG16        4 CONSTANT INDEXED   5 CONSTANT SEGREG

\ Initialize all variables and flags

headers

: ?STACK_CHECK  ( -- )
                [ forth ]
                depth 0<
                IF      SP0 @ SP!               \ reset stack to empty
                        here off                \ clear here to null
                        " Invalid addressing mode! " "err abort
                THEN  ;

: RESET         ( -- )
                ?STACK_CHECK
                0 <W> !   0 <OS> !  0 <RD> !
                0 <TD> !  0 <TS> !  0 <OD> !
                0 <D> !   0 <WD> !  0 <RS> !  0 <FR> !  0 <ND> !
                0 <DST> ! 0 <SST> ! 0 <WS> !  0 <ID> !  ;

headerless

: REG?     REG8 OVER = SWAP REG16 = OR ;

: DREG?   TD@ REG? ;

: ADREG?  DREG? RD@ ( 3 AND ) 0= AND ;          \ AX destination

: ASREG?  TS@ REG? RS@ ( 3 AND ) 0= AND ;       \ AX source

: SUBREG  C@ $038 AND ;

\ Init. Direction Pointer

: DSET    TS@ DUP INDEXED = SWAP DIRECT = OR NEGATE <D> ! ;

: DT      1 <D> ! ;    \ Set Direction Flag True.

HEADERS         \ 05/28/90 21:21:36.34 TJZ

: reserr        ( targ_addr -- )
                drop
                " Illegal forward resolution type" "err abort ;

: res_byte/rel  ( targ_addr -- )        \ resolve a byte relative targ addr
                here over 1+ - dup $-80 $7F between 0=
                if      "  Byte Relative Address out of range " "err
                then    swap TC! ;

: res_word/rel  ( targ_addr -- )        \ resolve a word relative targ addr
                here over 2+ - swap T! ;

: res_signed_byte/off  ( targ_addr -- )
                here dup $-80 $7F between 0=
                if      "  Signed offset out of range " "err
                then    swap TC! ;      \ resolve a byte OFFSET targ addr

: res_byte/off  ( targ_addr -- )
                here dup $00 $FF between 0=
                if      "  Byte Offset out of range " "err
                then    swap TC! ;      \ resolve a byte OFFSET targ addr

: res_word/off  ( targ_addr -- )
                here swap T! ;          \ resolve a word OFFSET targ addr

: resolve_8086  ( targ_addr res_type -- )       \ resolve a for ref
                0max 5 min exec:
                reserr                                  \ any lower = error
                res_byte/rel                            \ type 1
                res_word/rel                            \ type 2
                res_byte/off                            \ type 3
                res_word/off                            \ type 4
                res_signed_byte/off                     \ type 5
                reserr ;                                \ any higher = error

DEFER ASM!LREF          ' 2DROP IS ASM!LREF     \ save forward ref info

: RELATIVE_8,   ( a1 -- )
                here 1+ - dup $-80 $7F between 0=
                if      "  RELATIVE Address out of range" "err
                then    here 1 asm!lref                 \ res type 1
                OC, ;                                   \ 8bit RELATIVE

: RELATIVE_16,  ( a1 -- )
                here 2+ -
                here 2 asm!lref                         \ res type 2
                O, ;                                    \ 16bit RELATIVE

: SIGNED_8,     ( a1 -- )
                dup $-80 $7F between 0=
                if      "  SIGNED_OFFSET Address out of range" "err
                then    here 5 asm!lref                 \ res type 5
                OC, ;                                   \ 8bit SIGNED OFFSET

: OFFSET_8,     ( a1 -- )
                dup $00 $FF between 0=
                if      "  OFFSET Address out of range" "err
                then    here 3 asm!lref                 \ res type 3
                OC, ;                                   \ 8bit OFFSET

: OFFSET_16,    ( a1 -- )
                here 4 asm!lref                         \ res type 4
                O, ;                                    \ 16bit OFFSET

\ Calculate and store displacement for MEM/REG Instructions.

: DISP,   <D> @ IF      <OS>
                ELSE    <OD>
                THEN    @ DUP
                IF      DUP ABS $07F >
                        IF      SWAP MOD2 OC, OFFSET_16,
                        ELSE    SWAP MOD1 OC, SIGNED_8,
                        THEN
                ELSE    DROP DUP 7 AND 6 =      \ if r/m = (BP)+DISP & DISP=0
                        IF      MOD1 OC, 0
                        THEN    OC,
                THEN    ;

\ Calculate the M/R 2nd operator byte

: M/RS,   $038 AND TS@
          CASE DIRECT  OF 6 + OC, OFFSET_16,         ENDOF
             REG8    OF $0C0 + +RS OC,               ENDOF
             REG16   OF $0C0 + +RS OC,               ENDOF
             INDEXED OF <OS> @ 0= RS@ 6 <> AND
                        IF      +RS OC,
                        ELSE    <OS> @ $-80 $7F between
                                                        \ $080 + $0100 U<
                                IF     $040 + +RS OC, <OS> @ SIGNED_8,
                                ELSE   $080 + +RS OC, <OS> @ OFFSET_16,
                                THEN
                        THEN                         ENDOF
                        ERROR3
                        drop
          ENDCASE       ;

HEADERLESS      \ 05/28/90 21:21:52.99 TJZ

: M/RD,         ( ? --- ) D>S M/RS, ;

: 8/16,         ( a1 -- )
                <W> @
                IF      OFFSET_16,
                ELSE    OFFSET_8,
                THEN    ;

\ Words to build the instructions:

: 1MIF          ( A1 --- )
                C@ IC, RESET ;           \ Single Byte Inst.

: 1MI           CREATE C, DOES> ['] 1MIF ISETUP! ;

: 1AMIF         ( A1 --- )              \ AX LODS or AX STOS
                C@ +W IC, RESET ;           \ Single Byte Inst.

: 1AMI          CREATE C, DOES> ['] 1AMIF ISETUP! ;

: 2MIF          ( A1 --- )
                C@ IC, RELATIVE_8, RESET ;      \ Cond Jumps, Loops

: 2MI           CREATE C, DOES> ['] 2MIF ISETUP! ;

: 3MI           CREATE C, DOES> C@ IC, ;        \ Segment Over-ride

: 4MIF          ( A1 --- )
                ?D>S TS@                        \ Reg. Push and Pop
          CASE
                SEGREG OF C@ RS@ 8 * + IC,     ENDOF    \ SEGMENT
                REG16  OF 1+ C@ +RS IC,        ENDOF    \ REGISTER
                REG8   OF ERROR3               ENDOF    \ 8 BIT ILLEGAL
                          DROP 2+ C@ DUP IC, $030 AND M/RS,
          ENDCASE                                       \ MEMORY
          RESET ;

: 4MI           CREATE C, C, C, DOES> ['] 4MIF ISETUP! ;

: 5MIF          ( A1 --- )
                ?D>S TS@                        \ Iseg. Jump, Call
          CASE DIRECT  OF   <ND> @
                            IF   $0FF CC, C@ <FR> @
                                 IF  8 +  THEN  M/RS,
                            ELSE <FR> @
                                 IF  2+ C@ CC, O, O,
                                 ELSE  OVER HERE 3 + - $080 + $0100 U<
                                         OVER C@ $020 = AND
                                         <WD> @ 0= AND
                                         IF  DROP $0EB CC, RELATIVE_8,
                                         ELSE 1+ C@ CC, RELATIVE_16,
                                         THEN
                                 THEN
                            THEN                                ENDOF
             REG16   OF     $0FF CC, C@ MOD3 +RS OC,            ENDOF
             INDEXED OF     DSET $0FF CC, C@ <FR> @
                            IF  8 +  THEN  +RS DISP,            ENDOF
             ERROR3  DROP
          ENDCASE    RESET ;

: 5MI           CREATE C, C, C, DOES> ['] 5MIF ISETUP! ;

: 6MIF          ( A1 --- )      \ IN and OUT
                DUP C@ 2 AND            \ IN or OUT?
                IF      <WS> @          \ This is an OUT
                        ADREG? ?ORDERERROR
                ELSE    <WD> @          \ This is an IN
                        ASREG? ?ORDERERROR
                THEN    SWAP <ID> @     \ WAS THERE IMMEDIATE DATA ?
                IF         C@ + ( +W ) IC, OC,
                ELSE    1+ C@ + ( +W ) IC,
                THEN    RESET ;


: 6MI           CREATE C, C, DOES> ['] 6MIF ISETUP! ;

\ ADC, ADD, AND, etc.

: 7MIF          ( A1 --- )
                DUP 1+ C@ 1 AND <AO> !
          TS@ IMMED =
          IF   \ 12/17/92 15:44 tjz added following line to correct for
               \ WORD size numbers when the user doesn's specify WORD
               OVER $100 $FF7F UBETWEEN IF 1 <W> ! THEN
               ADREG?
               IF 2+ C@ +W IC, TD@ REG8 = IF OFFSET_8, ELSE OFFSET_16, THEN
               ELSE DUP 1+ C@ $0FE AND +W ROT >R  \ Save IMMEDiate data
                    <AO> @
                    <W> @ AND                     \  *** 07/22/88 10:07:40.64
                    IF  R@ $080 + $0100 U<
                         IF     2 OR IC, C@ M/RD, R@ SIGNED_8,
                         ELSE        IC, C@ M/RD, R@ OFFSET_16,
                         THEN
                    ELSE             IC, C@ M/RD, R@ 8/16,
                    THEN   r>drop              \ Clean Return stack
               THEN
          ELSE C@ TS@ REG?
               IF +W IC, RS@ 8 * M/RD,
               ELSE $084 OVER - IF 2 OR THEN +W IC, TD@ REG?
                    IF RD@ 8 * M/RS, ELSE ERROR3 THEN
               THEN
          THEN RESET ;

: 7MI           CREATE C, C, C, DOES> ['] 7MIF ISETUP! ;

: 8MIF          ( A1 --- )
                ?D>S
                DUP 1+ C@ +W IC, C@ M/RS, RESET ;

: 8MI           CREATE C, C, DOES> ['] 8MIF ISETUP! ;

: 9MIF          ( A1 --- )
                <DST> @ 0=
                IF      1 <DST> ! ?D><S
                        1 <TS> ! 1 <SST> !      \ : #  1 <TS> !  1 <SST> ! ;
                        1 SWAP  <W> @ <WD> !
                ELSE    POSTVAR @               \ If postfix, reverse
                        IF      ?D><S           \ the operands
                                <WS> @ <WD> !   \ Correct word mode
                        THEN
                THEN
                DUP 1+ C@ <WD> @ +
          TS@ 1 > IF 2+ IC, ELSE IC, NIP THEN  C@ M/RD, RESET ;

: 9MI           CREATE C, C, DOES> ['] 9MIF ISETUP! ;

: 10MIF         ( A1 --- )
                DUP 1+ C@ IC, C@ OC, RESET ;

: 10MI          CREATE C, C, DOES> ['] 10MIF ISETUP! ;

: 11MIF         ( A1 --- )
                ?D>S TS@ REG? <W> @ 0<> AND
                IF C@ +RS IC, ELSE 1+ C@ $0FE +W IC, M/RS, THEN RESET ;

: 11MI          CREATE C, C, DOES> ['] 11MIF ISETUP! ;

: 12MIF         ( A1 --- )
                DROP                    \ MOV Instruction
            TD@ SEGREG = IF $08E IC,  RD@ 8 * M/RS,   ELSE
            TS@ SEGREG = IF $08C IC,  RS@ 8 * M/RD,   ELSE
            TS@ IMMED = TD@ REG? AND
                IF $016 +W 8 * +RD IC, 8/16,          ELSE
            TS@ 0= TD@ 0= OR ADREG? ASREG? OR AND
                IF $0A0 +W TS@ IF 2+ THEN IC, OFFSET_16, ELSE
            TS@ IMMED =
                IF      postvar @
\ *****  09/26/88 18:33:25.98  *******  ZIMMER ***********
                        TD@ INDEXED <> AND
                        if swap then
                        $0C6 +W IC, >R 0 M/RD, R> 8/16, ELSE
            $088 +W TD@ REG?
                        IF 2+ IC, RD@ 8 * M/RS,      ELSE
            TS@ REG? IF IC, RS@ 8 * M/RD, ELSE ERROR3    THEN THEN THEN THEN
                                                        THEN THEN THEN
          RESET ;

: 12MI          CREATE DOES> ['] 12MIF ISETUP! ;

: 13MIF         ( A1 --- )
                DROP    TS@ REG? TD@ REG? AND   \ Both are registers
                        RS@ 0= RD@ 0= OR AND    \ Either register is AX
                        <W> @ 1 = AND           \ And it is AX not AL.
        IF      RS@ 0=
                IF      RD@
                ELSE    RS@
                THEN    $090 + IC,
        ELSE    $086 +W             \ XCHG Instruction
          TS@ REG? 0=
              IF TD@ REG? 0=
                   IF   ERROR3
                   ELSE IC,
                        RD@ 8 * M/RS,
                   THEN
              ELSE IC, RS@ 8 * M/RD,
              THEN
        THEN    RESET ;

: 13MI          CREATE DOES> ['] 13MIF ISETUP! ;

: 14MIF         ( A1 --- )
                C@ IC, TD@ REG?
              IF RD@ 8 * M/RS, ELSE ERROR3 THEN RESET ;

: 14MI          CREATE C, DOES> ['] 14MIF ISETUP! ;

: 15MIF         ( A1 --- )
                DROP DUP 3 =
                IF DROP $0CC IC, ELSE $0CD IC, OC, THEN RESET ;

: 15MI          CREATE DOES> ['] 15MIF ISETUP! ;

headers

\ Now let's create the actual instructions.

$37          1MI   AAA      $FC          1MI   CLD
$D5 $0A     10MI   AAD      $FA          1MI   CLI
$D4 $0A     10MI   AAM      $F5          1MI   CMC
$3F          1MI   AAS      $3C $81 $38  7MI   CMP
$14 $81 $10  7MI   ADC      $A6          1MI   CMPSB
$04 $81 $00  7MI   ADD      $A7          1MI   CMPSW
$24 $80 $20  7MI   AND      $99          1MI   CWD
$9A $E8 $10  5MI   CALL     $27          1MI   DAA
$98          1MI   CBW      $2F          1MI   DAS
$F8          1MI   CLC      $08 $48     11MI   DEC

$F6 $30     8MI   DIV      $73        2MI   JAE
$F4         1MI   HLT      $72        2MI   JB
$F6 $38     8MI   IDIV     $76        2MI   JBE
$F6 $28     8MI   IMUL     $72        2MI   JC
$EC $E4     6MI   IN       $E3        2MI   JCXZ
$00 $40    11MI   INC      $74        2MI   JE
           15MI   INT      $7F        2MI   JG
$CE         1MI   INTO     $7D        2MI   JGE
$CF         1MI   IRET     $7C        2MI   JL
$77         2MI   JA       $7E        2MI   JLE

$EA $E9 $20 5MI   JMP      $7F        2MI   JNLE
$76         2MI   JNA      $71        2MI   JNO
$72         2MI   JNAE     $7B        2MI   JNP
$73         2MI   JNB      $79        2MI   JNS
$77         2MI   JNBE     $75        2MI   JNZ
$73         2MI   JNC      $70        2MI   JO
$75         2MI   JNE      $7A        2MI   JP
$7E         2MI   JNG      $7A        2MI   JPE
$7C         2MI   JNGE     $7B        2MI   JPO
$7D         2MI   JNL      $78        2MI   JS

$74        2MI   JZ       $E0        2MI   LOOPNE
$9F        1MI   LAHF     $E0        2MI   LOOPNZ
$C5       14MI   LDS      $E1        2MI   LOOPZ
$8D       14MI   LEA                12MI   MOV
$C4       14MI   LES      $A4        1MI   MOVSB
$F0        1MI   LOCK     $A5        1MI   MOVSW  $A5      1MI   MOVS
$AC        1MI   LODSB    $F6 $20    8MI   MUL    $AC      1AMI  LODS
$AD        1MI   LODSW    $F6 $18    8MI   NEG
$E2        2MI   LOOP     $90        1MI   NOP
$E1        2MI   LOOPE    $F6 $10    8MI   NOT

$0C $80 $08 7MI  OR       $F2         1MI   REPNE
$EE $E6     6MI  OUT      $F2         1MI   REPNZ
$8F $58 $07 4MI  POP      $F3         1MI   REPZ
$9D         1MI  POPF     $C3         1MI   RET
                          $CB         1MI   RETF
$FF $50 $06 4MI  PUSH     $D0 $00     9MI   ROL
$9C         1MI  PUSHF    $D0 $08     9MI   ROR
$D0 $10     9MI  RCL      $9E         1MI   SAHF
$D0 $18     9MI  RCR      $D0 $38     9MI   SAR
$F3         1MI  REP      $1C $81 $18 7MI   SBB
$F3         1MI  REPE     $AE         1MI   SCASB

$AF        1MI   SCASW    $AB         1MI   STOSW  $AA    1AMI   STOS
$D0 $20    9MI   SAL      $2C $81 $28 7MI   SUB
$D0 $20    9MI   SHL      $A8 $F6 $84 7MI   TEST
$D0 $28    9MI   SHR      $9B         1MI   WAIT
$F9        1MI   STC                 13MI   XCHG
$FD        1MI   STD      $D7         1MI   XLAT
$FB        1MI   STI      $34 $80 $30 7MI   XOR
$AA        1MI   STOSB    \               ESC

\ =========================================================
\               BEGIN MNEMONIC JUMP SECTION:
\ =========================================================

\ The jump mnemonics:

' jmp  alias j          ( JMP  )
' jne  alias j0<>       ( JNE  )
' jz   alias j0=        ( JZ   )
' jns  alias j0>=       ( JNS  )
' js   alias j0<        ( JS   )
' jne  alias j<>        ( JNE  )
' jz   alias j=         ( JZ   )
' jnl  alias j>=        ( JNL  )
' jnge alias j<         ( JNGE )
' jnle alias j>         ( JNLE )
' jng  alias j<=        ( JNG  )
' jnc  alias ju>=       ( JNC  )
' jnae alias ju<        ( JNAE )
' jnbe alias ju>        ( JNBE )
' jna  alias ju<=       ( JNA  )

\ =========================================================
\               END MNEMONIC JUMP SECTION:
\ =========================================================

\ Segment over-ride commands:
$26        3MI   ES:
$2E        3MI   CS:
$36        3MI   SS:
$3E        3MI   DS:

: FAR     1 <FR> ! ;

: BYTE    0 <W> !   0 <WD> ! ;

: WORD    1 <W> !   1 <WD> ! ;

: #       1 <TS> ! -1 <SST> ! 1 <ID> ! ;

: #)      ( ?D><S ) -1 <SST> !   \ Swap source and dest if no dest spec'ed.
          1 <W> ! ;                \ Default to word mode

: []      0 <W> !  1 <ND> ! ;

: 3*      DUP 2* + ;

\ MACROS for NEXT, 1PUSH, and 2PUSH.

VARIABLE INLN           \ Flag to determine if we are compiling IN_LINE next.

: INLINEON      INLN ON ;
: INLINEOFF     INLN OFF ;      INLINEOFF       \ Default to NO INLINE NEXT.

: NEXT          ( -- )
                >PRE    INLN @
                IF      LODSW ES: JMP AX    A;
                ELSE              JMP >NEXT A;
                THEN    PRE> ;

: 1PUSH         ( -- )
                >PRE    INLN @
                IF      PUSH AX LODSW ES: JMP AX       A;
                ELSE                      JMP >NEXT 1- A;
                THEN    PRE> ;

: 2PUSH         ( -- )
                >PRE    INLN @
                IF      PUSH DX PUSH AX LODSW ES: JMP AX       A;
                ELSE                              JMP >NEXT 2- A;
                THEN    PRE> ;

headerless

: A?>MARK    ( -- f addr ) TRUE   HERE   0 OC,   ;
: A?>RESOLVE ( f addr -- ) HERE OVER 1+ - SWAP TC! ?CONDITION ;
: A?<MARK    ( -- f addr ) TRUE   HERE   ;
: A?<RESOLVE ( f addr -- ) HERE 1+ -  OC,   ?CONDITION   ;
' A?>MARK    ASSEMBLER IS ?>MARK
' A?>RESOLVE ASSEMBLER IS ?>RESOLVE
' A?<MARK    ASSEMBLER IS ?<MARK
' A?<RESOLVE ASSEMBLER IS ?<RESOLVE

headers

$75 CONSTANT 0=   $74 CONSTANT 0<>   $79 CONSTANT 0<
$78 CONSTANT 0>=  $7D CONSTANT <     $7C CONSTANT >=
$7F CONSTANT <=   $7E CONSTANT >     $73 CONSTANT U<
$72 CONSTANT U>=  $77 CONSTANT U<=   $76 CONSTANT U>
$70 CONSTANT OV<> $71 CONSTANT OV    $E3 CONSTANT CX<>0
$7B CONSTANT PE   $7A CONSTANT PO

\ : DO  ( n --- ) MOV CX # A; HERE ;

: BEGIN ( - a f ) A; ?<MARK ;
: UNTIL ( a f n - ) >R A; R> IC, ?<RESOLVE A; ;  \ ** ADDED A;
: AGAIN ( a f - ) $0EB UNTIL ;
: IF ( n - A f ) >R A; R> IC, ?>MARK A; ;        \ ** ADDED A;
: FORWARD ( - A f ) $0EB IF ;
: THEN ( A f - ) A; ?>RESOLVE ;
: AFT ( a f - a f A f ) 2DROP FORWARD BEGIN 2SWAP ;
: ELSE ( A f - A f ) FORWARD 2SWAP THEN ;
: REPEAT ( A f a f - ) A; AGAIN THEN ;
: CONTINUE (  a f A f - a f ) 2OVER REPEAT ;
: WHILE ( a f - A f a f ) IF 2SWAP ;


FORTH DEFINITIONS

: INLINE        [COMPILE] [ SETASSEM HERE X, ; IMMEDIATE

ASSEMBLER DEFINITIONS

: END-INLINE    [ ASSEMBLER ] END-CODE ] ;

COMMENT:
        \ Here is an example of how to use INLINE and END-INLINE to add
        \ assembly code in the middle of a CODE definition.

        : TEST  ( --- )
                5 0
                DO I
                        INLINE
                                pop ax
                                add ax, # 23
                                1push
                        END-INLINE
                        .
                LOOP ;
COMMENT;

ONLY FORTH DEFINITIONS ALSO

DECIMAL

