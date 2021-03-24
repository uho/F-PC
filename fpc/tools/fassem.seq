\ FASSEM.SEQ   Floating point assembler extensions

\ CR .( 8087/80287 Assembler extensions..)

\ HEX
ALSO FORTH ALSO ASSEMBLER ALSO DEFINITIONS

VARIABLE WAIT? WAIT? ON
VARIABLE <FW>

FALSE VALUE 8087NPU     \ Change this for shorter code with 80287 or 80387

: NOWAIT WAIT? OFF ;

: COMP-WAIT
        8087NPU WAIT? @ [ FORTH ] AND
        IF      $9B IC, ( WAIT )
        THEN
        WAIT? ON ;

: FPSTACK?   ( -- f )
        [ FORTH ] TS@ 6 = ;

\ Floating Point Source Registers

\     Reg  Type  W        Name
      0    6     1  SREG  ST
      0    6     1  SREG  ST0
      0    6     1  SREG  ST(0)
      1    6     1  SREG  ST1
      1    6     1  SREG  ST(1)
      2    6     1  SREG  ST2
      2    6     1  SREG  ST(2)
      3    6     1  SREG  ST3
      3    6     1  SREG  ST(3)
      4    6     1  SREG  ST4
      4    6     1  SREG  ST(4)
      5    6     1  SREG  ST5
      5    6     1  SREG  ST(5)
      6    6     1  SREG  ST6
      6    6     1  SREG  ST(6)
      7    6     1  SREG  ST7
      7    6     1  SREG  ST(7)

\ Floating Point Destination Registers
\     Reg  Type  W        Name

      0    6     1  DREG  ST,
      0    6     1  DREG  ST0,
      0    6     1  DREG  ST(0),
      1    6     1  DREG  ST1,
      1    6     1  DREG  ST(1),
      2    6     1  DREG  ST2,
      2    6     1  DREG  ST(2),
      3    6     1  DREG  ST3,
      3    6     1  DREG  ST(3),
      4    6     1  DREG  ST4,
      4    6     1  DREG  ST(4),
      5    6     1  DREG  ST5,
      5    6     1  DREG  ST(5),
      6    6     1  DREG  ST6,
      6    6     1  DREG  ST(6),
      7    6     1  DREG  ST7,
      7    6     1  DREG  ST(7),

: WORD-TYPE CREATE C, DOES> C@ <FW> ! ;

$007 WORD-TYPE INTEGER*2     $02F WORD-TYPE INTEGER*8
$003 WORD-TYPE INTEGER*4     $001 WORD-TYPE REAL*4
$005 WORD-TYPE REAL*8        $02B WORD-TYPE TEMP_REAL
$027 WORD-TYPE BCD

: MF    ( -- n )   <FW> @  [ FORTH ] 6 AND ;

: ESC,  ( n -- )   [ FORTH ] $D8 OR IC, ;

: N1FPF
        DUP 1+ C@ ESC, C@ OC, RESET ;

: N1FP   CREATE C, C, DOES> ['] N1FPF A;! A; ;

3 $0E2 N1FP FNCLEX    3 $0E3 N1FP FNINIT
3 $0E0 N1FP FNENI     3 $0E1 N1FP FNDISI
7 $0E0 N1FP FNSTWAX   ( 80287 instruction )

: W1FPF  $09B IC, N1FPF ;   \ Generate a WAIT before the instruction.

: W1FP   CREATE C, C, DOES> ['] W1FPF A;! A; ;

3 $0E2 W1FP FCLEX    3 $0E3 W1FP FINIT
3 $0E0 W1FP FENI     3 $0E1 W1FP FDISI
7 $0E0 W1FP FSTWAX   ( 80287 instruction )

: 1FP CREATE C, C, DOES> ['] N1FPF A;!  A; COMP-WAIT ;

\ NON-VARIANT 8087 INSTRUCTIONS 

6 $0D9 1FP FCOMPP    1 $0E4 1FP FTST      1 $0E5 1FP FXAM
1 $0EE 1FP FLDZ      1 $0E8 1FP FLD1      1 $0EB 1FP FLDPI
1 $0E9 1FP FLDL2T    1 $0EA 1FP FLDL2E    1 $0EC 1FP FLDLG2
1 $0ED 1FP FLDLN2    1 $0FA 1FP FSQRT,    1 $0FD 1FP FSCALE
1 $0F8 1FP FPREM     1 $0FC 1FP FRNDINT   1 $0F4 1FP FXTRACT
1 $0E1 1FP FABS,     1 $0E0 1FP FCHS      1 $0F2 1FP FPTAN
1 $0F3 1FP FPATAN    1 $0F0 1FP F2XM1     1 $0F1 1FP FYL2X
1 $0F9 1FP FYL2XP1   1 $0F7 1FP FINCSTP
1 $0F6 1FP FDECSTP   1 $0D0 1FP FNOP
( 3 $0E4 1FP FSETPM ) ( 80287 instruction )

: N2FPF
        DUP 1+ C@ ESC, C@ M/RS, RESET ;

: N2FP
        CREATE C, C, DOES>  ['] N2FPF A;! A; ;

1 $038 N2FP FNSTCW   5 $038 N2FP FNSTSW
1 $020 N2FP FNSTENV  5 $030 N2FP FNSAVE

: W2FPF $09B IC, N2FPF ;     \ Generate a WAIT before the instruction.

: W2FP  CREATE C, C, DOES>  ['] W2FPF A;! A; ;

1 $038 W2FP FSTCW    5 $038 W2FP FSTSW
1 $020 W2FP FSTENV

\ CR
\ .( ** WARNING: Re-defining the word FSAVE as a floating point instruction ** )

5 $030 W2FP FSAVE

: 2FP
        CREATE C, C,
        DOES>  ['] N2FPF A;! A; COMP-WAIT ;

1 $028 2FP FLDCW    1 $020 2FP FLDENV    5 $020 2FP FRSTOR

: 3FPF
        FPSTACK? [ FORTH ]
        IF     DUP 2+ C@ ESC, 1+ C@ RS@ OR OC,
        ELSE   MF 1 OR ESC, C@ <FW> @ 7 >
               IF       $010 AND <FW> @ $028 AND OR
                THEN
               M/RS,
        THEN RESET ;

: 3FP
        CREATE C, C, C,
        DOES>  ['] 3FPF A;! A; COMP-WAIT ;

01  $0C0  $000  3FP  FLD
05  $0D8  $018  3FP  FSTP

: 4FPF
        [ FORTH ] DUP 1+ C@ ESC, C@ RS@ OR OC, RESET ;

: 4FP
        CREATE C, C,
        DOES>  ['] 4FPF A;! A; COMP-WAIT ;

01  $0C8  4FP  FXCH
05  $0C0  4FP  FFREE

: 5FPF
        6 ESC, C@ RD@ [ FORTH ] OR OC, RESET ;

: 5FP
        CREATE C,  DOES>  ['] 5FPF A;! A; COMP-WAIT ;

$0C0  5FP  FADDP
$0C8  5FP  FMULP
$0E0  5FP  FSUBP
$0E8  5FP  FSUBRP
$0F0  5FP  FDIVP
$0F8  5FP  FDIVRP

: 6FPF
        FPSTACK?  [ FORTH ]
        IF      DUP C@ ESC, 1+ C@ RS@ OR OC,
        ELSE    DUP 1+ C@ 1 AND MF OR ESC,  C@ $038 AND M/RS,
        THEN RESET ;

: 6FP
        CREATE C, C,
        DOES>  ['] 6FPF A;! A; COMP-WAIT ;

$0D0  $000  6FP  FCOM
$0D8  $000  6FP  FCOMP
$0D1  $010  6FP  FST

: 7FPF
        [ FORTH ] FPSTACK?
        IF      RD@ 0=
                IF    0 ESC, C@ RS@ OR OC,
                ELSE  4 ESC, C@ RD@ OR OC,
                THEN
        ELSE    MF ESC, 1+ C@ M/RS,
        THEN RESET ;

: 7FP
        CREATE C, C,
        DOES>  ['] 7FPF A;! A; COMP-WAIT ;

$000  $0C0  7FP  FADD
$008  $0C8  7FP  FMUL
$020  $0E0  7FP  FSUB
$028  $0E8  7FP  FSUBR
$030  $0F0  7FP  FDIV
$038  $0F8  7FP  FDIVR

\ DECIMAL

: WSS: ( -- ) WAIT SS: NOWAIT ;

: WCS: ( -- ) WAIT CS: NOWAIT ;

: WDS: ( -- ) WAIT DS: NOWAIT ;

: WES: ( -- ) WAIT ES: NOWAIT ;

PREVIOUS PREVIOUS DEFINITIONS

CREATE CWTEMP 10 ALLOT

CODE >FREGS   ( addr -- )
        POP     BX
        WAIT
        FRSTOR  0 [BX]
        WAIT
        NEXT
        END-CODE

CODE >FREGS>   ( addr -- )
        POP     BX
        WAIT
        FSAVE   0 [BX]
        FRSTOR  0 [BX]
        WAIT
        NEXT
        END-CODE

: (TAG)   ( n1 addr -- n2 )          \ Returns the 2 bit tag for ST(n1)
        DUP>R 2+ @ $3800 AND $800 / +
        7 AND
        R> 4 + @ SWAP 0
        ?DO     2/ 2/
        LOOP
        3 AND ;

: {.}   ( n -- )
        SPACE ASCII { FEMIT (U.) TYPE ASCII } FEMIT SPACE ;

CODE INITFP    ( -- )
        FINIT
        WAIT
        FDISI
        WAIT
        NEXT
        END-CODE

CODE FPCW>    ( -- n )
        SUB     SP, # 2
        MOV     BX, SP
        FSTCW   0 [BX]
        WAIT
        NEXT
        END-CODE

CODE >FPCW    ( n -- )
        MOV     BX, SP
        FLDCW   0 [BX]
        ADD     SP, # 2
        WAIT
        NEXT
        END-CODE

CODE (F@)    ( F: -- r ; addr -- )   \ Load NPU from 10 byte temp_real area
        POP   BX
        FLD   TEMP_REAL 0 [BX]
        WAIT
        NEXT
        END-CODE

CODE FPSW>    ( -- n )
        SUB     SP, # 2
        MOV     BX, SP
        WAIT
        FSTSW   0 [BX]
        WAIT
        NEXT
        END-CODE

ALSO ASSEMBLER ALSO

LABEL (POWER)    ( F: log2x y -- x^y )  \ 2 ** (z*y) = x ** y
        FMULP ST(1), ST(0)      \ z * y  , where z = log2(x)
        FLD   ST(0)             \ DUP
        FSTCW CWTEMP        \ Save current Control Word
        MOV   AX, CWTEMP
        MOV   CX, AX
        AND   AX, # $0F3FF
        OR    AX, # $00400      \ Round toward neg. inf.
        MOV   CWTEMP AX
        FLDCW CWTEMP
        FRNDINT                 \ Take floor of z*y
        MOV   CWTEMP CX
        FLDCW CWTEMP        \ Restore Control word.
        FST   REAL*8 CWTEMP \ Save copy of floored value.
        FXCH  ST(1)
        FSUBP ST(1), ST(0)      \ (z*y) - floor(z*y) -> fract
        FLD1
        FCHS
        FXCH  ST(1)
        FSCALE                  \ fract/2
        FSTP  ST(1)             \  12/27/90 10:46:34.75 RLS
        F2XM1                   \ 2^(fract/2) - 1
        FLD1
        FADDP ST(1), ST(0)      \ 2^(fract/2)
        FLD   ST(0)             \ DUP
        FMULP ST(1), ST(0)      \ 2^fract
        FLD   REAL*8 CWTEMP
        FXCH  ST(1)
        FSCALE                  \ 2^(z*y)
        FSTP  ST(1)             \  12/27/90 10:46:59.47 RLS
        RET
        END-CODE

PREVIOUS PREVIOUS PREVIOUS

