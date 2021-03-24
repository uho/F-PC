\ Software Floating Point Package for FORTH  -- Forward, sequential version.
\ SFLOAT1.SEQ  The first of three files for SFLOAT
CR .( Copyright 1989 by R. L. Smith ) \ Software Floating Point Package for FORTH  -- Forward, sequential version.
CR .( Version 2.24   RLS      11/22/93 07:46 )
CR
\
\          (c) Copyright 1989  by Robert L. Smith
\                  2300 St. Francis Dr.
\                  Palo Alto, CA 94303
\
\  Permission to use this package or its derivatives is granted for private
\  use with Forth systems.  For permission to use in programs or packages
\  sold for profit, or use with other languages, please contact the author.
\
DEFINED NOFLOATING NIP #IF NOFLOATING #THEN

ANEW FPPACKAGE
PREFIX
\ LOADED,
DECIMAL
6 CONSTANT F#BYTES              \ Number of bytes for a floating point number
CREATE FPSTAT 0 , 0 ,           \ Holds error information

ALSO HIDDEN DEFINITIONS
40 F#BYTES * CONSTANT FPSSIZE   \ Size of floating point stack
CREATE FPSTACK   FPSSIZE ALLOT  \ Floating Point Stack is separate
PREVIOUS DEFINITIONS

HERE 12 ALLOT                   \ Leave a little room for underflow
CONSTANT FSP0                   \ Point to base of stack.
CREATE FSP   FSP0 ,             \ Points to top of stack

HEX

: FDEPTHB   ( -- n )             \ Depth of floating point stack in bytes.
        FSP0 FSP @ - ;

: FDEPTH   ( -- n )             \ Depth of floating point stack in FP words.
        FDEPTHB 6 / ;

: FCLEAR   ( -- )                 \ Clear Floating Point Stack.
        FSP0 FSP ! ;

CODE AND!   ( n addr -- )       \ Logical AND of contents at addr with n
        POP     BX
        POP     AX
        AND     0 [BX], AX
        NEXT
        END-CODE

CODE OR!   ( n addr -- )        \ Logical OR of contents at addr with n
        POP     BX
        POP     AX
        OR      0 [BX], AX
        NEXT
        END-CODE

CODE FPOP   ( F: r -- ; -- d n )   \ Pop floating number from f-stack to
        MOV     BX, FSP            \ parameter stack.
        PUSH    4 [BX]
        PUSH    2 [BX]
        PUSH    0 [BX]
        ADD     BX, # 6
        MOV     FSP BX
        NEXT
        END-CODE

CODE FPUSH   ( F: -- r ; d n -- )    \ Push floating number from parameter
        MOV     BX, FSP              \ stack to floating stack.
        SUB     BX, # 6
        MOV     FSP BX
        POP     0 [BX]
        POP     2 [BX]
        POP     4 [BX]
        NEXT
        END-CODE

CODE FPCOPY  ( F r -- r ; -- d n )      \ Get a copy of the floating number
        MOV     BX, FSP                 \ at top of floating stack.
        PUSH    4 [BX]
        PUSH    2 [BX]
        PUSH    0 [BX]
        NEXT
        END-CODE

CODE FPFRACT  ( F: r -- r ; -- d )
        MOV     BX, FSP
        PUSH    4 [BX]
        PUSH    2 [BX]
        NEXT
        END-CODE

CODE FPSEXP  ( F: r -- r ; -- n )
        MOV     BX, FSP
        PUSH    0 [BX]
        NEXT
        END-CODE

: 2/?   ( n1 -- n2 n3 )         \ n2 is n1 shifted right by 1.
                                \ n3 is least significant bit of n1 .
        DUP >R 2/ 7FFF AND R> 1 AND ;

: .NAME     ( n1 -- )
        >NAME .ID ;

DEFER FPERR

ALSO HIDDEN DEFINITIONS

: .FP.    ( -- )   ." Floating Point " ;

: (FPERR)    ( F: r -- r ; n1 n2 --  )       \ n1 is CFA, n2 is error flag.
        DUP FPSTAT OR!  CR  BELL EMIT
 ( 1 )  2/? IF  DROP .FP. ." Division by zero in "          .NAME EXIT THEN
 ( 2 )  2/? IF  DROP .FP. ." Overflow in "                  .NAME EXIT THEN
 ( 4 )  2/? IF  DROP .FP. ." argument is negative for "     .NAME EXIT THEN
 ( 8 )  2/? IF  DROP .FP. ." argument is zero for "         .NAME EXIT THEN
( 10 )  2/? IF  DROP .FP. ." argument is out of range for " .NAME EXIT THEN
( 20 )  2/? IF  DROP .FP. ." Overflow for Input in "        .NAME EXIT THEN
( 40 )  2/? IF  DROP .FP. ." Overflow for Output in "       .NAME EXIT THEN
( 80 )  2/? IF  DROP ." Integer overflow for "              .NAME EXIT THEN
( 100)  2/? IF  DROP .FP. ." Underflow in "                 .NAME EXIT THEN
( 200)  2/? IF  DROP .FP. ." argument inaccurate for "      .NAME EXIT THEN
( 400)  2/? IF  DROP .FP. ." Underflow for Input in "       .NAME EXIT THEN
( 800)  2/? IF  DROP .FP. ." Underflow for Ouput in "       .NAME EXIT THEN
( 1000) 2/? IF  DROP .FP. ." results inaccurate for "       .NAME EXIT THEN
        IF ." Unspecified Error " THEN
        DROP QUIT ;

' (FPERR) IS FPERR

LABEL DENORM    \ CX = count, BX = Hi, DX = LO, AX = Guard, Round, & Sticky.
        CLEAR_LABELS
        XOR     AX, AX          \ Clear GRS bits
        CMP     CX, # 10        \ Check size of shift
        JL      7 $             \ Branch if shift less than 16
        CMP     CX, # 18        \ Cnt >= 16.  Compare with 24.
        JGE     1 $             \ Branch if shift >= 24
        SUB     CX, # 10        \ 16 <= cnt < 24.  Subtract 16 from cnt.
        MOV     AX, DX          \ Shift by one word.
        MOV     DX, BX
        XOR     BX, BX          \ Clear MS word.
        AND     AL, AL          \ Don't miss any sticky bits.
        JZ      8 $
        OR      AH, # 2         \ Set a sticky bit.
        JMP     8 $             \ Shift from 0 to 7 bits.

1 $:    CMP     CX, # 20        \ Cnt >= 24.  Compare with 32.
        JGE     3 $             \ Branch if shift >= 32.
        MOV     AH, BL          \ 24 <= cnt < 32, so shift by 3 bytes.
        MOV     AL, DH
        OR      AL, DL          \ OR in the sticky bits.
        JZ      2 $             \ Shall we set a sticky bit?
        OR      AH, # 2         \ Yes.
2 $:    MOV     DL, BH          \ Continue the 3-byte shift.
        XOR     DH, DH          \ Clear the high 3 bytes.
        XOR     BX, BX
        SUB     CX, # 18        \ Adjust the shift counter.
        JMP     8 $             \ Shift remaining 0 to 7 places.

3 $:    CMP     CX, # 28        \ Cnt >= 32.  Check against 40.
        JGE     5 $             \ Branch if shift count > 40.
        MOV     AX, BX          \ 32 <= cnt < 40.  Do a 2 word shift.
        OR      AL, DH          \ Check the sticky bits.
        OR      AL, DL
        JZ      4 $             \ If sticky byte not zero,
        OR      AH, # 2         \  then set sticky bit.
4 $:    XOR     BX, BX          \ Clear high and low words.
        XOR     DX, DX
        SUB     CX, # 20        \ Adjust shift counter.
        JMP     8 $             \ Go to final shift area.

5 $:    OR      BX, DX          \ Shift count >= 40
        JZ      0A $            \ In theory, this should never jump.
        MOV     AH, # 2         \ So set a sticky bit.
        XOR     BX, BX          \ Clear all the rest.
        XOR     DX, DX
        RET

7 $:    CMP     CX, # 8         \ Count < 16.  See if less than 8.
        JL      8 $
        MOV     AH, DL          \ 8 <= cnt < 16.
        MOV     DL, DH          \ Move by one byte.
        MOV     DH, BL
        MOV     BL, BH
        XOR     BH, BH          \ Clear high byte.
        SUB     CX, # 8         \ Adjust shift count.
8 $:    AND     CX, CX          \ Test for zero count.
        JZ      0A $
9 $:    SHR     BX, # 1         \ Shift right by one bit.
        RCR     DX, # 1
        RCR     AX, # 1
        LOOP    9 $             \ Loop until count is 0.
0A $:   RET

        END-CODE

PREVIOUS DEFINITIONS
\ F+ and F-

HEX

CODE F-     ( F: r1 r2 -- r3 )
        CLEAR_LABELS
        PUSH    BP
        MOV     BP, FSP
        XOR     WORD 0 [BP], # 8000  \ For subtraction, reverse sign and add.
        JMP     1 $
        END-CODE

CODE F\-   ( F: r1 r2 -- r3 )     \ Reverse subtraction
        PUSH    BP
        MOV     BP, FSP
        XOR     WORD 6 [BP], # 8000
        JMP     1 $
        END-CODE

ALSO HIDDEN

CODE F+     ( F: r1 r2 -- r3 )    \ Timing: 174 usec.
        PUSH    BP
1 $:    MOV     BP, FSP
        MOV     AX, 0 [BP]      \ Get SX2  ( Sign and biased exponent )
        MOV     BX, 2 [BP]      \ Hi2
        MOV     DX, 4 [BP]      \ Lo2
        ADD     BP, # 6
        MOV     FSP BP          \ Update Floating Stack Pointer
        OR      BH, BH          \ Check if normalized number.
        JNS     3 $             \ Branch if m.s. bit of Hi2 not set.
        MOV     CX, 0 [BP]      \ SX1.
        MOV     DI, 2 [BP]      \ Hi1
        OR      DI, DI          \ Check if Hi1 is normal.
        JNS     4 $             \ Jump if m.s. bit not set.
        CMP     AX, CX          \ Check if signs and exponents the same.
        JNE     5 $             \ Branch if not the same.
        AND     AX, # 7FFF
        CMP     AX, # 7FFE
        JGE     0F $            \ Overflow check.
        MOV     AX, 4 [BP]      \ Equal exponents: Add magnitudes.
        ADD     AX, DX          \ Lo1 + Lo2
        ADC     BX, DI          \ Hi1 + Hi2
        RCR     BX              \ Carry must have been generated.
        RCR     AX
        JNC     2 $             \ Check guard bit.
        ADD     AX, # 1         \ Round result.
        ADC     BX, # 0         \ No carry-out
        AND     AX, # FFFE      \ Round to even.
2 $:    INC     CX              \ Add to exponent.  Could check for overflow.
        MOV     4 [BP], AX      \ Push results
        MOV     2 [BP], BX
        MOV     0 [BP], CX
3 $:    POP     BP
        NEXT

4 $:                            \ Treat 2nd operand as zero.
        MOV     4 [BP], DX      \ Push 1st operand.
        MOV     2 [BP], BX
        MOV     0 [BP], AX
        POP     BP
        NEXT

0F $:   MOV     CX, # 2         \ Report an overflow
0E $:   MOV     BX, # LAST @ NAME>
        POP     BP
        PUSH    BX
        PUSH    CX
        MOV     AX, # ' FPERR
        JMP     AX

5 $:                            \ Unequal exponents.
        PUSH    SI              \ Save SI
        MOV     SI, 4 [BP]      \ Get Lo1.
        OR      AH, AH          \ Test for negative sign of F2.
        JS      0B $            \ If F2 negative, branch to neg F2 case.
        OR      CH, CH          \ Test for negative F1.
        JS      0C $            \ Branch if F1 is negative.
6 $:    CMP     AX, CX          \ Add magnitudes.  Find smaller exponent.
        JB      7 $             \ Branch if F2 is smaller.
        XCHG    AX, CX          \ Swap F1 and F2.
        XCHG    BX, DI
        XCHG    DX, SI
7 $:    PUSH    CX              \ Save CX
        SUB     CX, AX          \ Set CX to exponent difference.
        CALL    DENORM          \ Denormalize the smaller number.
        POP     CX              \ Restore CX
        ADD     DX, SI          \ Add low parts.
        ADC     BX, DI          \ Add high parts.
        JNC     8 $             \ Branch if no carry out.
        INC     CX              \ Increment exponent of sum
        RCR     BX              \ Shift result right by 1.
        RCR     DX
        RCR     AX
8 $:    ADD     AX, # 8000      \ Round result
        ADC     DX, # 0
        ADC     BX, # 0
        JNC     0A $            \ Branch if no carry out.
        INC     CX              \ Else increment exponent
        RCR     BX              \ Shift the mantissa right
        RCR     DX
9 $:    POP     SI              \ Restore SI
        MOV     4 [BP], DX
        MOV     2 [BP], BX
        MOV     0 [BP], CX
        TEST    CX, # 7FFE      \ Test for overflow
        JZ      0F $
        POP     BP
        NEXT

0A $:   OR      AX, AX          \ Should we round to even?
        JNE     9 $
        AND     DL, # 0FE       \ Yes, round to even.
        JMP     9 $

0B $:   OR      CH, CH          \ F2 is negative.  Test F1.
        JS      6 $             \ If F1 is negative, add magnitudes.
0C $:   XOR     AH, # 80        \ Subtract magnitudes.  Flip sign of F2.
        CMP     AX, CX          \ Are exponents now equal?
        JNE     10 $            \ Branch if not equal.
        SUB     SI, DX          \ Subtract F1 - F2.
        SBB     DI, BX
        JA      12 $            \ Branch if F1 > F2 with non-zero high part.
        JC      1C $            \ Branch if F1 < F2
        OR      SI, SI
        JNZ     12 $            \ Branch if F1 > F2 , non-zero low part.
1C $:   XOR     CH, # 80        \ Otherwise, change sign of result,
        NEG     SI              \  and negate the mantissa.
        JZ      0D $            \ Branch on low part = 0.
        NOT     DI              \ Usually DI is just complemented.
        JMP     12 $            \ Join common code.

0D $:   NEG     DI              \ Finish negating high part.
        JNZ     12 $            \ If non-zero, join common code.
1F $:   XOR     BX, BX          \ Otherwise result is zero.
        XOR     DX, DX
        XOR     CX, CX
        JMP     15 $

10 $:   JB      11 $            \ Jump if no swap required.
        XOR     AH, # 80
        XCHG    AX, CX          \ Exchange F1 and F2.
        XCHG    BX, DI
        XCHG    DX, SI
        XOR     AH, # 80
11 $:   PUSH    CX              \ Save CX
        SUB     CX, AX          \ CX = exponent difference.  (Underflow?)
        CALL    DENORM          \ Denormalize the smaller.
        POP     CX              \ Restore CX
        NEG     AX              \ Subtract GRS
        SBB     SI, DX          \ Low part of difference.
        SBB     DI, BX          \ High part of difference.
12 $:   JZ      16 $            \ If high word is 0, branch to word shift.
        TEST    CX, # 7FC0      \ Check for "Near Underflow"
        JZ      1F $
        MOV     BX, DI          \ Put high part of result in BX
        OR      BH, BH          \ Test high byte.
        JZ      17 $            \ If zero, branch to byte shift.
        JS      1A $            \ If sign bit set, branch to rounding.
        SHL     AX              \ Shift left by 1 bit.
        RCL     SI
        RCL     BX
        DEC     CX              \ Adjust exponent.   (Underflow?)
        AND     BH, BH          \ Test sign bit.
        JS      1A $            \ Go to rounding if sign bit set.
13 $:   SHL     SI              \ Shift until m.s. bit set.
        RCL     BX
        DEC     CX              \ (Underflow?)
1D $:   AND     BH, BH
14 $:   JNS     13 $            \ Branch back until m.s. bit is set.
15 $:   MOV     DX, SI
        POP     SI              \ Restore SI
        MOV     4 [BP], DX
        MOV     2 [BP], BX
        MOV     0 [BP], CX
        POP     BP
        NEXT

16 $:   MOV     BX, SI          \ Shift by one word.
        MOV     SI, AX
        XOR     AX, AX          \ The rounding and sticky bits are 0.
        CMP     CX, # 7FC0      \ Test for "Near Overflow"
        JZ      1F $
        SUB     CX, # 10        \ Subtract 16 from exponent.
        OR      BX, BX          \ Check if high part is zero.
        JZ      18 $            \ Branch if high = 0.
        OR      BH, BH          \ Check if shift by a byte.
        JNZ     1D $            \ Branch if shift less than 8 bits.
17 $:   XCHG    CX, SI          \ Do a shift by a byte.
        MOV     BH, BL
        MOV     BL, CH
        MOV     CH, CL
        MOV     CL, AH
        XOR     AX, AX
        XCHG    CX, SI
        SUB     CX, # 8         \ Adjust exponent.
        JMP     1D $            \ Jump to finish shifts.

18 $:   OR      SI, SI          \ Second shift by a word.
        JZ      19 $            \ Branch if total result is zero.
        MOV     BX, SI          \ Move in guard bit.
        XOR     SI, SI
        SUB     CX, # 10        \ Adjust exponent
        OR      BH, BH
        JNZ     14 $            \ Test byte shift.
        JMP     17 $            \ Check remaining shifts.

19 $:   POP     SI              \ Restore SI
        XOR     CX, CX
        MOV     4 [BP], CX
        MOV     2 [BP], CX
        MOV     0 [BP], CX
        POP     BP
        NEXT

1A $:   ADD     AX, # 8000      \ Round result
        ADC     SI, # 0
        ADC     BX, # 0
        JC      1B $            \ Branch if Carry-out generated.
        OR      AX, AX          \ Check if round to even required.
        JNZ     15 $            \ Branch if not.
        AND     SI, # FFFE      \ Round to even.
        JMP     15 $            \ Push results.

1B $:   RCR     BX              \ Carry-out was generated.
        RCR     SI
        INC     CX              \ Adjust exponent.
        JMP     15 $            \ Push results.

        END-CODE

\ Floating Point Multiply.

HEX

CODE F*   ( F: r1 r2 -- r3 )      \ Time: 256 usec.
        CLEAR_LABELS
        PUSH    BP
        MOV     BP, FSP
        MOV     AX, 0 [BP]      \ SX2
        MOV     DI, 2 [BP]      \ A
        MOV     BX, 4 [BP]      \ B
        ADD     BP, # 6
        MOV     FSP BP
        MOV     CX, 0 [BP]      \ SX1
        MOV     DX, AX          \ SX2
        XOR     DX, CX          \ Get Sign of product
        AND     DX, # 8000      \ Isolate the sign.
        AND     AX, # 7FFF
        AND     CX, # 7FFF
        ADD     AX, CX          \ Add the exponents,
        SUB     AX, # 3FFF      \ Adjust for the bias.
        JS      6 $             \ Jump if Overflow or Underflow
        AND     AX, # 7FFF      \ Isolate exponent of product,
        OR      AX, DX          \ then join sign and exponent.
        MOV     CX, 2 [BP]      \ C
        MOV     DX, 4 [BP]      \ D
        PUSH    SI
        PUSH    AX              \ Temporarily save product SX
        OR      DI, DI          \ Check if F2 is normal.
        JNS     4 $             \ If not normal, treat as zero.
        OR      CH, CH          \ Check if F1 is normal.
        JNS     4 $             \ If not normal, treat as zero.
        OR      DX, DX          \ Check for low parts = zero.
        JZ      7 $
        OR      BX, BX
        JZ      8 $
        MOV     SI, DX          \ D
        MOV     AX, BX          \ B
        MUL     DX              \ BD to (DX, AX)
        PUSH    AX              \ Save BDL
        MOV     AX, SI          \ D
        MOV     SI, DX          \ BDH to SI
        MUL     DI              \ AD to (DX,AX)
        ADD     SI, AX          \ ADL+BDH to SI
        ADC     DX, # 0         \ ADH
        MOV     AX, BX          \ B
        MOV     BX, DX          \ ADH to BX
        MUL     CX              \ BC to (DX,AX)
        ADD     AX, SI          \ BCL+ADL+BDH to AX
        ADC     BX, DX          \ BCH+ADH
        SBB     SI, SI          \ Set SI to carry-out
        POP     DX              \ BDL
        OR      DX, DX
        JZ      1 $
        OR      AX, # 1         \ In case we need it for rounding!
1 $:    NEG     SI
2 $:    XCHG    AX, DI          \ A to AX, PROD1 to DI
        MUL     CX              \ AC to (DX,AX)
        ADD     AX, BX          \ ACL+BCH+ADH
        ADC     DX, SI          \ ACH+carry
        POP     BX              \ SX of product.
        JS      3 $             \ Branch if m.s. bit set
        SHL     DI              \ Otherwise, shift left by 1 bit.
        RCL     AX
        RCL     DX
        DEC     BX              \ Adjust exponent.  (Underflow?)
3 $:    ADD     DI, # 8000      \ Round result.
        ADC     AX, # 0
        ADC     DX, # 0
        JC      13 $            \ If carry set, re-normalize.
        OR      DI, DI
        JNZ     0C $            \ Branch if round to even is NOT required.
        AND     AX, # FFFE      \ Round to even!
        JMP     0C $            \ Push results.

4 $:    JMP     14 $            \ Needed because of limited jumps

6 $:    JMP     16 $            \ Needed because of limited jumps

7 $:    OR      BX, BX          \ D=0 case.
        JZ      0A $
        MOV     AX, BX
        MUL     CX              \ AC
        MOV     BX, DX          \ ACH
        XOR     SI, SI
        JMP     2 $

8 $:    MOV     AX, DI          \ B=0 case.
        MUL     DX
        MOV     BX, DX
        XOR     SI, SI
        JMP     2 $

9 $:    POP     SI
        JMP     5 $

0A $:   MOV     AX, DI          \ B = D = 0 case.
        XOR     DI, DI
        MUL     CX
        POP     BX
        OR      DX, DX
        JS      0C $
        TEST    BX, # 7FFF      \ Check for underflow
        JZ      9 $
        DEC     BX
0B $:   RCL     AX              \ Re-normalize.
        RCL     DX
0C $:   POP     SI
        MOV     4 [BP], AX      \ Push results.
        MOV     2 [BP], DX
        MOV     0 [BP], BX
        POP     BP
        NEXT

13 $:   RCR     DX
        INC     BX
        JMP     0C $

14 $:   XOR     AX, AX          \ Send zero results.
        ADD     SP, # 2
        POP     SI              \ Restore SI
15 $:   XOR     AX, AX          \ Send zero result
        MOV     4 [BP], AX      \ Push zero result.
        MOV     2 [BP], AX
        MOV     0 [BP], AX
        POP     BP
        NEXT

16 $:                           \ Overflow or Underflow
        CMP     AX, # C000
        JA      15 $            \ Return zero for Underflow case.
        MOV     BX, # -1        \ Overflow case
        MOV     4 [BP], BX
        MOV     2 [BP], BX
        MOV     BX, # 7FFF
        MOV     0 [BP], BX
        POP     BP
        MOV     BX, # LAST @ NAME>
        MOV     CX, # 2
        PUSH    BX
        PUSH    CX
        MOV     AX, # ' FPERR
        JMP     AX

        END-CODE

PREVIOUS

\ Floating division.
\    The division routine that follows is based on the work of Roedy Green,
\    the author of BBL/Abundance.
\    The speed on a standard XT-PC clone is about 290 micro-seconds.

CODE F/    ( F: r1 r2 -- r3 )     \ Time: 290 usec.
        CLEAR_LABELS
        PUSH    BP
        MOV     BP, FSP
        MOV     AX, 0 [BP]      \ SX2
        MOV     CX, 2 [BP]      \ Hi2
        MOV     BX, 4 [BP]      \ Lo2
        ADD     BP, # 6
        MOV     FSP BP
        MOV     DX, 0 [BP]      \ SX1
        MOV     DI, DX
        XOR     DI, AX
        AND     DI, # 8000      \ Get sign of result.
        AND     DX, # 7FFF
        AND     AX, # 7FFF
        SUB     DX, AX          \ Difference of biased exponents.
        ADD     DX, # 3FFF      \ Re-bias the exponent.
        CMP     DX, # 7FFD      \ Check for near overflow.
        JAE     0 $             \ Jump if nearly overflow.
        OR      DI, DX          \ Join with the sign of the quotient.
        OR      CH, CH          \ Check for unnormalized (zero) divisor.
        JNS     2 $             \ Branch to Divide by zero routine.
        MOV     DX, 2 [BP]      \ Hi1
        OR      DH, DH          \ Check for unnormalized numerator.
        JNS     3 $             \ Branch to zero case, if unnormalized.
        MOV     AX, 4 [BP]      \ Lo1
        PUSH    BP              \ Save FSP
        PUSH    SI              \ Save SI
        XOR     BP, BP          \ Zero out BP
        CMP     DX, CX          \ Compare numerator with denominator.
        JB      4 $             \ If num < den, begin division process.
        JA      1 $             \ If num > den, shift numerator right by 1.
        CMP     AX, BX          \ If high parts equal, check low parts.
        JAE     1 $             \ If num >= den, shift num right by 1.
        MOV     SI, # FFFF      \ MS num = MS den.  Start with trial g0 = s-1
        MOV     BP, AX
        SUB     BP, BX          \ midnum - lsden
        MOV     AX, BX
        ADD     BP, CX          \ msnum + (midnum-lsnum)
        JC      7 $             \ Good quotient if Carry is set
        JMP     1 $             \ Otherwise, correct result

0 $:    OR      CX, CX          \ Overflow or Underflow
        JNS     2 $             \ Check for Divide by zero.
        MOV     AX, 2 [BP]
        OR      AH, AH
        JNS     3 $             \ Jump if numerator is unnormalized (zero)
        CMP     DX, # C000
        JAE     3 $             \ For underflow, treat as zero
        OR      DI, # 7FFF
        MOV     CX, # 2         \ Overflow flag
        JMP     11 $

1 $:                            \ Num >= Den
        INC     DI              \ Increment exponent of quotient.  (OVFL?)
        SHR     DX, # 1         \ Shift accum right by 1
        RCR     AX, # 1
        JNC     4 $             \ If low bit clear, join normal sequence.
        DIV     CX              \ Initial approximation.
        MOV     SI, AX          \ quotient g0
        MOV     BP, DX          \ remainder r0
        MUL     BX              \ g0 * denlo
        NOT     AX              \ Negate low part of correction factor
        SUB     AX, # 7FFF      \ Adjust for carry from shift.
        JMP     5 $             \ Join rest of main code.

2 $:    AND     DI, # 8000      \ Divide by zero case.
        OR      DI, # 7FFF      \ Set exponent bits to 1.
        MOV     CX, # 1         \ Set flag for divide by zero
11 $:   MOV     AX, # -1        \ Set largest possible number.
        MOV     2 [BP], AX
        MOV     4 [BP], AX
        MOV     0 [BP], DI
        POP     BP
        MOV     BX, # LAST @ NAME>      \ Point to this routine.
        PUSH    BX
        PUSH    CX
        MOV     AX, # ' FPERR
        JMP     AX

3 $:                            \ Numerator is zero.  Give 0 result.
        XOR     AX, AX
        MOV     0 [BP], AX
        MOV     2 [BP], AX
        MOV     4 [BP], AX
        POP     BP
        NEXT

4 $:    DIV     CX              \ Initial approximation. Divide by denhi.
        MOV     SI, AX          \ Save initial quotient estimate = g0
        MOV     BP, DX          \ Save r0
        MUL     BX              \ Correction factor = g0 * denlo
        NEG     AX
5 $:    SBB     BP, DX          \ r1 = s * r0 - g0 * denlo
        JNC     7 $             \ Jump if r1 >= 0   (no borrow)
6 $:    DEC     SI              \ Decrement g by 1, at least.
        ADD     AX, BX          \ r2 = r1 + den
        ADC     BP, CX
        JC      7 $             \ Jump if r2 >= 0   ( no carry)
        DEC     SI              \ Decrement g by 1 for last time.
        ADD     AX, BX
        ADC     BP, CX          \ r3 = r2 + den
7 $:    MOV     DX, BP
        PUSH    SI              \ Save MS quotient
        CMP     DX, CX
        JAE     0E $
        DIV     CX              \ Approximate LS part of quotient.
        MOV     SI, AX
        MOV     BP, DX
        MUL     BX              \ Correction factor
        NEG     AX
        SBB     BP, DX
        JNC     9 $             \ Jump if no borrow
8 $:    DEC     SI              \ Decrement g1 by 1
        ADD     AX, BX          \ Add denominator back in to remainder
        ADC     BP, CX
        JC      9 $             \ Jump if no carry
        DEC     SI              \ Decrement g1 again
        ADD     AX, BX          \ Add denominator again
        ADC     BP, CX
9 $:    POP     DX              \ Get MS part of quotient.
        SHL     AX, # 1         \ Shift remainder left by 1
        RCL     BP, # 1
        JC      0A $            \ If ms bit was set, jump.
        CMP     BP, CX          \ Compare MS 2*remainder with denhi
        JB      0B $            \ If 2*rem < den, jump to no rounding
        JA      0A $            \ If 2*rem > den, jump to rounding case
        CMP     AX, BX          \ If MS parts are equal, compare LS parts
        JB      0B $            \ If 2*rem < den, jump to no rounding
        JE      10 $            \ If 2*rem > den, jump to rounding case
0A $:   ADD     SI, # 1         \ Round up, for sure.
        ADC     DX, # 0
        JC      0F $
0B $:   MOV     AX, SI          \ Put ls part of quotient in AX
        POP     SI              \ Restore SI and BP registers
        POP     BP              \ Restore Floating Stack Pointer
        MOV     4 [BP], AX      \ Push results and return.
        MOV     2 [BP], DX
        MOV     0 [BP], DI
        POP     BP              \ Restore original BP
        NEXT

0E $:   MOV     SI, # FFFF      \ MS num = MS den.  Start with trial g0 = s-1
        MOV     BP, AX
        SUB     BP, BX          \ midnum - lsden
        MOV     AX, BX
        ADD     BP, CX          \ msnum + (midnum-lsnum)
        JC      9 $             \ Good quotient if Carry is set
        JMP     8 $             \ Otherwise, correct result

0F $:   RCR     DX, # 1         \ Oops!  We overflowed available bits.
        RCR     SI, # 1         \ So shift right and adjust the exponent.
        INC     DI              \ (Overflow?)
        JMP     0B $

10 $:   ADD     SI, # 1         \ Round to even.
        ADC     DX, # 0
        JC      0F $            \ In rare cases we will get a carry-out.
        AND     SI, # FFFE      \ Otherwise, round to even
        JMP     0B $

        END-CODE


\  End of basic 4-functions.

\  Floating Square Root

DECIMAL

CODE FSQRT    ( F: r1 -- r2 )
        CLEAR_LABELS            \ Clear the local label table.
        PUSH    BP
        MOV     BP, FSP
        MOV     DI, 0 [BP]      \ SEXP  ( Sign and exponent of f1 )
        MOV     BX, 2 [BP]      \ MS part of f1.  Num-hi.
        MOV     DX, 4 [BP]      \ LS part of f1.  Num-low.
        XOR     AX, AX
        OR      BX, BX
        JNS     2 $             \ If MS bit not set, treat as zero.
        OR      DI, DI
        JNS     0 $
        XOR     AX, AX          \ Negative argument to square root
        MOV     2 [BP], AX
        MOV     4 [BP], AX
        MOV     AX, # -1
        MOV     0 [BP], AX
        MOV     AX, # LAST @ NAME>
        POP     BP
        PUSH    AX
        MOV     AX, # 4         \ Negative argument flag
        PUSH    AX
        MOV     AX, # ' FPERR
        JMP     AX

0 $:    PUSH    SI              \ Save some registers.
        PUSH    BP
        XOR     SI, SI          \ Clear Subtractor-hi.
        XOR     BP, BP          \ Clear Subtractor-lo.
        MOV     CX, # 13        \ Set count of 13.
        SHL     DX              \ Shift numerator left by 1.
        RCL     BX
        RCL     AX
        TEST    DI, # 1         \ Do we need another shift?
        JZ      1 $
        SHL     DX              \ Yes.
        RCL     BX
        RCL     AX
1 $:    SAR     DI              \ Calculate new exponent.
        ADD     DI, # 8192      \ = 2000 in hex.
        JMP     4 $             \ Enter loop in the middle.

2 $:    MOV     0 [BP], AX      \ Zero result case.
        MOV     2 [BP], AX
        MOV     4 [BP], AX
        POP     BP
        NEXT

3 $:    SHL     SI              \ Shift left Subtractor by 1.
        CMP     AX, SI
        JBE     5 $             \ Jump if Num <= Sub.
4 $:    STC
        SBB     AX, SI          \ Num - Sub -1 -> Num.
        ADD     SI, # 2         \ Put new bit into subtractor.
5 $:    SHL     DX              \ Shift Num left twice.
        RCL     BX
        RCL     AX
        SHL     DX
        RCL     BX
        RCL     AX
        LOOP    3 $             \ Repeat first iteration 13 times.
        MOV     CX, # 16
6 $:    SHL     SI              \ Shift subtractor left by 1.
        RCL     BP
        CMP     DX, BP          \ Compare MS parts.
        JA      7 $             \ If Num > Sub, then subtract.
        JB      8 $             \ If Num < Sub, go to shift.
        CMP     AX, SI          \ Compare LS parts.
        JBE     8 $
7 $:    STC                     \ Subtract case.
        SBB     AX, SI          \ Num - Sub - 1 -> Num.
        SBB     DX, BP
        ADD     SI, # 2         \ Put in new quotient bit.
        ADC     BP, # 0
8 $:    SHL     BX              \ Shift Num left by 2 places.
        RCL     AX
        RCL     DX
        SHL     BX
        RCL     AX
        RCL     DX
        LOOP    6 $             \ Repeat 16 times.
\ For final iterations, use triple precision.
\ ( BH, DX, AX ) is Num.   ( BL, BP, SI ) is Subtractor.
        MOV     CX, # 4
9 $:    SHL     SI              \ Shift Subtractor left by 1.
        RCL     BP
        RCL     BL
        CMP     BH, BL          \ Compare MS part.
        JA      10 $
        JB      11 $
        CMP     DX, BP          \ Compare middle part.
        JA      10 $
        JB      11 $
        CMP     AX, SI          \ Compare LS part.
        JBE     11 $
10 $:   STC
        SBB     AX, SI          \ Num - Sub - 1 -> Num.
        SBB     DX, BP
        SBB     BH, BL
        ADD     SI, # 2
        ADC     BP, # 0
        ADC     BL, # 0
11 $:   SHL     AX              \ Shift Num left 2 places.
        RCL     DX
        RCL     BH
        SHL     AX
        RCL     DX
        RCL     BH
        LOOP    9 $             \ Iterate 4 times.
\ Main iterations finished.  Now shift answer right by 2 and round.
        SHR     BL
        RCR     BP
        RCR     SI
        SHR     BL
        RCR     BP
        RCR     SI
        JNC     13 $
        TEST    SI, # 1         \ Check LS bit.
        JNZ     12 $
        OR      BH, BH          \ Round to even check.
        JNZ     12 $
        OR      DX, AX
        JZ      13 $
12 $:   ADD     SI, # 1
        ADC     BP, # 0
        JNC     13 $
        RCR     BP
        RCR     SI
        INC     DI
13 $:   MOV     AX, BP
        MOV     BX, SI
        POP     BP
        POP     SI
        MOV     4 [BP], BX      \ Push LS part of root.
        MOV     2 [BP], AX      \ Push MS part of root.
        MOV     0 [BP], DI      \ Push Sign+Exponent.
        POP     BP              \ Restore original BP
        NEXT
        END-CODE

\ End of Square-root code.

HEX
CODE FDUP   ( F: r -- r r )
        MOV     BX, FSP
        MOV     CX, 0 [BX]
        SUB     BX, # 6
        MOV     FSP BX
        MOV     0 [BX], CX
        MOV     CX, 8 [BX]
        MOV     2 [BX], CX
        MOV     CX, 0A [BX]
        MOV     4 [BX], CX
        NEXT
        END-CODE

CODE FDROP   ( F: r1 -- )
        ADD     WORD FSP # 6
        NEXT
        END-CODE

CODE F2DROP   ( F: r1 r2 -- )
        ADD     WORD FSP # 0C
        NEXT
        END-CODE

CODE FNIP   ( F: r1 r2 -- r2 )
        MOV     BX, FSP
        MOV     AX, 0 [BX]
        MOV     6 [BX], AX
        MOV     AX, 2 [BX]
        MOV     8 [BX], AX
        MOV     AX, 4 [BX]
        MOV     0A [BX], AX
        ADD     BX, # 6
        MOV     FSP BX
        NEXT
        END-CODE

CODE FOVER   ( F: r1 r2 -- r1 r2 r1 )
        MOV     BX, FSP
        SUB     BX, # 6
        MOV     FSP BX
        MOV     AX, 0C [BX]
        MOV     0 [BX], AX
        MOV     AX, 0E [BX]
        MOV     2 [BX], AX
        MOV     AX, 10 [BX]
        MOV     4 [BX], AX
        NEXT
        END-CODE

: F2DUP   ( F: r1 r2 -- r1 r2 r1 r2 )
        FOVER FOVER ;

CODE FSWAP   ( F: r1 r2 -- r2 r1 )
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     AX, 0 [BX]
        XCHG    AX, 6 [BX]
        MOV     0 [BX], AX
        MOV     AX, 2 [BX]
        XCHG    AX, 8 [BX]
        MOV     2 [BX], AX
        MOV     AX, 4 [BX]
        XCHG    AX, 0A [BX]
        MOV     4 [BX], AX
        NEXT
        END-CODE

CODE FROT   ( F: r1 r2 r3 -- r2 r3 r1 )
        MOV     BX, FSP
        MOV     AX, 0 [BX]
        XCHG    AX, 6 [BX]
        XCHG    AX, 0C [BX]
        MOV     0 [BX], AX
        MOV     AX, 2 [BX]
        XCHG    AX, 8 [BX]
        XCHG    AX, 0E [BX]
        MOV     2 [BX], AX
        MOV     AX, 4 [BX]
        XCHG    AX, 0A [BX]
        XCHG    AX, 10 [BX]
        MOV     4 [BX], AX
        NEXT
        END-CODE

CODE F-ROT   ( F: r1 r2 r3 -- r3 r1 r2 )
        MOV     BX, FSP
        MOV     AX, 0 [BX]
        XCHG    AX, 0C [BX]
        XCHG    AX, 6 [BX]
        MOV     0 [BX], AX
        MOV     AX, 2 [BX]
        XCHG    AX, 0E [BX]
        XCHG    AX, 8 [BX]
        MOV     2 [BX], AX
        MOV     AX, 4 [BX]
        XCHG    AX, 10 [BX]
        XCHG    AX, 0A [BX]
        MOV     4 [BX], AX
        NEXT
        END-CODE

CODE FPICK  ( F: rn rn-1 ... r0 -- rn rn-1 ... r0 rn ; n -- )
        POP     DI
        ADD     DI, # 1
        MOV     BX, DI
        SHL     DI, # 1
        ADD     DI, BX
        SHL     DI, # 1
        MOV     BX, FSP
        SUB     BX, # 6
        MOV     FSP BX
        MOV     AX, 0 [BX+DI]
        MOV     0 [BX], AX
        MOV     AX, 2 [BX+DI]
        MOV     2 [BX], AX
        MOV     AX, 4 [BX+DI]
        MOV     4 [BX], AX
        NEXT
        END-CODE

CODE FNSWAP   ( F: rn rn-1 ... r0 -- r0 rn-1 ... r1 rn ; n -- )
        POP     DI
        SHL     DI, # 1
        MOV     BX, DI
        SHL     DI, # 1
        ADD     DI, BX          \ n*6
        MOV     BX, FSP
        MOV     AX, 0 [BX]
        XCHG    AX, 0 [BX+DI]
        MOV     0 [BX], AX
        ADD     BX, # 2
        MOV     AX, 0 [BX]
        XCHG    AX, 0 [BX+DI]
        MOV     0 [BX], AX
        ADD     BX, # 2
        MOV     AX, 0 [BX]
        XCHG    AX, 0 [BX+DI]
        MOV     0 [BX], AX
        NEXT
        END-CODE

CODE F0<   ( F: r -- ; -- flag )
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     AX, 0 [BX]
        MOV     CX, 2 [BX]
        ADD     BX, # 6
        MOV     FSP BX
0 $:    OR      CX, CX          \ Test for zero
        JNS     1 $
        OR      AX, AX
        JNS     1 $
2 $:    MOV     AX, # -1
        PUSH    AX
        NEXT
1 $:    XOR     AX, AX
        PUSH    AX
        NEXT
        END-CODE

CODE FDUP0<   ( F: r1 -- r1 ; -- flag )
        MOV     BX, FSP
        MOV     AX, 0 [BX]
        MOV     CX, 2 [BX]
        JMP     0 $
        END-CODE

CODE F0>   ( F: r -- ; -- flag )
        MOV     BX, FSP
        MOV     AX, 0 [BX]
        MOV     CX, 2 [BX]
        ADD     BX, # 6
        MOV     FSP BX
        OR      CX, CX
        JNS     1 $
        OR      AX, AX
        JS      1 $
        JMP     2 $
        END-CODE

CODE F0=   ( F: r -- ; -- flag )
        MOV     BX, FSP
        MOV     AX, 2 [BX]
        ADD     BX, # 6
        MOV     FSP BX
        OR      AX, AX
        JNS     2 $
        JMP     1 $
        END-CODE

CODE FDUP0=   ( F: r -- r ; -- flag )
        MOV     BX, FSP
        MOV     AX, 2 [BX]
        OR      AX, AX
        JNS     2 $
        JMP     1 $
        END-CODE

CODE F=    ( F: r1 r2 -- ; -- flag )
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     AX, 2 [BX]      \ Get MS fraction of top f.p. number
        MOV     DX, 8 [BX]      \ Get MS fraction of second f.p. number
        MOV     CX, AX
        OR      AX, DX
        JNS     1 $             \ Jump if r1 and r2 are both zero
        XOR     DX, CX
        MOV     AX, 0 [BX]      \ Get Sign and Exponent of top f.p. number
        XOR     AX, 6 [BX]      \ This code is fairly fast because
        OR      DX, AX          \ it avoids most conditional branches.
        MOV     AX, 4 [BX]
        XOR     AX, 0A [BX]
        OR      DX, AX
        XOR     AX, AX
        ADD     DX, # -1
        SBB     AX, # 0
        NOT     AX
        PUSH    AX
        ADD     BX, # 0C
        MOV     FSP BX
        NEXT

1 $:    MOV     AX, # -1
        PUSH    AX
        ADD     BX, # 0C
        MOV     FSP BX
        NEXT

        END-CODE

CODE F2DUP>   ( F: r1 r2 -- ; -- flag )
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     DI, 2 [BX]      \ MS fract of r2
        OR      DI, DI
        JNS     1 $             \ Jump if r2 = 0
        MOV     CX, 8 [BX]      \ MS fract of r1
        OR      CH, CH
        JNS     2 $             \ Jump if r1 = 0
        MOV     AX, 0 [BX]      \ SX of r2
        OR      AH, AH
        JS      3 $             \ Jump if r2 < 0
        MOV     DX, 6 [BX]      \ SX of r1
        OR      DH, DH
        JS      5 $             \ Jump if (r1 < 0) and (r2 > 0)
8 $:    CMP     DX, AX          \ Signs are positive.
        JA      7 $             \ Jump if Exponent of r1 > Exponent of r2
        JB      5 $             \ Jump if Exponent of r1 < Exponent of r2
        CMP     CX, DI          \ Exponents are equal.  Check MS parts
        JA      7 $             \ Jump if MS part of r1 > MS part of r2
        JB      5 $             \ Jump if MS part of r1 < MS part of r2
        MOV     CX, 0A [BX]     \ LS part of r1
        CMP     CX, 4 [BX]      \ Compare with LS part of r2
        JA      7 $
        JMP     5 $

1 $:    MOV     AX, 8 [BX]      \ r2 = 0 case.  Check r1.
        OR      AH, AH
        JNS     5 $             \ Jump if r1 = r2 = 0
        MOV     DX, 6 [BX]
        OR      DH, DH
        JNS     7 $             \ Jump if r1 > 0
5 $:    XOR     AX, AX          \ Push a false flag.
        PUSH    AX
        NEXT

2 $:    MOV     AX, 0 [BX]      \ r1 = 0 case.  Test r2.
        OR      AX, AX
        JNS     5 $
7 $:    MOV     AX, # -1        \ Push a true flag
        PUSH    AX
        NEXT

3 $:    MOV     DX, 6 [BX]      \ r2 < 0 case.  Get SX of r1
        OR      DX, DX
        JNS     7 $
9 $:    CMP     DX, AX          \ r1 and r2 both negative case.
        JB      7 $
        JA      5 $
        CMP     CX, DI          \ exp1 = exp2 and signs both negative.
        JB      7 $             \ Check MS part of fraction.
        JA      5 $
        MOV     CX, 4 [BX]      \ MS parts are equal.  Check LS parts
        MOV     DI, 0A [BX]
        CMP     DI, CX
        JB      7 $
        JMP     5 $
        END-CODE

CODE F2DUP<   ( F: r1 r2 -- ; -- flag )
        MOV     BX, FSP
        MOV     DI, 2 [BX]
        OR      DI, DI
        JNS     0B $            \ Jump if r2 = 0
        MOV     CX, 8 [BX]
        OR      CH, CH
        JNS     0C $            \ Jump if r1 = 0
        MOV     AX, 0 [BX]
        OR      AH, AH
        JS      0D $            \ Jump if r2 < 0
        MOV     DX, 6 [BX]
        OR      DX, DX
        JS      7 $             \ If r1 < 0, result is true.
        JMP     9 $

0B $:   MOV     AX, 8 [BX]
        OR      AH, AH          \ r2 = 0 case.  Check r1
        JNS     5 $             \ If r1 = 0, result is false.
        MOV     DX, 6 [BX]
        OR      DH, DH
        JNS     5 $             \ r2 = 0.  If r1 >0, result is false.
        JMP     7 $

0C $:   MOV     AX, 0 [BX]      \ r1 = 0 case. Check sign of r2.
        OR      AH, AH
        JS      5 $             \ If r2 < 0, result is false.
        JMP     7 $

0D $:   MOV     DX, 6 [BX]
        OR      DX, DX          \ r2 < 0 case.  Check r1.
        JNS     5 $             \ If r1 > 0, result is false.
        JMP     8 $
        END-CODE

: F<    ( F: r1 r2 -- ; -- flag )
        F2DUP< F2DROP ;

: F>    ( F: r1 r2 -- ; -- flag )
        F2DUP> F2DROP ;

: F<=   ( F: r1 r2 -- ; -- flag )
        F2DUP> F2DROP 0= ;

: F>=   ( F: r1 r2 -- ; -- flag )
        F2DUP< F2DROP 0= ;

: FMAX  ( F: r1 r2 -- r3 )
        F2DUP>  IF  FDROP  ELSE  FNIP  THEN ;

: FMIN  ( F: r1 r2 -- r3 )
        F2DUP<  IF  FDROP  ELSE  FNIP  THEN ;

CODE FABS   ( F: r1 -- r2 )
        MOV     BX, FSP
        AND     WORD 0 [BX], # 7FFF
        NEXT
        END-CODE

CODE FNEGATE   ( F: r1 -- r2 )
        MOV     BX, FSP
        XOR     WORD 0 [BX], # 8000
        NEXT
        END-CODE

DECIMAL


