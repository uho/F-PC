\ SFLOAT2 - Second Module of Floating Point Extensions
\
\               (c) Copyright 1988 by Robert L. Smith
\                       2300 St Francis Dr.
\                       Palo Alto, CA 94303
\                          (415)856-9321
\
\       Permission is granted to use this package or its derivatives for
\       private use.  For permission to use it in programs or packages
\       sold for profit, please contact the author.
\

DEFINED NOFLOATING NIP #IF NOFLOATING #THEN

ANEW FLOGSRC
\ LOADED,
PREFIX

: FCONSTANT   ( F: r -- )  \ When creating an FCONSTANT
              ( F: -- r )  \ When using a created FCONSTANT.
        CREATE  FPOP , , ,
        ;CODE
        POP     BX
        MOV     AX, 4 [BX]
        MOV     CX, 2 [BX]
        MOV     DX, 0 [BX]
        MOV     BX, FSP
        SUB     BX, # 6
        MOV     FSP BX
        MOV     4 [BX], AX
        MOV     2 [BX], CX
        MOV     0 [BX], DX
        NEXT
        END-CODE

: FVARIABLE   ( -- )       \ At creation time.
              ( -- addr )  \ At run time.
        VARIABLE  0 , 0 , ;

HEX
DAA2 C90F 4001 FPUSH FCONSTANT PI
17F8 B172 3FFF FPUSH FCONSTANT FLN2
         0 0 0 FPUSH FCONSTANT F0.0
   0 8000 4000 FPUSH FCONSTANT F1.0
D8A9 DE5B 3FFE FPUSH FCONSTANT FLOG10E          \ log base 10 of e = 1/ln(10)
8DDE 935D 4001 FPUSH FCONSTANT FLN10.0          \ log base e of 10
0000 A000 4003 FPUSH FCONSTANT F10.0
0000 8000 7FFF FPUSH FCONSTANT FINFINITY
0000 8000 3FFF FPUSH FCONSTANT F0.5

: F1.0+   ( F: r1 -- r2 )     F1.0 F+ ;

CODE F@   ( F: -- r ; addr -- )
        POP     BX
        MOV     AX, 4 [BX]
        MOV     CX, 2 [BX]
        MOV     DX, 0 [BX]
        MOV     BX, FSP
        SUB     BX, # 6
        MOV     FSP BX
        MOV     4 [BX], AX
        MOV     2 [BX], CX
        MOV     0 [BX], DX
        NEXT
        END-CODE

CODE F!   ( F: r -- ; addr -- )
        MOV     BX, FSP
        MOV     AX, 0 [BX]
        MOV     CX, 2 [BX]
        MOV     DX, 4 [BX]
        ADD     BX, # 6
        MOV     FSP BX
        POP     BX
        MOV     0 [BX], AX
        MOV     2 [BX], CX
        MOV     4 [BX], DX
        NEXT
        END-CODE

: F,   ( F: r -- )
        HERE F#BYTES ALLOT F! ;

: 1/F   ( F: r1 -- r2 )
        F1.0 FSWAP F/ ;

CODE FLOAT     ( F: -- r ; dbl -- )
        CLEAR_LABELS
        POP     AX
        POP     BX
        XOR     CX, CX
        XOR     DX, DX
        XOR     DI, DI
        OR      AH, AH
        JNS     2 $
        OR      CH, # 80
        NEG     AX
        NEG     BX
        SBB     AX, # 0
1 $:    SHR     AX
        RCR     BX
        RCR     DX
        RCR     DI
        INC     CX
2 $:    OR      BX, BX
        JNE     1 $
        OR      AX, AX
        JNE     1 $
        OR      CX, CX
        JZ      3 $
        ADD     CX, # 3FFF
3 $:    MOV     BX, FSP
        SUB     BX, # 6
        MOV     FSP BX
        MOV     4 [BX], DI
        MOV     2 [BX], DX
        MOV     0 [BX], CX
        NEXT
        END-CODE

: IFLOAT   ( F: -- r ; n -- )   S>D FLOAT ;

CODE DINTABS   ( F: r -- ; -- dbl flag )  \ For Positive arguments.
        CLEAR_LABELS                      \ Truncating.
        MOV     BX, FSP
        MOV     CX, 0 [BX]
        MOV     AX, 2 [BX]
        MOV     DI, 4 [BX]
        ADD     BX, # 6
        MOV     FSP BX
        MOV     BX, DI
        XOR     DI, DI          \ Make room for shifting in integer
        XOR     DX, DX
        OR      AX, AX
        JNS     1 $             \ Test for zero (unnormalized number)
        AND     CX, # 7FFF
        SUB     CX, # 3FFF
        JLE     1 $             \ Return zero if exponent is <= 0
        CMP     CX, # 10
        JGE     2 $
0 $:    SHL     AX, # 1         \ 0 < exponent < 16
        RCL     DI, # 1
        LOOP    0 $
1 $:    PUSH    DI
        PUSH    DX
        XOR     AX, AX          \ Send a flag of 0
        PUSH    AX
        NEXT

2 $:    CMP     CX, # 20
        JG      6 $
        JE      7 $
        SUB     CX, # 10        \ 16 <= exponent < 32.
        JZ      5 $             \ Jump if exactly 16.
4 $:    SHL     BX, # 1         \ Else shift left.
        RCL     AX, # 1
        RCL     DI, # 1
        LOOP    4 $
5 $:    PUSH    AX
        PUSH    DI
        XOR     AX, AX          \ Send a flag of zero.
        PUSH    AX
        NEXT

6 $:    OR      CH, # 80        \ Overflow case.  Set ms bit of flag.
        PUSH    DI
        PUSH    AX
        PUSH    CX
        NEXT

7 $:                            \ Result exactly fits 32 bits.
        MOV     CX, # 7FFF      \ Flag is non-zero with ms bit off
        PUSH    BX
        PUSH    AX
        PUSH    CX
        NEXT

        END-CODE

WARNING @ WARNING OFF

: FIX   ( F: r -- ; -- d )
        FDUP0<
        IF    FABS F0.5 F+ DINTABS >R DNEGATE R>
        ELSE  F0.5 F+ DINTABS
        THEN 0<
        IF     [ LAST @ NAME> ] LITERAL 2 FPERR
        THEN ;

: INT    ( F: r -- ; -- d )
        FDUP0<
        IF    DINTABS DUP 0<
              IF   [ LAST @ NAME> ] LITERAL 2 FPERR
              ELSE
                    IF   [ LAST @ NAME> ] LITERAL 2 FPERR
                    THEN
              THEN
              DNEGATE
        ELSE  DINTABS DUP
              IF    [ LAST @ NAME> ] LITERAL 2 FPERR
              ELSE  DROP
              THEN
        THEN ;

WARNING !

CODE TINTA   ( F: r -- ; -- lo mid hi )  \ Convert a floating number to
        CLEAR_LABELS                    \ triple integer.
        MOV     BX, FSP
        MOV     CX, 0 [BX]
        MOV     AX, 2 [BX]
        MOV     DX, 4 [BX]
        ADD     BX, # 6
        MOV     FSP BX
        XOR     BX, BX
        XOR     DI, DI
        AND     CX, # 7FFF      \ Knock off sign bit.
        SUB     CX, # 3FFF      \ Un-bias the exponent
        JLE     9 $
        CMP     CX, # 10
        JL      5 $
        CMP     CX, # 20
        JL      4 $
        CMP     CX, # 30
        JL      3 $
        JG      7 $
        MOV     DI, AX
        MOV     BX, DX
        XOR     AX, AX
        SUB     CX, # 30
0 $:    JE      2 $
1 $:    SHL     DX, # 1
        RCL     AX, # 1
        RCL     BX, # 1
        RCL     DI, # 1
        LOOP    1 $
2 $:    PUSH    AX
        PUSH    BX
        PUSH    DI
        NEXT
3 $:    MOV     BX, AX
        MOV     AX, DX
        XOR     DX, DX
        SUB     CX, # 20
        JMP     0 $
4 $:    SUB     CX, # 10
        JMP     0 $
5 $:    MOV     DX, AX
        XOR     AX, AX
        OR      CX, CX
        JMP     0 $
9 $:    XOR     AX, AX
        JMP     2 $

7 $:    OR      AH, AH          \ Test for unnormalized number (zero)
        JNS     9 $
        MOV     CX, # LAST @ NAME>
        PUSH    CX
        MOV     CX, # 2         \ Send overflow message
        PUSH    CX
        MOV     AX, # ' FPERR
        JMP     AX
        END-CODE

CODE FINT   ( F: r1 -- r2 )     \ Set true fractional bits to zero.
        CLEAR_LABELS
        MOV     BX, FSP         \ Get pointer to floating point stack.
        MOV     DX, 2 [BX]
        MOV     DI, # -1        \ Setup mask
        MOV     AX, DI
        OR      DH, DH
        JNS     4 $             \ Jump if unnormalized (zero).
        MOV     CX, 0 [BX]      \ Sign and exponent.
        AND     CX, # 7FFF      \ Knock off sign.
        SUB     CX, # 3FFF      \ Get unbiased exponent
        JLE     4 $             \ Return zero if exponent <= 0.
        CMP     CX, # 20        \ If exponent >= 20,
        JGE     3 $             \ just leave.
        CMP     CX, # 10
        JL      1 $
        XOR     AX, AX
        SUB     CX, # 10
        JZ      2 $
1 $:    SHR     AX, # 1         \ Shift in zeros from left.
        RCR     DI, # 1
        LOOP    1 $
2 $:    NOT     DI              \ Convert zeros to ones, and vice-versa.
        NOT     AX
        AND     4 [BX], DI
        AND     2 [BX], AX
3 $:    NEXT

4 $:    XOR     DX, DX
        MOV     0 [BX], DX
        MOV     2 [BX], DX
        MOV     4 [BX], DX
        NEXT
        END-CODE

ALSO HIDDEN DEFINITIONS

LABEL RENORM            \ Normalize the number in (CX,AX,BX,DX)
        CLEAR_LABELS
        OR      AH, AH          \ Check if shift by bytes or words.
        JZ      5 $             \ Branch if byte or word shift.
        JNS     4 $             \ Branch if not already in place.
1 $:    ADD     DX, # 8000      \ Begin rounding process.
        JZ      3 $             \ If result is zero, round to even.
        ADC     BX, # 0
        ADC     AX, # 0
        JNC     2 $             \ Branch if no carry out.
        RCR     AX, # 1         \ We generated a carry.  Shift it back.
        RCR     BX, # 1
        INC     CX              \ Modify the exponent to compensate.
2 $:    RET                     \ Return from subroutine.

3 $:    AND     BL, # 0FE       \ Round to even case.
        RET                     \ Return.

4 $:    DEC     CX              \ Normalize step.  Decrement Exponent.
        SHL     DX, # 1         \ Shift rest left by 1 bit.
        RCL     BX, # 1
        RCL     AX, # 1
        JNO     4 $             \ Loop if no overflow (sign bit not set).
        JMP     1 $             \ Finish by rounding.

5 $:    OR      AX, AX          \ Can we shift by words?
        JNZ     9 $             \ If not, jump.
        OR      BX, BX          \ Can we shift by two words?
        JNZ     7 $             \ If not, jump.
        OR      DX, DX          \ Is fraction zero?
        JNZ     6 $             \ If not, jump.
        XOR     CX, CX          \ Make everything zero,
        RET                     \ Then return.

6 $:    MOV     AX, DX          \ Shift by two words.
        SUB     CX, # 20        \ Decrement exponent by 32
        JMP     8 $             \ Join rest of code.

7 $:    MOV     AX, BX          \ Shift by one word.
        MOV     BX, DX
        SUB     CX, # 10        \ Subtract 16 from exponent.
8 $:    XOR     DX, DX          \ Clear least significant word.
        OR      AH, AH          \ Can we shift by a byte?
        JZ      9 $             \ Jump if we can shift by a byte.
        JNS     4 $             \ If sign is not set, jump to normalize.
        RET                     \ Otherwise, just return.

9 $:    MOV     AH, AL          \ Shift left by one byte.
        MOV     AL, BH
        MOV     BH, BL
        MOV     BL, DH
        MOV     DH, DL
        XOR     DL, DL
        SUB     CX, # 8         \ Subtract 8 from exponent.
        OR      AH, AH
        JNS     4 $             \ If sign bit not set, go to Normalize.
        RET                     \ Else just return.
        END-CODE

: F**+N     ( F: r1 -- r2 ; n -- )
        7FFF AND >R F1.0
        BEGIN R@ 1 AND
              IF   FOVER F*  THEN
              R> 2/ DUP
        WHILE >R FSWAP FDUP F* FSWAP
        REPEAT
        DROP FNIP ;

PREVIOUS DEFINITIONS

: F**N     ( F: r1 -- r2 ; n -- )  \ Floating number raised to integer power.
        DUP 0<
        IF    ABS [ ALSO HIDDEN ] F**+N F1.0 FSWAP F/
        ELSE  F**+N
        THEN ;
PREVIOUS

: F**N*    ( F: r1 r2 -- r3 ; n -- )  \ Raise r2 to nth power and
        [ ALSO HIDDEN ]
        DUP 0<                        \ and multiply by r1.
        IF      ABS F**+N F/
        ELSE    F**+N F*
        THEN ;
PREVIOUS

DECIMAL
CODE D2**N   ( n1 -- lo hi )
        CLEAR_LABELS
        POP     CX
        XOR     AX, AX
        XOR     BX, BX
        CMP     CX, # 16
        JAE     3 $
        INC     BX
        SHL     BX, CL
2 $:    PUSH    BX
        PUSH    AX
        NEXT
3 $:    CMP     CX, # 32
        JAE     2 $
        SUB     CX, # 16
        INC     AX
        SHL     AX, CL
        PUSH    BX
        PUSH    AX
        NEXT
        END-CODE

DECIMAL
HEX

\ Floating division with partial remainder.
\    The division routine that follows is based on the work of Roedy Green,
\    the author of BBL/Abundance.

CODE F/PREM     ( F: r1 r2 -- urrem urquot )  \ Results are unnormalized.
        CLEAR_LABELS
        MOV     BX, FSP         \ Floating Point Stack Pointer to BX
        MOV     DI, 2 [BX]      \ FDHi  High part of Denominator fraction
        OR      DI, DI          \ Check for unnormalized (zero) divisor.
        JNS     1 $             \ Branch to Divide by zero routine.
        PUSH    SI              \ Save SI, BP, and DS
        PUSH    BP
        PUSH    DS
        MOV     DX, 8 [BX]      \ FNHi  High part of Numerator
        OR      DH, DH          \ Check for unnormalized numerator.
        JNS     2 $             \ Branch to zero case, if unnormalized.
        MOV     CX, 6 [BX]      \ SXN   Sign + Exponent of Numerator
        MOV     AX, 0 [BX]      \ SXD   Sign + Exponent of Denominator
        MOV     SI, CX
        AND     SI, # 7FFF      \ XN
        CMP     SI, # 20
        JLE     2 $             \ Treat small numerator as zero.
        MOV     BP, AX
        AND     BP, # 7FFF      \ XD
        SUB     SI, BP          \ XN - XD
        JS      3 $             \ Jump if XN < XD
        CMP     SI, # 20
        JL      8 $             \ Jump if  0 <= (XN-XD) < 32
        SUB     CX, # 20        \ SXN - 32 -> SXR
        MOV     BP, AX          \ SXD
        XOR     BP, CX
        AND     BP, # 8000      \ SQ
        ADD     SI, # 3FFF      \ Make biased XQ
        JS      4 $             \ If sign is set, its an OVERFLOW condition.
        OR      SI, BP          \ SXQ
        MOV     AX, 0A [BX]     \ FN1  ( Initially the low part )
        CMP     DX, DI          \ Compare FNHi with FDHi
        JA      7 $
        JE      6 $
0 $:    MOV     0 [BX], SI      \ Save biased SXQ
        MOV     6 [BX], CX      \ SXR
        MOV     BP, 4 [BX]      \ FDLo  Low part of Denominator fraction
        XOR     BX, BX
        XOR     CX, CX
        JMP     1E $

1 $:    JMP     $1C $ WORD

2 $:    XOR     AX, AX          \ Set results to zero.
        POP     DS
        POP     BP
        POP     SI
        MOV     0A [BX], AX
        MOV     8 [BX], AX
        MOV     6 [BX], AX
        MOV     4 [BX], AX
        MOV     2 [BX], AX
        MOV     WORD 0 [BX], # 401F
        NEXT

3 $:                            \ Exponent of num < exponent of denominator.
        XOR     AX, AX          \ Set quotient to 0
\                               \ Remainder is Numerator
        MOV     4 [BX], AX      \ Quotient is 0
        MOV     2 [BX], AX
        MOV     WORD 0 [BX], # 401F     \ With quotient exponent of 401F
        POP     DS              \ Restore registers.
        POP     BP
        POP     SI
        NEXT

4 $:    JMP     $1A $ WORD
6 $:    CMP     AX, BX          \ FNHi = FDHi.  Compare FNLo with FDLo.
        JB      0 $
7 $:                            \ XN >= XD + 32, and FN >= FD
        INC     CX
        INC     SI              \ Overflow?
        JO      4 $
        MOV     0 [BX], SI      \ SXQ
        MOV     6 [BX], CX      \ SXR
        MOV     BP, 4 [BX]      \ FDLo
        XOR     BX, BX
        XOR     CX, CX
        SHR     DX, # 1
        RCR     AX, # 1
        RCR     BX, # 1
1E $:   JMP     0E $

8 $:                            \ 0 <= (XN - XD) < 32
        AND     CX, # 8000      \ SN
        OR      BP, CX          \ SN + XD -> SXR
        AND     AX, # 8000
        XOR     CX, AX          \ SQ
        OR      CX, # 401F      \ SXQ  The exponent is 32
        MOV     0 [BX], CX      \ SXQ
        MOV     6 [BX], BP      \ SXR
        MOV     BP, 4 [BX]      \ FDLo  Low part of Denominator fraction
        MOV     AX, 0A [BX]     \ FNLo  Low part of Numerator fraction
\ Numerator is in (DX, AX, BX, CX).  Denominator is in (DI, BP)
        MOV     CX, AX          \ Preliminary shift right by 32
        MOV     BX, DX
        XOR     AX, AX          \ Clear MS parts of numerator
        MOV     DX, AX
        CMP     SI, # 10        \ Test shift count
        JL      0B $
        MOV     AX, BX          \ Shift left by 16
        MOV     BX, CX
        XOR     CX, CX
        SUB     SI, # 10
        JZ      0E $
0B $:   CMP     SI, # 8
        JL      0C $
        MOV     DL, AH          \ Shift left by 8
        MOV     AH, AL
        MOV     AL, BH
        MOV     BH, BL
        MOV     BL, CH
        MOV     CH, CL
        XOR     CL, CL
        SUB     SI, # 8
        JZ      0E $
0C $:   OR      SI, SI          \ Test for zero exponent difference.
        JZ      0E $
        XCHG    CX, SI          \ Restore count and FN3
0D $:   SHL     SI, # 1         \ Shift left by specified count
        RCL     BX, # 1
        RCL     AX, # 1
        RCL     DX, # 1
        LOOP    0D $
        MOV     CX, SI
0E $:   MOV     DS, CX          \ Move FN3 out of the way for a while.
        CMP     DX, DI          \ See if trial divide would overflow
        JB      0F $            \ Jump if not.
        MOV     SI, # -1        \ Guess initial quotient is  FFFF
        MOV     CX, AX          \ FN1
        SUB     CX, BP          \ FN1 - FDLo
        MOV     BX, BP          \ FN2 + FDLo  ( FN2 = 0 )
        ADD     CX, DI          \ FDHi + (FN1 - FDLo)
        JC      11 $            \ Carry means result is OK
        JMP     10 $            \ No Carry means we have to modify remainder.

0F $:   DIV     DI              \ Initial approximation. Divide by FDHi
        MOV     SI, AX          \ Save initial quotient estimate = g0
        MOV     CX, DX          \ Save r0
        MUL     BP              \ Correction factor = g0 * FDLo
        SUB     BX, AX
        SBB     CX, DX          \ r1 = (s * r0) + FN2 - (g0 * FDLo)
        JNC     11 $            \ Jump if r1 >= 0   (no borrow)
10 $:   DEC     SI              \ Decrement g by 1, at least.
        ADD     BX, BP          \ r2 = r1 + FD
        ADC     CX, DI
        JC      11 $            \ Jump if r2 >= 0
        DEC     SI              \ Decrement g by 1 for last time.
        ADD     BX, BP
        ADC     CX, DI          \ r3 = r2 + den
11 $:   MOV     AX, BX
        MOV     DX, CX
        MOV     BX, DS          \ FN3
        MOV     DS, SI          \ MS part of Quotient
        CMP     DX, DI
        JAE     14 $
        DIV     DI              \ Approximate LS part of quotient.
        MOV     SI, AX
        MOV     CX, DX
        MUL     BP              \ Correction factor
        SUB     BX, AX
        SBB     CX, DX          \ r5 = s*r4 + FN3 - g1*FDLo
        JNC     13 $            \ Jump if no borrow
12 $:   DEC     SI              \ Decrement g1 by 1
        ADD     BX, BP          \ Add denominator back in to remainder
        ADC     CX, DI
        JC      13 $            \ Jump if no carry
        DEC     SI              \ Decrement g1 again
        ADD     BX, BP          \ Add denominator again
        ADC     CX, DI
13 $:
        MOV     BP, BX          \ Make room for FSP
        MOV     AX, DS
        POP     DS              \ Restore DS
        MOV     BX, FSP
        MOV     0A [BX], BP     \ LS part of Remainder
        MOV     8 [BX], CX      \ MS part of Remainder
        MOV     4 [BX], SI      \ LS part of Quotient
        MOV     2 [BX], AX      \ MS part of Quotient
        POP     BP              \ Restore BP
        POP     SI              \ Restore SI
        NEXT

14 $:   MOV     SI, # FFFF      \ MS num = MS den.  Start with trial g0 = s-1
        MOV     CX, AX
        SUB     CX, BP          \ midnum - lsden
        ADD     BX, BP
        ADD     CX, DI          \ msnum + (midnum-lsnum)
        JC      13 $            \ Good quotient if Carry is set
        JMP     12 $            \ Otherwise, correct result

1A $:                           \ Overflow case.  Let Rem = Num.
        MOV     AX, # -1
        OR      BP, # 7FFF      \ Set quotient to maximum, with SQ.
        MOV     DX, # 2         \ Indicate Overflow to error routine.
        POP     DS
        MOV     0A [BX], AX
        MOV     8 [BX], AX
        MOV     6 [BX], BP
        POP     BP
        POP     SI
1B $:   MOV     BX, # LAST @ NAME>
        PUSH    BX
        PUSH    DX
        MOV     AX, # ' FPERR
        JMP     AX

1C $:                           \ Divide by zero case.
        MOV     DI, # -1        \ Set exponent bits to 1.
        XOR     AX, AX          \ Clear rest of quotient.
\                               \ Set remainder to numerator!
        MOV     DX, # 1         \ Set indicator to Overflow.
        MOV     4 [BX], AX      \ Clear fractional part of quotient,
        MOV     2 [BX], AX
        MOV     0 [BX], DI      \ But set exponent bits to 1
        JMP     1B $            \ Jump to common error code.

        END-CODE

ALSO HIDDEN DEFINITIONS

CREATE LOGTAB1          \ Table of logarithms for argument > 1
        0000 , 0000 , 0000 ,   0FC1 , 4D87 , 3C1A ,   1F0A , 30C0 , 1163 ,
        2DE1 , A515 , CAD7 ,   3C4E , 0EDC , 55E6 ,   4A55 , 4BE0 , 7FD5 ,
        57FC , C1C2 , 9E4F ,   6549 , 6A73 , D15B ,   723F , DF1E , 6A69 ,
        7EE4 , 61B5 , 78F9 ,   8B3A , E55D , 5D30 ,   9747 , 15D7 , 88EA ,
        A30C , 5E10 , E2F6 ,   AE8D , EDFA , C04E ,   B9CE , BFB5 , DE80 ,
        C4D1 , 9C36 , 0A13 ,   CF99 , 1F65 , FCC2 ,   DA27 , BBDE , 647B ,
        E47F , BE3C , D4D1 ,

CREATE LOGTAB2          \ Table of logarithms for argument < 1
        0000 , 0000 , 0000 ,   2082 , BB13 , CE89 ,   4216 , 62D6 , 78E8 ,
        64CD , 7975 , 6526 ,   88BC , 7411 , 1F24 ,   ADFA , 035A , A1EE ,
        D49F , 69E4 , 56CF ,   FCC8 , E365 , 9D9C ,

LABEL FLN+          \ Fractional part treated as if 1.0 <= X < 1.5625
                    \ DI = Unbiased Exponent, DX = MS fract., AX = LS fract.
                    \ SI and BP have been pushed to stack.
        CLEAR_LABELS
        DEC     DI              \ Decrement the true exponent,
        PUSH    DI              \ Save it for the time being.
        MOV     DI, DX
        AND     DI, # FC00      \ Create a divisor of MS 6 bits of argument.
        MOV     CL, DH          \ Shift left by 6 = 8-2.  First by bytes.
        MOV     DH, DL
        MOV     DL, AH
        MOV     AH, AL
        XOR     AL, AL
        AND     CX, # 7F        \ Discard MS bit by masking.
        SHR     CX, # 1         \ Now shift right by 2 positions.
        RCR     DX, # 1
        RCR     AX, # 1
        SHR     CX, # 1
        RCR     DX, # 1
        RCR     AX, # 1
        PUSH    CX              \ Save Index value.
        OR      CX, CX          \ Delete these 5 instructions if you have
        JNZ     0 $             \ an NEC V-20 chip or equivalent.
        MOV     BX, DX
        JMP     10 $

0 $:    SHR     DX, # 1         \ Shift fractional part right by 1
        RCR     AX, # 1         \   so that division won't overflow.
        DIV     DI              \ Divide by MS 6 bits of original fraction.
        MOV     BX, AX          \ Save MS part of quotient in BX.
        XOR     AX, AX
        DIV     DI              \ Generate LS part of quotient.
        SHR     DI, # 1         \ Test for rounding.
        CMP     DX, DI
        CMC
        ADC     AX, # 0
        ADC     BX, # 0
10 $:   MOV     CX, AX          \ LS part of quotient to CX.
        MOV     DL, BH
        XOR     DH, DH
        SHL     DX, # 1
        SHL     DX, # 1         \ y*2^-6
        MOV     AX, # AAAB
        SUB     AX, DX          \ 2/3 - y*2^-6
        MUL     BX              \ *y
        SHR     DX, # 1
        SHR     DX, # 1
        SHR     DX, # 1
        SHR     DX, # 1
        SHR     DX, # 1         \ *(2^-5)
        ADC     DX, # 0         \ Round
        MOV     DI, DX          \ temp to DI
        MOV     AX, BX          \ y
        MUL     BX              \ y^2
        MOV     BP, DX          \ (yhi^2)hi
        MOV     SI, AX          \ (yhi^2)lo
        MOV     AX, # CCCD
        SUB     AX, DI          \ (4/5) - temp
        MUL     BP              \ * y^2
        MOV     DI, DX          \ temp2
        MOV     AX, BX
        MUL     CX              \ yhi*ylo
        SHL     AX, # 1         \ 2*yhi*ylo
        RCL     DX, # 1
        ADC     BP, # 0
        ADD     SI, DX          \ (y^2)lo
        ADC     BP, # 0         \ (y^2)hi
        SHR     DI, # 1
        SHR     DI, # 1
        SHR     DI, # 1
        SHR     DI, # 1
        SHR     DI, # 1         \ * (2^-5)
        ADC     DI, # 0
        MOV     AX, BX
        SUB     AX, DI          \ y - (y^2)*(2^-5)*(4/5) + stuff
        PUSH    CX              \ ylo
        PUSH    BX              \ yhi
        XOR     CX, CX
        MOV     CH, AL
        MOV     AL, AH
        XOR     AH, AH
        SHL     CX, # 1
        RCL     AX, # 1
        SHL     CX, # 1
        RCL     AX, # 1         \ *(2^-6) in Double precision
        MOV     BX, # AAAA
        MOV     DI, # AAAB
        SUB     DI, CX
        SBB     BX, AX          \ (2/3) - (2^-6)*(y - stuff) in (BX,DI).
        MOV     AX, BX
        MUL     BP              \ BP = y^2 hi
        MOV     CX, AX
        MOV     AX, DI
        MOV     DI, DX
        MUL     BP
        ADD     CX, DX
        ADC     DI, # 0
        MOV     AX, BX
        MUL     SI
        ADD     CX, DX
        ADC     DI, # 0         \ * y^2 dbl.  in (DI,CX)
        POP     BX              \ yhi
        POP     DX              \ ylo
        PUSH    SI
        PUSH    BP
        MOV     SI, DX          \ y in (BX,SI)
        MOV     AX, DI
        MUL     BX
        XCHG    AX, CX
        MOV     BP, DX
        MUL     BX
        ADD     CX, DX
        ADC     BP, # 0
        MOV     AX, SI
        MUL     DI
        ADD     CX, DX
        ADC     BP, # 0         \ * y
        SHR     BP, # 1
        RCR     CX, # 1
        SHR     BP, # 1
        RCR     CX, # 1
        SHR     BP, # 1
        RCR     CX, # 1
        SHR     BP, # 1
        RCR     CX, # 1
        SHR     BP, # 1
        RCR     CX, # 1         \ * (2^-5)  in (BP,CX)
        ADC     CX, # 0
        ADC     BP, # 0         \ Round.
        POP     AX              \ y^2 hi
        POP     DX              \ y^2 lo
        SUB     DX, CX
        SBB     AX, BP          \ y^2 - stuff  in (AX,DX)
        SHR     AX, # 1
        RCR     DX, # 1
        SHR     AX, # 1
        RCR     DX, # 1
        SHR     AX, # 1
        RCR     DX, # 1
        SHR     AX, # 1
        RCR     DX, # 1
        SHR     AX, # 1
        RCR     DX, # 1
        SHR     AX, # 1
        RCR     DX, # 1         \ * (2^-6)
        SUB     SI, DX
        SBB     BX, AX
        MOV     DX, BX          \ Result now in (DX,SI), unnormalized.
        XOR     AX, AX
        SHR     DX, # 1
        RCR     SI, # 1
        RCR     AX, # 1
        SHR     DX, # 1
        RCR     SI, # 1
        RCR     AX, # 1
        SHR     DX, # 1
        RCR     SI, # 1
        RCR     AX, # 1
        SHR     DX, # 1
        RCR     SI, # 1
        RCR     AX, # 1         \ * (2^-4)  Binary point 1 place to left.
        POP     BX              \ Get table index.
        SHL     BX, # 1
        MOV     DI, BX
        SHL     BX, # 1
        ADD     BX, DI          \ Index * 6
        ADD     BX, # LOGTAB1   \ Add base to offset
        ADD     AX, 4 [BX]      \ Add table value to logarithm
        ADC     SI, 2 [BX]
        ADC     DX, 0 [BX]
        XCHG    AX, DX          \ Rearrange registers for normalizing.
        MOV     BX, SI
        MOV     CX, # 3FFE      \ Put in an exponent (-1, biased)
1 $:    CALL    RENORM
        MOV     BP, BX
        MOV     BX, FSP
        MOV     4 [BX], BP      \ Push partial logarithm
        MOV     2 [BX], AX
        MOV     0 [BX], CX
        XCHG    BP, BX
        POP     DI              \ Unbiased exponent.
        MOV     CX, # 400F      \ Starting exponent for I*ln(2)
        OR      DI, DI
        JNS     2 $
        OR      CH, # 80
        NEG     DI
2 $:    MOV     AX, DI
        MOV     DX, # 17F8      \ Lo part of ln(2)
        MUL     DX
        MOV     BX, DX          \ BX = middle part
        XCHG    AX, DI          \ DI = lowest part
        MOV     DX, # B172      \ Hi part of ln(2)
        MUL     DX
        ADD     BX, AX          \ Final middle part
        ADC     DX, # 0         \ Possible carry to high part
        MOV     AX, DX          \ High part to AX
        MOV     DX, DI          \ Lo part to DX.  Index*ln(2) = (AX,BX,DX)
        CALL    RENORM
        XCHG    BX, BP
        SUB     BX, # 6
        MOV     FSP BX
        MOV     4 [BX], BP
        MOV     2 [BX], AX
        MOV     0 [BX], CX
        POP     BP              \ Restore BP
        POP     SI              \ Restore SI
        JMP     ' F+            \ Add to basic logarithm.
        END-CODE

PREVIOUS DEFINITIONS ALSO HIDDEN

CODE FLN   ( F: r1 -- r2 )   \ Natural logarithm function.   Time: 1260 usec.
        MOV     BX, FSP
        MOV     DI, 0 [BX]      \ Sign and biased exponent.
        MOV     DX, 2 [BX]      \ MS part of fraction.
        MOV     AX, 4 [BX]      \ LS part of fraction.
        OR      DI, DI
        JS      4 $
        OR      DX, DX
        JS      5 $
        MOV     CX, # 8         \ Zero argument for FLN
3 $:    MOV     DX, # LAST @ NAME>
        PUSH    DX
        PUSH    CX
        MOV     AX, # -1        \ Set result to largest negative number.
        MOV     4 [BX], AX
        MOV     2 [BX], AX
        MOV     0 [BX], AX
        MOV     AX, # ' FPERR
        JMP     AX

4 $:    MOV     CX, # 4         \ Negative argument for FLN
        JMP     3 $

5 $:    PUSH    SI              \ Save SI and BP
        PUSH    BP
        SUB     DI, # 3FFF      \ Unbiased exponent.
        CMP     DX, # C800      \ MS part of fraction.
        JAE     6 $
        JMP     FLN+            \ Jump if in range 1.0 to 1.5625

6 $:    PUSH    DI              \ Save true exponent.
        MOV     DI, DX
        NEG     DX
        NEG     AX
        SBB     DX, # 0         \ Negated argument  ( 1 - arg )
        XOR     CX, CX
        SHL     AX, # 1         \ Shift argument left by 5.
        RCL     DX, # 1
        RCL     CX, # 1
        SHL     AX, # 1
        RCL     DX, # 1
        RCL     CX, # 1
        SHL     AX, # 1
        RCL     DX, # 1
        RCL     CX, # 1
        SHL     AX, # 1
        RCL     DX, # 1
        RCL     CX, # 1
        SHL     DX, # 1         \ Yes - I do mean this instruction!
        RCL     CX, # 1         \ Finished shift.
        PUSH    CX              \ Save Index value.
        SHR     DX, # 1         \ Shift right by 1.
        MOV     BX, DX          \ This instruction and next may save a JMP !
        MOV     CX, AX
        AND     DI, # F800
        ADD     DI, # 0800
        JZ      7 $             \ Test for zero divisor (really 1.0).
        DIV     DI              \ Create y = (x/div)*(2^4)
        MOV     BX, AX          \ Save MS part of quotient in BX
        XOR     AX, AX
        DIV     DI
        MOV     CX, AX          \ LS part of quotient.
7 $:    MOV     AX, BX
        SHR     AX, # 1
        SHR     AX, # 1
        SHR     AX, # 1
        SHR     AX, # 1
        SHR     AX, # 1         \ y*(2^-5)
        ADD     AX, # AAAB      \ + (2/3)
        MUL     BX              \ * y
        SHR     DX, # 1
        SHR     DX, # 1
        SHR     DX, # 1
        SHR     DX, # 1         \ * (2^-4)
        ADC     DX, # 0         \ Round
        ADD     DX, # CCCD      \ + (4/5)
        MOV     DI, DX          \ temp to DI
        MOV     AX, BX          \ y
        MUL     BX              \ yhi^2
        MOV     BP, DX          \ (yhi^2)hi
        MOV     SI, AX          \ (yhi^2)lo
        MOV     AX, DX
        MUL     DI              \ temp * y^2
        SHL     AX, # 1
        ADC     DX, # 0         \ Round DX
        MOV     DI, DX          \ temp2
        MOV     AX, BX
        MUL     CX              \ yhi*ylo
        SHL     AX, # 1         \ 2*yhi*ylo
        RCL     DX, # 1
        ADC     BP, # 0
        ADD     SI, DX          \ (y^2)lo
        ADC     BP, # 0         \ (y^2)hi
        XOR     DX, DX
        SHR     DI, # 1
        RCR     DX, # 1
        SHR     DI, # 1
        RCR     DX, # 1
        SHR     DI, # 1
        RCR     DX, # 1
        SHR     DI, # 1
        RCR     DX, # 1         \ * (2^-4) double
        ADD     DX, CX
        ADC     DI, BX          \ + ydbl
        SHR     DI, # 1
        RCR     DX, # 1
        SHR     DI, # 1
        RCR     DX, # 1
        SHR     DI, # 1
        RCR     DX, # 1
        SHR     DI, # 1
        RCR     DX, # 1
        SHR     DI, # 1
        RCR     DX, # 1         \ * (2^-5) double
        ADD     DX, # AAAB      \           templo
        ADC     DI, # AAAA      \ + (2/3)   temphi
        PUSH    SI
        MOV     AX, BX          \ yhi = A
        MUL     DX              \ A*D
        MOV     SI, DX          \ ADhi
        MOV     AX, CX          \ B
        MUL     DI              \ B*C
        ADD     SI, DX          \ ADhi + BChi , no carry out.
        MOV     AX, BX          \ A
        MUL     DI              \ A*C
        ADD     SI, AX          \ AClo + ADhi + BChi
        ADC     DX, # 0         \ AChi + cy
        MOV     DI, DX          \ new temphi = new C
        MOV     AX, SI          \ new D
        MUL     BP              \ A*D
        POP     SI              \ y^2lo = B
        PUSH    CX              \ ylo
        MOV     CX, DX          \ ADhi
        MOV     AX, DI          \ C
        MUL     SI              \ B*C
        ADD     CX, DX          \ ADhi + BChi , no carry out.
        MOV     AX, BP          \ A
        MUL     DI              \ A*C
        ADD     CX, AX          \ AClo + ADhi + BChi
        ADC     DX, # 0         \ AChi
        SHR     DX, # 1
        RCR     CX, # 1
        SHR     DX, # 1
        RCR     CX, # 1
        SHR     DX, # 1
        RCR     CX, # 1
        SHR     DX, # 1
        RCR     CX, # 1         \ * (2^-4)
        ADC     CX, # 0
        ADC     DX, # 0         \ Round
        ADD     SI, CX
        ADC     BP, DX          \ + y^2
        XOR     DX, DX
        SHR     BP, # 1
        RCR     SI, # 1
        RCR     DX, # 1
        SHR     BP, # 1
        RCR     SI, # 1
        RCR     DX, # 1
        SHR     BP, # 1
        RCR     SI, # 1
        RCR     DX, # 1
        SHR     BP, # 1
        RCR     SI, # 1
        RCR     DX, # 1
        SHR     BP, # 1
        RCR     SI, # 1
        RCR     DX, # 1         \ * (2^-5)
        POP     CX              \ ylo
        ADD     CX, SI
        ADC     BX, BP          \ Result in (BX,CX)
        SHR     BX, # 1
        RCR     CX, # 1
        RCR     DX, # 1
        SHR     BX, # 1
        RCR     CX, # 1
        RCR     DX, # 1         \ * (2^-2)
        MOV     AX, BX
        POP     BX              \ Index
        SHL     BX
        MOV     DI, BX
        SHL     DI
        ADD     BX, DI
        JZ      8 $
        ADD     BX, # LOGTAB2
        ADD     DX, 4 [BX]
        ADC     CX, 2 [BX]
        ADC     AX, 0 [BX]      \ Add in logarithm from table
8 $:    MOV     BX, CX
        MOV     CX, # BFFD      \ -1*(2^-2) to biased exponent.
        JMP     1 $             \ Jump to common code
        END-CODE

\ End of Logarithm Function.

PREVIOUS DEFINITIONS

: FLOG     ( F: r1 -- r2 )
        FLN FLOG10E F* ;
HEX

CODE F/LN2     ( F: r -- r/ln2 )    \ Divide by natural log of 2
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     DX, 2 [BX]
        OR      DH, DH
        JNS     4 $             \ Check for zero.
        MOV     CX, 0 [BX]
        MOV     DI, 4 [BX]
        MOV     BX, DX
        PUSH    SI
        PUSH    BP
        INC     CX
        MOV     AX, DI          \ flo = B
        MOV     DX, # 0B8AA     \ (1/ln2 )hi = C
        MUL     DX
        MOV     SI, DX          \ BCh
        MOV     BP, AX          \ BCl
        MOV     AX, BX          \ fhi = A
        MOV     DX, # 03B29     \ (1/ln2)lo = D
        MUL     DX
        ADD     BP, AX          \ ADl + BCl
        ADC     SI, DX          \ ADh + BCh
        MOV     AX, DI          \ B
        MOV     DX, # 03B29     \ D
        MUL     DX
        ADD     BP, DX          \ BDh + ADl + BCl
        ADC     SI, # 0         \ ADh + BCh + cy
        MOV     AX, BX          \ A
        MOV     DX, # 0B8AA     \ C
        MUL     DX
        ADD     AX, SI          \ Prod low
        ADC     DX, # 0         \ Prod high
        JS      1 $             \ Br if ms bit set
        SHL     BP, # 1         \ Else normalize
        RCL     AX, # 1
        RCL     DX, # 1
        DEC     CX
1 $:    ADD     BP, # 08000     \ Round
        ADC     AX, # 0
        ADC     DX, # 0
        JC      3 $
2 $:    POP     BP              \ Restore
        POP     SI
        MOV     BX, FSP
        MOV     4 [BX], AX      \ Push results
        MOV     2 [BX], DX
        MOV     0 [BX], CX
        NEXT

3 $:    RCR     DX, # 1         \ Rare case of renorm on rounding
        RCR     AX, # 1
        INC     CX
        JMP     2 $

4 $:    XOR     AX, AX          \ Zero argument case.
        MOV     4 [BX], AX
        MOV     2 [BX], AX
        MOV     0 [BX], AX
        NEXT
        END-CODE

\ End of F/LN2

