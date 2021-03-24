\ FLTAUX.SEQ    Auxilliary floating point functions for CODEBUG or FLTBUG

NEEDS FASSEM.SEQ        \ Requires the floating point assembler

CODE (WAIT)
        WAIT
        NEXT
        END-CODE

CREATE FR1 94 ALLOT
FR1 14 + CONSTANT NPSTK
CREATE FLT-WORK 10 ALLOT
CREATE FLT-BCD 10 ALLOT
VARIABLE (PLACES)
VARIABLE (#BCD)   17 (#BCD) !
CREATE [F2.0]     0 , 0 , 0 , $4000 ,

: NPU-DEPTH   ( -- n )
        FR1 >FREGS>
        0 0 7
        DO      I FR1 (TAG) 3 <>
                IF      DROP I 1+ LEAVE
                THEN
        -1 +LOOP ;

CODE (FDROP)   ( F: r -- )      \ No monitoring of virtual stack.
        FSTP  REAL*8 ST(0)      \ drop from NPU
        NEXT
        END-CODE

CODE (FDUP)    ( F: r -- r r )          \ No monitoring of virtual stack
        FLD   ST
        NEXT
        END-CODE

CODE (FABS)    ( F: r1 -- |r1|)         \ No virtual stack monitoring
        FABS,
        NEXT
        END-CODE

CODE (FLOG)    ( F: r1 -- LOG10[r1])    \ No virtual stack check
        FLDLG2
        FXCH   ST(1)
        FYL2X
        NEXT
        END-CODE

CODE (FALOG)    ( F: r -- 10^r )
        FLDL2T
        CALL (POWER)
        NEXT
        END-CODE

CODE (FLOAT)    ( F: -- r ; d -- )
        MOV     BX, SP
        MOV     AX, 0 [BX]
        MOV     CX, 2 [BX]
        MOV     2 [BX], AX
        MOV     0 [BX], CX
        FLD     INTEGER*4 0 [BX]
        ADD     SP, # 4
        WAIT
        NEXT
        END-CODE

CODE (F!)   ( F: r -- ; addr -- )
        POP   BX
        FSTP  REAL*8 0 [BX]
        WAIT
        NEXT
        END-CODE

CODE (F/)    ( F: r1 r2 -- r1/r2)
        FDIVRP  ST(1), ST(0)
        NEXT
        END-CODE

CODE (FRNDFRC)    ( F: +r1 -- +r2 )
        FLD    INTEGER*2 (PLACES)       \ 2
        FCHS
        FLDL2T                          \ 3
        CALL    (POWER)                 \ 2
        FLD     REAL*8 [F2.0]           \ 3
        FDIVRP  ST(1), ST(0)            \ 2
        FADDP   ST(1), ST(0)            \ 1
        NEXT
        END-CODE

CODE (R>BCD!)    ( F: r -- ; n -- ; full precision bcd-string to FLT-BCD )
       MOV      AX, (#BCD)
       POP      CX
       SUB      AX, CX
       DEC      AX WORD
       PUSH     AX
       MOV      BX, SP
       FLD      INTEGER*2 0 [BX]
       ADD      SP, # 2
       FLDL2T
       CALL     (POWER)
       FMULP    ST(1), ST(0)
       FSTP     FLT-BCD BCD
       WAIT
       NEXT
       END-CODE

: (E.R0)    ( #dec   #col -- )
        OVER - 5 - SPACES ASCII . FEMIT 0
        DO
                ASCII 0 FEMIT
        LOOP
        ." E+00" ;

: (F.SPECIAL)   ( F: r -- ; cc n -- )  \ Display special f-p numbers.
        SWAP DUP $0100 AND 0=
        IF   (FDROP) DROP SPACES EXIT  THEN
        DUP $4000 >
        IF   DROP " EMPTY"
        ELSE DUP $0200 AND  IF  ." -"  ELSE  ." +"  THEN
             $0400 >
             IF    (FDROP) " INFINITY"
             ELSE  FLT-WORK (F!)  FLT-WORK 2@ D0= >R
                   FLT-WORK 4 + 2@ SWAP
                   $7FFF AND 0 $7FF8 D= R> AND
                   IF   " INDEFINITE"  ELSE   " NAN"   THEN
             THEN
        THEN
        ROT $.R ;

CODE (FEXAM)    ( F: r -- r ; -- n )    \ This version assumes fpstack is ok.
        NOP
        FXAM
        SUB     SP, # 2
        MOV     BX, SP
        FSTSW   0 [BX]
        WAIT
        AND     0 [BX], # $4700 WORD
        NEXT
        END-CODE

CODE (FP>DI)    ( F: r -- ; -- 32b )
        SUB     SP, # 4
        MOV     BX, SP
        FRNDINT
        FSTP    INTEGER*4 0 [BX]
        WAIT
        NEXT
        END-CODE

: (C3C0X)    ( fpsw -- n )
        DUP  $04000 AND
        IF   2
        ELSE 0
        THEN
        SWAP $00100 AND
        IF   1+
        THEN ;

CODE (RVS0)     ( F: r -- ; -- fpsw )
        FTST
        SUB     SP, # 2
        MOV     BX, SP
        FSTSW   0 [BX]
        FSTP    ST(0)
        WAIT
        NEXT
        END-CODE

CODE (RVSR)    ( F: r1 r2 -- ; -- fpsw )
        FXCH    ST(1)
        FCOMPP
        SUB     SP, # 2
        MOV     BX, SP
        FSTSW   0 [BX]
        WAIT
        NEXT
        END-CODE

: (F0<)  ( F: r -- ; -- f)  (RVS0) (C3C0X) 1 = ;

: (F<)    ( F: r1 r2 -- ; -- f )
        (RVSR) (C3C0X) 1 = ;

: (RND>-INF)  ( F: r -- ; -- d )
        $0400
        FPCW> DUP >R  $F3FF AND OR >FPCW
        (FP>DI) SWAP R> >FPCW ;

: (FMAG)   ( F: r -- r ; -- n )
        (FDUP) (FABS) (FLOG) (RND>-INF) DROP ;

: (.DIGITS)    ( last first -- )
        2DUP > ABORT"  FP I/O error. "
        DO I 1- 2/ FLT-BCD + C@ 16 /MOD I 2 MOD
             IF   DROP  ELSE   NIP  THEN
             ASCII 0 + FEMIT
         -1 +LOOP ;

: (FULL2)    ( n -- )
        0 <# # # #> TYPE ;

: (E.R#)    ( F: r -- ; #dec -- )
        (PLACES) ! (FDUP) (F0<)
        IF      ASCII -
        ELSE    BL
        THEN
        FEMIT ASCII . FEMIT
        (FABS) (FMAG) DUP >R
        1+ S>D (FLOAT) (FALOG) (F/) (FMAG) >R (FRNDFRC) (FMAG) DUP R> - >R
        (R>BCD!) (PLACES) @ 17 DUP ROT - 1+ SWAP (.DIGITS)
        ASCII E FEMIT R> R> + 1+
        DUP 0<
        IF      ASCII -
        ELSE    ASCII +
        THEN
        FEMIT ABS DUP 100 <
        IF      (FULL2)
        ELSE    .
        THEN ;

: (E.R)    ( F: r -- ; #dec #col -- )
        (FEXAM)  DUP $0100 AND
        IF   -ROT NIP (F.SPECIAL) EXIT
        THEN
        $4500 AND $4000 =                        \ Test for zero
        IF      (FDROP) (E.R0) EXIT
        THEN
        (FDUP) (FABS) (FLOG)
        (FDUP) (FABS) 100. (FLOAT) (F<) 0=       \ Test exponent
        IF      7 - 0 MAX SPACES DROP
                (F0<) (FDROP)
                IF      ."  SMALL "
                ELSE    ."  LARGE "
                THEN
                EXIT
        THEN
        (FDROP)
        2DUP SWAP - 6 - 0<                      \ get # characters
        IF      0                               \ too big, print *'s
                DO      ASCII * FEMIT
                LOOP
                DROP (FDROP)
        ELSE
                OVER - 6 - 0 MAX SPACES (E.R#)  \ ok, print it
        THEN ;

VARIABLE NDEPTH
77 CONSTANT FPDISPSIZE
VARIABLE DECIMAL_STACK
TRUE DECIMAL_STACK !
VARIABLE SIGDIGITS
3 SIGDIGITS !           \ Default Number of significant digits

: .NPUSTK   ( -- )               \ Needs lots of work yet.
        NPU-DEPTH DUP NDEPTH ! {.} INITFP
        SIGDIGITS @ 2 UMAX 19 UMIN DUP 7 +
        FPDISPSIZE #OUT @ - OVER / 0 MAX NDEPTH @ UMIN NDEPTH @ 0<>
        IF      1 MAX
        THEN
        NEGATE 0 SWAP
        ?DO     NPSTK I 1+ 10 * - (F@)
                2DUP (E.R)
        LOOP
        2DROP
        FR1 >FREGS
        FPDISPSIZE 2 + #OUT @ - 0 MAX SPACES ;

: .NPUHEX  ( addr -- )
        $10 SAVE!> BASE 0 9
        DO
                DUP I + C@ 0 <# # # #> TYPE
        -1 +LOOP
        DROP SPACE
        RESTORE> BASE ;

: H.NPUSTK   ( -- )
        NPU-DEPTH DUP NDEPTH ! {.}
        FPDISPSIZE 21 / NDEPTH @ UMIN 0
        ?DO     NDEPTH @ I - 1- 10 * NPSTK +
                .NPUHEX
        LOOP
        FR1 >FREGS
        FPDISPSIZE #OUT @ - 0MAX SPACES ;

