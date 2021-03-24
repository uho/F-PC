\ SFLOAT3 - Floating Point Extensions
\
\ CR CR  .( Copyright 1988 by R. L. Smith )  CR
\        .( Version 2.00-A )  CR
\
\
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

ANEW FEXPSRC
\ LOADED,
PREFIX
HEX
ALSO HIDDEN DEFINITIONS

CREATE 1GEXPTAB

        \ Table of (1 - e^(k/16))  for k <= 0
        3FFE , FE8C , DC02 ,  3FFE , EDF2 , 36CB ,  3FFE , DC45 , 6CF4 ,
        3FFE , C974 , D039 ,  3FFE , B56D , 8E6D ,  3FFE , A01B , 9EA1 ,
        3FFE , 8969 , AD21 ,  3FFD , E282 , 0C2A ,  3FFD , AF12 , FDA8 ,
        3FFC , F0A5 , 76C5 ,  3FFB , F82A , 021C ,  0000 , 0000 , 0000 ,

        \ Table of (e^(k/16) - 1)  for k >= 0
        3FFC , 8415 , ABBF ,  3FFD , 8858 , 116E ,  3FFD , D32E , 05C3 ,
        3FFE , 916B , C788 ,  3FFE , BBD2 , 2EC1 ,  3FFE , E8F4 , A27B ,
        3FFF , 8C80 , 2478 ,  3FFF , A612 , 98E2 ,  3FFF , C14B , 4312 ,
        3FFF , DE45 , 5DF8 ,  3FFF , FD1D , E618 ,

42 1GEXPTAB + CONSTANT GEXPTAB    \ Points to zero point of table.

LABEL Y**2                      \ Argument in (BX,CX)
        CLEAR_LABELS
                                \ Start calculation of y^2
        MOV     AX, BX
        MUL     CX              \ A*B
        XCHG    AX, CX          \ ABL -> CX
        MOV     AL, AH
        MUL     AL              \ B^2  (note 8 bit multiply)
        SHR     AX
        ADD     CX, AX
        ADC     DX, # 0         \ AB + (B^2)/2
        XCHG    DX, BX          \ A -> DX,  AB -> (BX,CX)
        MOV     AX, DX
        MUL     AX              \ A^2
        SHL     CX              \ 2AB + B^2
        RCL     BX
        ADC     DX, # 0
        ADD     BX, AX
        ADC     DX, # 0
        SHL     CX              \ Round
        ADC     BX, # 0
        ADC     DX, # 0
        MOV     CX, BX
        MOV     BX, DX          \ y^2 -> (BX,CX)
        RET
        END-CODE

LABEL FRACT*
\ 32 bit by 32 bit multiply.  Let  (DI,BP) = (A,B), and (BX,CX) = (C,D)
        MOV     AX, BP
        MUL     CX              \ B*D
        MOV     AX, CX          \ D
        MOV     CX, DX          \ BDhi
        MUL     DI              \ A*D
        ADD     CX, AX          \ BDhi + ADlo
        ADC     DX, # 0         \ ADhi + carry1 , no carry out in this case.
        XCHG    DX, BP          \ B -> DX,  ADhi -> BP
        MOV     AX, BX          \ C
        MUL     DX              \ B*C
        ADD     CX, AX          \ BDhi + ADlo + BClo
        ADC     BP, # 0         \ ADhi + carry1 + carry2
        MOV     AX, DI          \ A
        XCHG    BX, DX          \ BChi -> CX, C -> DX
        MUL     DX              \ A*C
        ADD     BX, AX          \ BChi + AClo
        ADC     DX, # 0         \ AChi + carry
        ADD     BX, BP          \ BChi + AClo + ADhi + previous carries.
        ADC     DX, # 0         \ AChi + carry -> Product high part.
        RET                     \ Product in (DX,BX)
        END-CODE

LABEL GEXP1-                     \ Negative argument case
        CLEAR_LABELS
\ On entry, y = x*2^4 = (BX,CX), x = (SI,(SP)), x*(2/45) in (DX,AX)
        MOV     BP, # 4444
        SUB     BP, AX
        MOV     AX, # 4444      \ 4/15
        SBB     AX, DX          \ ((4/15) - (2/45)*x)
        MUL     SI              \ * x
        MOV     BP, # 5555
        MOV     DI, # 5555
        SUB     DI, AX
        SBB     BP, DX          \ (1/3) - x * ((4/15) - (2/45)*x)
        MOV     DX, BP          \ Note an implied addition by 1
        MOV     AX, DI          \ 17 bit multiply
        SHR     DI
        OR      BH, BH
        JS      1 $
        ADD     DI, AX
1 $:    RCR     DI
        MOV     AX, BX          \ yhi
        MUL     DX              \ * y
        ADD     AX, DI
        ADC     DX, # 0
        XOR     DI, DI
        ADD     AX, CX          \ + y
        ADC     DX, BX          \ Product in (DI,DX,AX)
        ADC     DI, # 0
        MOV     BP, # 5555
        MOV     AX, # 0055
        SUB     BP, DX
        SBB     AX, DI          \ ((1/3)*(2^-8) - y*(2^-16)*prev
        MOV     DI, AX
        CALL    Y**2
        CALL    FRACT*
        POP     AX              \ xlo
        SUB     AX, BX
        SBB     SI, DX          \ x - prev
        SHR     SI              \ Shift right by 1
        RCR     AX
        ADC     AX, # 0         \ Round
        ADC     SI, # 0
        NOT     SI              \ Subtract from 1 by negating result.
        NEG     AX
        CMC
        ADC     SI, # 0
        MOV     DX, SI
        POP     SI              \ Restore SI and BP
        POP     BP
        MOV     BX, FSP
        MOV     4 [BX], AX      \ Push result
        MOV     2 [BX], DX
        MOV     AX, # 3FFF      \ Exponent of result.
        MOV     0 [BX], AX
        NEXT
        END-CODE

CODE GEXP1   ( F: r1 -- r2 )  \  |f1| < 2^-4  \  GEXP1 = (exp(r1) - r1) / r1 .
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     CX, 0 [BX]      \ Sign and biased exponent.
        MOV     DX, 4 [BX]      \ LS part of r1
        MOV     BX, 2 [BX]      \ MS part of r1
        MOV     DI, CX          \ Save sign
        AND     CX, # 7FFF      \ Mask off sign bit
        SUB     CX, # 3FFB      \ Get unbiased exponent + 4
        CMP     CX, # -8
        JG      3 $
        CMP     CX, # -20
        JG      1 $
        XOR     AX, AX          \ Unbiased exponent <= -36
        MOV     DX, # 4000
        MOV     CX, # 8000
        MOV     BX, FSP
        MOV     4 [BX], AX
        MOV     2 [BX], CX
        MOV     0 [BX], DX
        NEXT

1 $:    CMP     CX, # -10
        JG      2 $
        MOV     DX, BX          \ Shift right by 16
        XOR     BX, BX
        ADD     CX, # 10        \ Adjust exponent
2 $:    CMP     CX, # -8
        JG      3 $
        MOV     DL, DH          \ Shift right by 8.
        MOV     DH, BL
        MOV     BL, BH
        XOR     BH, BH
        ADD     CX, # 8
3 $:    PUSH    BP
        PUSH    SI
        OR      CX, CX
        JZ      5 $
        NEG     CX
4 $:    SHR     BX
        RCR     DX
        LOOP    4 $
        ADC     DX, # 0         \ Round
        ADC     BX, # 0
5 $:    MOV     CX, DX          \ y = x*(2^4) in (BX,CX)
        MOV     SI, BX
        SHR     SI              \ Shift right by 4.
        RCR     DX
        SHR     SI
        RCR     DX
        SHR     SI
        RCR     DX
        SHR     SI
        RCR     DX
        ADC     DX, # 0
        ADC     SI, # 0         \ Round
        PUSH    DX              \ x in (SI,(SP))
        MOV     AX, # 0B61      \ 2/45
        MUL     SI              \ * x
        OR      DI, DI
        JNS     6 $             \ Jump if r1 is positive.
        JMP     GEXP1-          \ Jump to negative case.
6 $:    ADD     AX, # 4444
        ADC     DX, # 4444      \ + 4/15
        MOV     AX, SI
        MUL     DX              \ * x
        ADD     AX, # 5555
        ADC     DX, # 5555      \ + 1/3, double, implied 1+
        MOV     DI, AX          \ Perform a 17 bit multiply.
        SHR     DI
        OR      BH, BH
        JNS     7 $
        ADD     DI, AX
7 $:    RCR     DI
        MOV     AX, BX          \ yhi
        MUL     DX              \ * y
        ADD     AX, DI
        ADC     DX, # 0
        XOR     DI, DI
        ADD     AX, CX
        ADC     DX, BX
        ADC     DI, # 0
        ADD     DX, # 5555
        ADC     DI, # 0055      \ + (1/3)*2^-8
        MOV     BP, DX
        CALL    Y**2
        CALL    FRACT*
        POP     AX              \ xlo
        ADD     AX, BX
        ADC     DX, SI          \ + x
        SHR     DX              \ Shift right by 2
        RCR     AX
        SHR     DX
        RCR     AX
        ADC     AX, # 0         \ Round
        ADC     DX, # 8000      \ + 1
        POP     SI              \ Restore SI and BP
        POP     BP
        MOV     BX, FSP
        MOV     4 [BX], AX      \ Push result
        MOV     2 [BX], DX
        MOV     CX, # 4000      \ Exponent of result.
        MOV     0 [BX], CX
        NEXT
        END-CODE

PREVIOUS DEFINITIONS

CODE FNORMALIZE   ( F: r1 -- r2 )
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     CX, 0 [BX]      \ SX1
        MOV     AX, 2 [BX]      \ FH1
        OR      AX, AX          \ Check if already normalized
        JS      3 $             \ If so, just return.
        JNZ     4 $             \ If high part non-zero, shift less than 16
        MOV     AX, 4 [BX]      \ FL1  Shift left by 16 by waving a hand.
        XOR     DX, DX
        OR      AX, AX          \ Check if argument is all zero.
        JZ      2 $
        SUB     CX, # 10        \ Reduce exponent to compensate.
        JO      1 $             \ Jump if we underflowed
        OR      AX, AX
        JS      7 $
        JMP     5 $

1 $:    XOR     AX, AX          \ Underflow case.  Set results to 0.
        MOV     4 [BX], AX
        MOV     2 [BX], AX
2 $:    MOV     0 [BX], AX
3 $:    NEXT

4 $:    MOV     DX, 4 [BX]
5 $:    OR      AH, AH
        JNZ     6 $
        SUB     CX, # 8
        MOV     AH, AL
        MOV     AL, DH
        XOR     DL, DL
        OR      AH, AH
        JS      7 $
6 $:    DEC     CX
        SHL     DX
        RCL     AX
        JNO     6 $
7 $:    MOV     4 [BX], DX
        MOV     2 [BX], AX
        MOV     0 [BX], CX
        NEXT

        END-CODE

CODE >INTFRACT   ( F: r1 -- ur2 ; -- int )   \ r2 may not be normalized.
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     AX, 0 [BX]      \ Sign + exponent
        MOV     DX, 4 [BX]      \ LS part
        MOV     BX, 2 [BX]      \ MS part
        XOR     DI, DI          \ Integer part
        MOV     CX, AX
        AND     CX, # 7FFF      \ Strip sign from exponent
        SUB     CX, # 3FFF      \ Calculate true exponent
        JLE     2 $             \ Jump if no shifting required
1 $:    SHL     DX              \ Left shift loop
        RCL     BX
        RCL     DI
        LOOP    1 $
        OR      AX, # 3FFF      \ Set resultant exponent to 3FFF
        AND     AX, # BFFF      \ But keep sign.
        JNS     2 $             \ Jump if positive sign
        NEG     DI              \ Otherwise negate integer part
2 $:    PUSH    DI
        MOV     CX, BX
        MOV     BX, FSP
        MOV     4 [BX], DX      \ Push fractional part of real
        MOV     2 [BX], CX
        OR      DX, CX
        JZ      3 $             \ Jump if fractional part is zero
        MOV     0 [BX], AX      \ Else push exponent and integer part
        NEXT                    \ And exit

3 $:    MOV     0 [BX], CX      \ If fractional part is zero, push 0 exponent
        NEXT
        END-CODE

: FLOOR   ( F: r -- ; -- n )
        >INTFRACT FPOP DUP
        IF    0<
              IF    2DROP 1-
              ELSE  2DROP
              THEN
        ELSE  DROP 2DROP
        THEN ;

: CEILING   ( F: r -- ; -- n )
        >INTFRACT FPOP DUP
        IF    0< 0=
              IF    2DROP 1+
              ELSE  2DROP
        THEN
        ELSE  DROP 2DROP
        THEN ;

ALSO HIDDEN DEFINITIONS

: GEXPTAB@   ( F: -- r ; n -- )
        6 * GEXPTAB + F@ ;

: AUX-    ( F: r1 -- r2 ; n -- )
        ?DUP
        IF   GEXPTAB@ FOVER F1.0+ F* F-   THEN ;

: AUX+    ( F: r1 -- r2 ; n -- )
        ?DUP
        IF   GEXPTAB@ FOVER FOVER F* F+ F+  THEN ;

: GEXP0   ( F: r1 -- r2 ; -- n )
        4 FSP @ +!
        >INTFRACT -4 FSP @ +!
        FNORMALIZE FDUP GEXP1 F* ;

: GEXPLN2   ( F: r1 -- r2 ; -- n )
        FNORMALIZE FLN2 F* GEXP0 ;

PREVIOUS DEFINITIONS

: FEXP    ( F: r1 -- r2 )
        [ ALSO HIDDEN ]
        FPSEXP 0<
        IF      FDUP FLN2 FNEGATE F<=
                IF      F/LN2 FPSEXP BFF2 <
                        IF      FDROP F0.0 EXIT   THEN
                        >INTFRACT GEXPLN2 AUX- F1.0+ FSP @ +!
                ELSE    GEXP0 AUX- F1.0+
                THEN
        ELSE    FDUP FLN2 F>=
                IF      F/LN2 FPSEXP 400D >
                        IF      FDROP -1 -1 7FFF FPUSH
                                [ LAST @ NAME> ] LITERAL 10 FPERR EXIT
                        ELSE    >INTFRACT GEXPLN2 AUX+ F1.0+ FSP @ +!
                        THEN
                ELSE    GEXP0 AUX+ F1.0+
                THEN
        THEN ;

PREVIOUS

: FALN   ( F: r1 -- r2 )   FEXP ;

: F**    ( F: r1 r2 -- r3 )
        FSWAP FLN F* FEXP ;

: FALOG   ( F: r1 -- r2 )
        FLN10.0 F* FEXP ;

ALSO HIDDEN DEFINITIONS

: F2**   ( F: -- r ; n -- )
        >R 0 8000 4000 R> + FPUSH ;

: F2**N-1   ( F: -- r ; n -- )
        F2** F1.0 F- ;

: FEXP-1    ( F: r1 -- r2 )                \ ( e^x - 1 )
        FPSEXP 0<
        IF      FDUP FLN2 FNEGATE F<=
                IF      F/LN2 FPSEXP BFF2 <
                        IF      FDROP F1.0 EXIT   THEN
                        >INTFRACT GEXPLN2 AUX- DUP FSP @ +!
                        F2**N-1 F+
                ELSE    GEXP0 AUX-
                THEN
        ELSE    FDUP FLN2 F>=
                IF      F/LN2 FPSEXP 400D >
                        IF      FDROP -1 -1 7FFF FPUSH
                                [ LAST @ NAME> ] LITERAL 10 FPERR EXIT
                        ELSE    >INTFRACT GEXPLN2
                                AUX+ DUP FSP @ +!
                                F2**N-1 F+
                        THEN
                ELSE    GEXP0 AUX+
                THEN
        THEN ;

: FSINH1   ( F: r1 -- r2 )
        FEXP-1 FDUP FDUP F1.0+ F/ F+ -1 FSP @ +! ;

: FTANH1   ( F: r1 -- r2 )
        1 FSP @ +!
        FEXP-1 FDUP 0 8000 4001 FPUSH F+ F/ ;

PREVIOUS DEFINITIONS

: FSINH    ( F: r1 -- r2 )
        [ ALSO HIDDEN ]
        FPSEXP 0<
        IF   FNEGATE FSINH1 FNEGATE
        ELSE FSINH1
        THEN ;
        PREVIOUS

: FCOSH   ( F: r1 -- r2 )
        FABS FEXP F1.0 FOVER F/ F+ -1 FSP @ +! ;

: FTANH   ( F: r1 -- r2 )
        [ ALSO HIDDEN ]
        FPSEXP 0<
        IF   FTANH1
        ELSE FNEGATE FTANH1 FNEGATE
        THEN ;
        PREVIOUS

HEX

F900 9502 4021 FPUSH FCONSTANT F1.0E10

\ : ISCALE    ( n ilg -- ilg len iscale )
\        SWAP DUP 0<
\        IF    NEGATE OVER 0 MAX +  THEN
\        0A MIN 1 MAX
\        2DUP SWAP - ;

: T#     ( t1 -- t2)     \ Output one digit of a triple number.
        0 BASE @ UM/MOD >R
        BASE @ UM/MOD >R
        BASE @ UM/MOD
        SWAP 0 # 2DROP R> R> ;

: TDUP0=   ( t -- t flag )
        2DUP OR 3 PICK OR 0= ;

: FPARTS    ( F: r -- ; n -- t exp sign )       \ t is a triple number.
        0A MIN 1 MAX                            \ Limit range of width.
        FDUP0=                                  \ Check for 0.0
        IF   FDROP DROP 0 0 0 0 0  EXIT THEN    \ Handle 0.0
        FPSEXP 0< 2* 1+ >R                      \ Save Sign.
        FABS FDUP FLOG FLOOR 1+                 \ Get output exponent.
        2DUP -                                  \ Scaling power
        F10.0 F**N*                             \ Scale the number
        F0.5 F+ FINT                            \ Round the result (add 0.5)
        SWAP FDUP F10.0 F**N F< 0=              \ Compare with 10^n
        IF   F10.0 F/ 1+   THEN                 \ If too large, divide by 10
        >R TINTA R> R> ;

DECIMAL
: (E.)   ( F: r -- ; n -- addr cnt )
        FPARTS                                  \ Separate into pieces.
        BASE @ >R >R                            \ Save current base, sign
        DUP -99 <                               \ Check for very small exp.
        IF  2DROP 2DROP 0 0 0 0  THEN           \ If small, use zero.
        DUP 99 >                                \ Check for large exponent.
        IF  BELL EMIT  THEN                     \ Error is just a bell!
        DECIMAL  <#                             \ Begin numeric formatting.
        DUP ABS 0 # #S 2DROP 0<                 \ Output exp.
        IF  ASCII -  ELSE ASCII +  THEN HOLD    \ Output sign of exp.
        ASCII E HOLD                            \ Output the "E".
        10 0 DO  T#  LOOP DROP                  \ Output remaining digits
        ASCII . HOLD                            \ Output the decimal point
        R> 0<                                   \ Check sign
        IF   ASCII -  ELSE   BL  THEN   HOLD    \ Output sign
        R> BASE ! #> ;                          \ Restore base and exit.

: E.   ( F: r -- )    10 (E.) TYPE SPACE ;

: E.R    ( F: r -- ; places width -- )
        SWAP 1 MAX 10 MIN SWAP
        OVER - SPACES (E.) TYPE ;

CREATE F#PLACES    10 ,

: PLACES   ( n -- )
        0 MAX 10 MIN F#PLACES ! ;

ALSO HIDDEN DEFINITIONS

: (F.FRACT)   ( F: r -- ; #pl -- )
        >R <# TINTA R> 1 MAX 0
        DO  T#  LOOP
        DROP 2DROP ASCII . HOLD ;

: (F.INT1)    ( F: r -- ; sign -- addr cnt )
        TINTA
        BEGIN DUP WHILE T# REPEAT DROP
        2DUP OR  IF #S THEN
        ROT  IF   ASCII - HOLD  THEN
        #> ;

: HOLDBL   ( n -- )
        DUP 0 >
        IF    0 DO  BL HOLD  LOOP
        ELSE  DROP  THEN ;

: (F.INT2)    ( F: r1 r2 --    ; sign width -- addr cnt 0 )
              ( F: r1 r2 -- r1 ; sign width -- addr cnt -1 )
        >R TINTA -1 0 R> 1-
        DO    DROP T# TDUP0=  IF I LEAVE  THEN  -1
        -1 +LOOP
        >R DROP 2DROP 0<  IF  ASCII - HOLD  THEN
        R> DUP 0<
        IF    0 #> -1
        ELSE  HOLDBL  0 0 #> 0 FDROP
        THEN ;

: (F.INTR)    ( F: r1 r2 --    ; width #pl sign -- addr cnt 0  )
              ( F: r1 r2 -- r1 ; width #pl sign -- addr cnt -1 ) \ Error case
        DUP >R - 1+ - R> SWAP DUP 0<            \ ( sign width' flag )
        IF     FDROP #> -1 EXIT  THEN
        ?DUP 0=
        IF    TINTA OR OR
              IF     0 #> -1 EXIT
              ELSE   0<  IF ASCII - HOLD  THEN
                     0 0 #> FDROP 0 EXIT
              THEN
        THEN  (F.INT2) ;

HEX

: (F.1)    ( F: r1 -- r2 r3 ; #pl -- sign #pl flag )
        FPSEXP 0< SWAP FABS FPSEXP 7F00 >
        IF      F0.0 FSWAP -1 EXIT  THEN
        F10.0 DUP
        F**N FSWAP FOVER F* F0.5 F+ FINT
        FSWAP F/PREM FPSEXP 4021 > ;

PREVIOUS DEFINITIONS

: F.     ( F: r -- )
        F#PLACES @ 1 MAX 0A MIN
        FDUP0=
        IF      FDROP F0.0  THEN
        [ ALSO HIDDEN ] (F.1)
        IF      DROP FNIP [ LAST @ NAME> ] LITERAL 40 FPERR
                IF   FNEGATE  THEN 0A (E.)
        ELSE    FSWAP (F.FRACT) (F.INT1)
        THEN    TYPE SPACE ;  PREVIOUS

: F.R    ( F: r -- ; #pl width -- )
        [ ALSO HIDDEN ] FDUP SWAP DUP (F.1)
        IF      DROP F2DROP [ LAST @ NAME> ] LITERAL 40 FPERR
                IF   FNEGATE  THEN 0A (E.)
        ELSE    FSWAP (F.FRACT)         \ ( F: r1 r2 -- ; width #pl sign )
                (F.INTR)
                IF    2DROP [ LAST @ NAME> ] LITERAL 40 FPERR 0A (E.)
                THEN  TYPE
        THEN ;  PREVIOUS

: ?FSTACK   ( F: -- )       \ Check Floating Point stack.
        (?STACK) FDEPTHB DUP 0<
        IF      FCLEAR DROP TRUE ABORT" Floating Point Stack Underflow"
        THEN
        [ ALSO HIDDEN ] FPSSIZE >=
        IF      FCLEAR TRUE ABORT" Floating Point Stack Overflow"
        THEN ;
        PREVIOUS

DECIMAL
: .FS   ( F: -- )
        ?FSTACK CR
        FDEPTHB DUP 0=
        IF      ." {0} "  DROP
        ELSE
              DUP 6 / <# 32 HOLD ASCII } HOLD 0 #S ASCII { HOLD #> TYPE
              1+ DUP 19 - 6 MAX
              DO      FSP0 I - F@ E.
              6 +LOOP
        THEN ;
HEX

: UNPACK   ( F: r -- ; -- d n )
        FDUP0=                                  \ Check for 0.0
        IF   FDROP 0 0 0 FPUSH EXIT  THEN       \ Handle 0.0
        FPSEXP FABS FDUP FLOG FLOOR 1+ >R       \ Save Sign, output exp.
        09 R@ - F10.0 F**N*                     \ Scale the number
        F0.5 F+ FINT                            \ Round the result
        FDUP 2800 EE6B 401D FPUSH ( 10^9 )
        F< 0=                                   \ Compare with 10^9
        IF   F10.0 F/ R> 1+                     \ If too large, divide by 10
        ELSE R>
        THEN
        SWAP 0<
        IF   FNEGATE  THEN
        >R INT R> 9 - ;

: FASINH   ( F: r1 -- r2 )
        FDUP FABS F1.0 F>
        IF      F1.0 FOVER F/ FDUP F* F1.0 F+ FSQRT FOVER F*
        ELSE    FDUP FDUP F* F1.0 F+ FSQRT
        THEN
        F+ FLN ;

: FACOSH   ( F: r1 -- r2 )
        FDUP FABS F1.0 F<
        IF      FDROP F0.0 [ LAST @ NAME> ] LITERAL 10 FPERR
        ELSE    F1.0 FOVER FDUP F* F- FSQRT F+ FLN
        THEN ;

: FATANH   ( F: r1 -- r2 )
        FDUP FABS F1.0 F<
        IF      F1.0 FOVER F+ FSWAP F1.0 F- F/ FLN 1 -
        ELSE
                FPSEXP FDROP FINFINITY 0<
                IF    FNEGATE  THEN
                [ LAST @ NAME> ] LITERAL 10 FPERR
        THEN ;

CODE UMT*   ( ut1 n -- ut2 )      \ Multiply a triple number by a single.
        POP     DI
        POP     CX
        POP     BX
        POP     AX
        MUL     DI
        PUSH    AX              \ Lowest part of result.
        MOV     AX, BX
        MOV     BX, DX
        MUL     DI
        ADD     BX, AX
        ADC     DX, # 0
        PUSH    BX              \ Middle part of product.
        MOV     BX, DX
        MOV     AX, CX
        MUL     DI
        ADD     AX, BX
        PUSH    AX              \ Most significant part of product.
        NEXT
        END-CODE

CODE TS+   ( ut1 n -- ut2 )
        POP     AX
        POP     BX
        POP     CX
        POP     DX
        ADD     DX, AX
        ADC     CX, # 0
        ADC     BX, # 0
        PUSH    DX
        PUSH    CX
        PUSH    BX
        NEXT
        END-CODE

: TCONVERT   ( ut1 adr1 -- ut2 adr2 )
        BEGIN   1+ DUP >R C@ BASE @ DIGIT
        WHILE
                OVER 1000 U<        \ Check for very large numbers
                IF
                        >R BASE @ UMT* R> TS+
                        DOUBLE?  IF DPL INCR  THEN
                ELSE    DROP
                THEN  R>
        REPEAT  DROP R> ;

CODE TFLOAT   ( F: -- r ; ut -- )   \ Float triple precision unsigned number
        CLEAR_LABELS
        POP     AX              \ MS part
        POP     BX              \ Mid part
        POP     DX              \ LS part
        MOV     CX, # 402F      \ Initial exponent.
        OR      AX, AX          \ See if we can shift 16 bits at a time.
        JNZ     1 $
        SUB     CX, # 10        \ Yes.  Adjust exponent.
        MOV     AX, BX          \ Shift left by 16 bits
        MOV     BX, DX
        XOR     DX, DX          \ Zero out LS part.
        OR      AX, AX          \ Can we shift another 16 bits?
        JNZ     1 $
        SUB     CX, # 10        \ Adjust exponent again
        MOV     AX, BX          \ Shift left another 16 bits
        XOR     BX, BX          \ Clear middle part
        OR      AX, AX          \ Check for initial value of 0.
        JZ      9 $
1 $:    OR      AH, AH          \ See if we can shift by 8 bits
        JNZ     2 $
        SUB     CX, # 8         \ Yes.
        MOV     AH, AL
        MOV     AL, BH
        MOV     BH, BL
        MOV     BL, DH
        MOV     DH, DL
        XOR     DL, DL
        OR      AH, AH          \ Test MS bit
2 $:    JS      4 $
3 $:    DEC     CX              \ MS bit not set, so shift left by 1
        SHL     DX, # 1
        RCL     BX, # 1
        RCL     AX, # 1
        JNO     3 $             \ Overflow indicates MS bit set.
4 $:    ADD     DX, # 8000      \ Begin rounding process
        JC      6 $             \ Jump if rounding needed.
5 $:    MOV     DI, BX
        MOV     BX, FSP
        SUB     BX, # 6
        MOV     FSP BX
        MOV     4 [BX], DI      \ Push result on floating point stack.
        MOV     2 [BX], AX
        MOV     0 [BX], CX
        NEXT

6 $:    JZ      8 $             \ Rounding.  Jump for round to even case.
        ADC     BX, # 0
        ADC     AX, # 0
        JNC     5 $             \ Jump if no carry out
7 $:    RCR     AX, # 1         \ Shift right
        RCR     BX, # 1
        INC     CX              \ Adjust exponent
        JMP     5 $

8 $:    ADC     BX, # 0
        ADC     AX, # 0
        JC      7 $
        AND     BX, # FFFE
        JMP     5 $

9 $:    MOV     BX, FSP
        SUB     BX, # 6
        MOV     FSP BX
        MOV     4 [BX], AX      \ Zero result
        MOV     2 [BX], AX
        MOV     0 [BX], AX
        NEXT
        END-CODE

VARIABLE FPT  0 FPT !

VARIABLE FLTS   0 FLTS !

: FLOATS   ( -- )               \ Allows decimal point to mean floating #.
        -1 FLTS ! ;

: DOUBLES   ( -- )              \ Decimal point in number means double.
        0 FLTS ! ;

: PACK   ( F: -- r ; d n -- )
\ Convert a double integer and power of 10 to a floating point number.
        >R FLOAT R> F10.0 F**N* ;

: -NUMBER   ( adr1 -- adr2 flag )
        DUP 1+ C@ ASCII - = DUP >R - R> ;

: 1FNUMBER   ( F: -- r ; ut adr -- -1 )   \ if convertable
                      \ or  ( ut adr -- 0 ) if not convertable.
        DPL @ FPT !  >R 0 0 R> -NUMBER  >R  DPL -1!  CONVERT
        DUP C@ BL <>
        IF      DUP C@ 29 = SWAP 1+ C@ BL = AND 0=
        ELSE    DROP 0
        THEN
        OR DOUBLE? OR
        FPT @ DPL !  FPT 0!
        IF      DROP R> DROP 2DROP DROP 0  EXIT  THEN
        R>  IF  NEGATE  THEN
        FPT -1!  DOUBLE?
        IF   DPL @ -  THEN
        >R TFLOAT BASE @ IFLOAT R> F**N* TRUE ;

\ ( F: -- r ; adr -- -1)   \ if convertable into floating point
\ (adr -- d 1 ) \ ( adr -- 0 )


: (FNUMBER?)  \ ( F: -- r ; adr -- 1)   \ if convertable into floating point
              \ ( adr -- d -1 ) / ( adr -- 0 )
        >R 0 0 0 R> -NUMBER >R
        DPL -1!  FPT 0!
        BEGIN   TCONVERT DUP C@ ASCII . =
        WHILE   DPL 0!
        REPEAT
        DUP C@ BL =
        IF      DROP DPL @ 0> FLTS @ AND
                IF      TFLOAT F10.0 DPL @ NEGATE F**N* R>
                        IF  FNEGATE THEN        1       \ TRUE  \ 07/06/89 TJZ
                ELSE    DROP R>
                        IF   DNEGATE  THEN      TRUE    \ 1     \ 07/06/89 TJZ
                THEN    EXIT
        THEN
        DUP C@ DUP 28 = SWAP 0DF AND 45 = OR
        IF      1FNUMBER
                IF      R>  IF   FNEGATE  THEN  1       \ TRUE  \ 07/06/89 TJZ
                ELSE    R> DROP                 FALSE
                THEN
        ELSE    R> 2DROP ( 2DROP ) drop         FALSE           \ 08/25/89 TJZ
        THEN ;

\ 07/06/89 19:47:10.34 TJZ REMOVED FOLLOWING DEFINITION
\ : (FNUMBER??)   ( A1 --- D1 )           \ Prefix with $ for auto HEX base.
\         +1=$?                     \ $ is for HEX
\         IF
\         ELSE    +1='?                   \ recognize ' for ascii
\                 IF      2+ C@ 0 TRUE
\                         DPL ON
\                 ELSE    (FNUMBER?)
\                 THEN
\         THEN    ;

: FNUMBER   ( F: -- r ; adr -- -1 )     \ if convertable.
                \ ( adr -- d 1 ) \ Error message
\        (FNUMBER??) DUP 0= ?MISSING  ; \ 07/06/89 19:47:26.60 TJZ CHANGE
                %NUMBER dup 0= ?MISSING ;

: F#   ( F: -- r ; fnumber --- )
        BL WORD FNUMBER 0> 0=   \ 0< 0=                         \ 07/06/89 TJZ
        IF      DUP 0<
                IF      DABS 0 TFLOAT FNEGATE
                ELSE    0 TFLOAT
                THEN
                DPL @ 0>
                IF      DPL @ NEGATE F10.0 F**N*
                THEN
        THEN ;

: #?    ( number --- val flag )
        BL WORD FNUMBER NEGATE ;        \  07/06/89 TJZ NEGATE ADDED

CODE (FLIT)   ( F: -- r )
        MOV     BX, FSP
        SUB     BX, # 6
        MOV     FSP BX
        LODSW   ES:
        MOV     0 [BX], AX
        LODSW   ES:
        MOV     2 [BX], AX
        LODSW   ES:
        MOV     4 [BX], AX
        NEXT
        END-CODE

: FLITERAL    ( F: r -- )
        COMPILE (FLIT) FPOP X, X, X, ;  IMMEDIATE

\ Add to Status line in Floating Point mode.

DECIMAL
ALSO HIDDEN DEFINITIONS

: <.FSTAT>   ( -- )
        #OUT @ #LINE @ >R >R  ATTRIB C@ >R  BASE @ >R
        DECIMAL 74 0 AT FDEPTHB 6 / DUP
        IF      >ATTRIB4 ."  {" (U.) TYPE ." }" 2 SPACES >ATTRIB1
        ELSE    >ATTRIB1 DROP ."  {0}  "
        THEN
        R> BASE ! R> ATTRIB C! R> R> AT ;

PREVIOUS DEFINITIONS

: .FSTATUS   ( -- )
        [ HIDDEN ALSO ]
        DEFERS STATUS
        ?STACK STATV @
        IF   <.FSTAT>   THEN ;
PREVIOUS
HEX

: FINTERP       ( -- )
        BEGIN   ?FSTACK  DEFINED
                IF      EXECUTE
                ELSE    FNUMBER -1 =    \ 1 =                   \ 07/06/89 TJZ
                        IF      DOUBLE? NOT IF DROP THEN  THEN
                THEN    FALSE DONE?
        UNTIL   ;

: (F])          ( -- )
        STATE ON
        BEGIN   ?FSTACK DEFINED DUP
                IF      0> IF  EXECUTE  ELSE  X,  THEN
                ELSE    DROP FNUMBER -1 = \ 1 =                 \ 07/06/89 TJZ
                        IF      DOUBLE?
                                IF   [COMPILE] DLITERAL
                                ELSE DROP [COMPILE] LITERAL
                                THEN
                        ELSE    [COMPILE] FLITERAL
                        THEN
                THEN
                TRUE DONE?
        UNTIL ;

ALSO HIDDEN DEFINITIONS

DEFER OLD-INTERP
DEFER OLD-]
DEFER OLD-?STACK
DEFER OLD-STATUS
DEFER OLD-LOADSTAT
DEFER OLD-#NUM          \ 07/06/89 19:46:21.35 TJZ ADDED

PREVIOUS DEFINITIONS

: FLOATING      ( -- )
        [ ALSO HIDDEN ] \ 07/06/89 19:46:32.77 TJZ ADDED FOLLOWING TWO LINES
        @> #NUM      IS OLD-#NUM           ['] (FNUMBER?) IS #NUM
        @> INTERPRET IS OLD-INTERP         ['] FINTERP    IS INTERPRET
        @> ]         IS OLD-]              ['] (F])       IS ]
        @> ?STACK    IS OLD-?STACK         ['] ?FSTACK    IS ?STACK
        @> STATUS    IS OLD-STATUS         ['] .FSTATUS   IS STATUS
        @> LOADSTAT  IS OLD-LOADSTAT       ['] <.FSTAT>   IS LOADSTAT ;
        PREVIOUS

: NOFLOATING    ( -- )
        [ ALSO HIDDEN ] \ 07/06/89 19:46:46.23 TJZ ADDED FOLLOWING TWO LINES
        @> OLD-#NUM     IS #NUM
        @> OLD-INTERP   IS INTERPRET
        @> OLD-]        IS ]
        @> OLD-?STACK   IS ?STACK
        @> OLD-STATUS   IS STATUS
        @> OLD-LOADSTAT IS LOADSTAT ;
        PREVIOUS

ALSO HIDDEN DEFINITIONS
CODE AUXTAN  ( F: r1 -- ur2 )       \ ur2 is tan(x) - x , |x| < 2^-4
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     CX, 0 [BX]      \ Sign and biased exponent.
        MOV     DX, 4 [BX]      \ LS part of r1
        MOV     BX, 2 [BX]      \ MS part of r1
        MOV     DI, CX          \ Save sign
        AND     DI, # 8000
        ADD     DI, # 3FF2      \ Generate Sign and Exponent of result.
        AND     CX, # 7FFF      \ Mask off sign bit
        SUB     CX, # 3FFB      \ Get unbiased exponent + 4
        PUSH    BP
        PUSH    SI
        PUSH    DI
        CMP     CX, # -8
        JG      3 $
        CMP     CX, # -10
        JG      2 $
        XOR     CX, CX
        ADD     SP, # 6         \ Don't bother popping the stack.
        MOV     BX, FSP
        MOV     4 [BX], CX
        MOV     2 [BX], CX
        MOV     0 [BX], CX
        NEXT

2 $:    MOV     AH, DL          \ Shift right by 8
        MOV     DL, DH
        MOV     DH, BL
        MOV     BL, BH
        XOR     BH, BH
        ADD     CX, # 8
        JNZ     3 $             \ Jump if we need more shifting
        ADD     AH, # 80
        JMP     5 $

3 $:    OR      CX, CX
        JZ      6 $
        NEG     CX
4 $:    SHR     BX
        RCR     DX
        LOOP    4 $
5 $:    ADC     DX, # 0         \ Round
        ADC     BX, # 0
6 $:    MOV     CX, DX          \ y = x*(2^4) in (BX,CX)
        MOV     SI, BX
        PUSH    DX              \ y in (SI,(SP))
        CALL    Y**2            \ y^2 in (BX,CX)
        MOV     AL, # 37        \ 68/315
        MUL     BH              \ (y^2)*(2^-8)*(68/315)
        MOV     AL, AH
        XOR     AH, AH
        ADD     AX, # 8889      \ + (8/15)
        MUL     BX              \ * (y^2)
        MOV     AL, AH          \ Shift right by 8
        MOV     AH, DL
        MOV     DL, DH
        XOR     DH, DH
        SHR     DX              \ /2
        RCR     AX
        ADD     AX, # AAAB      \ + (2/3)
        ADC     DX, # AAAA
        MOV     BP, AX
        MOV     DI, DX          \ Partial result in (DI,BP)
        CALL    FRACT*          \ *(y^2).  Result in (DX,BX)
        MOV     DI, DX
        MOV     BP, BX
        MOV     BX, SI
        POP     CX
        CALL    FRACT*          \ *y.  Result in (DX,BX)
        POP     AX              \ SX
        POP     SI
        POP     BP
        MOV     DI, BX
        MOV     BX, FSP
        MOV     4 [BX], DI
        MOV     2 [BX], DX
        MOV     0 [BX], AX
        NEXT
        END-CODE

CREATE TANTAB           \ Table of tan(i/16)  for i = 0 to 12.
        0000 , 0000 , 0000 ,   3FFC , 802A , BBC3 ,   3FFD , 80AB , BD79 ,
        3FFD , C248 , 3789 ,   3FFE , 82BC , 2D22 ,   3FFE , A56B , 8F6B ,
        3FFE , C989 , 6C2D ,   3FFE , EF7A , 4F55 ,   3FFF , 8BDA , 7AE0 ,
        3FFF , A164 , 5D07 ,   3FFF , B8B3 , 344F ,   3FFF , D236 , 595F ,
        3FFF , EE7D , 1B09 ,

: GTAN0   ( F: r1 -- r2 r3 )       \ The tangent is the ratio of r2 to r3.
        4 FSP @ +!
        >INTFRACT 6 * TANTAB + >R
        -4 FSP @ +!
        FNORMALIZE FDUP AUXTAN FNORMALIZE F+ FDUP R@ F@ F+
        FSWAP R> F@ F* F1.0 F\- ;

: GTAN1   ( F: r1 -- r2 r3 ; flag )
        PI -2 FSP @ +!  ( pi/4)
        F/PREM FPOP 401F =
        IF    DROP >R FNORMALIZE GTAN0 R@ 1 AND
              IF    FOVER FOVER R> 2 AND
                    IF    F- F-ROT F+
                    ELSE  F+ F-ROT F\-
                    THEN
              ELSE  R> 2 AND
                    IF    FNEGATE FSWAP  THEN
              THEN 0
        ELSE  2DROP -1
        THEN ;

: GTAN2   ( F: r1 -- r2 r3 ; -- flag1 flag2 )
        \ flag1 is non-zero if out of range.  flag2 is non-zero if r3 = 0.
        FDUP0<  ( FDUP F0< )
        IF      FNEGATE GTAN1 FNEGATE
        ELSE    GTAN1
        THEN
        FDUP0= ;

PREVIOUS DEFINITIONS

: FTAN   ( F: r1 -- r2 )
        [ ALSO HIDDEN ] GTAN2
        IF      FDROP F0<
                IF      -1 -1 C0FF ( Default -infinity )
                ELSE    -1 -1 40FF ( Default infinity )
                THEN    FPUSH
        ELSE    F/
        THEN
        IF      [ LAST @ NAME> ] LITERAL 10 FPERR
        THEN ;
        PREVIOUS

: FSIN   ( F: r1 -- r2 )
        [ ALSO HIDDEN ]
        -1 FSP @ +!  ( 2.0E0 F/ ) GTAN2
        IF      FDROP FDROP F0.0
        ELSE    F/ FDUP FDUP F* F1.0 F+ F/ 1 FSP @ +! ( 2.0E0 F* )
        THEN
        IF      [ LAST @ NAME> ] LITERAL 10 FPERR
        THEN ;
        PREVIOUS

: FCOS   ( F: r1 -- r2 )
        [ ALSO HIDDEN ]
        -1 FSP @ +! ( 2.0E0 F/ ) GTAN2
        IF      FDROP FDROP F1.0 FNEGATE
        ELSE    FOVER FOVER FOVER FOVER
                F\- F-ROT F+ F*
                F-ROT FDUP F* FSWAP FDUP F* F+ F/
        THEN
        IF      [ LAST @ NAME> ] LITERAL 10 FPERR
        THEN ;
        PREVIOUS DEFINITIONS

ALSO HIDDEN DEFINITIONS
CODE AUXATAN  ( F: r1 -- ur2 )     \ ur2 is x - arctan(x) , |x| < 2^-4
        CLEAR_LABELS
        MOV     BX, FSP
        MOV     CX, 0 [BX]      \ Sign and biased exponent.
        MOV     DX, 4 [BX]      \ LS part of r1
        MOV     BX, 2 [BX]      \ MS part of r1
        MOV     DI, CX          \ Save sign
        AND     DI, # 8000
        ADD     DI, # 3FF2      \ Generate Sign and Exponent of result.
        AND     CX, # 7FFF      \ Mask off sign bit
        SUB     CX, # 3FFB      \ Get unbiased exponent + 4
        PUSH    BP
        PUSH    SI
        PUSH    DI
        CMP     CX, # -8
        JG      3 $
        CMP     CX, # -10
        JG      2 $
        XOR     CX, CX
        ADD     SP, # 6         \ Don't bother popping the stack.
        MOV     BX, FSP
        MOV     4 [BX], CX
        MOV     2 [BX], CX
        MOV     0 [BX], CX
        NEXT

2 $:    MOV     AH, DL          \ Shift right by 8
        MOV     DL, DH
        MOV     DH, BL
        MOV     BL, BH
        XOR     BH, BH
        ADD     CX, # 8
        JNZ     3 $             \ Jump if we need more shifting
        ADD     AH, # 80
        JMP     5 $

3 $:    OR      CX, CX
        JZ      6 $
        NEG     CX
4 $:    SHR     BX
        RCR     DX
        LOOP    4 $
5 $:    ADC     DX, # 0         \ Round
        ADC     BX, # 0
6 $:    MOV     CX, DX          \ y = x*(2^4) in (BX,CX)
        MOV     SI, BX
        PUSH    DX              \ y in (SI,(SP))
        CALL    Y**2            \ y^2 in (BX,CX)
        MOV     AL, # 92        \ 4/7
        MUL     BH              \ (y^2)*(2^-8)*(4/7)
        MOV     AL, AH
        XOR     AH, AH
        NEG     AX
        ADD     AX, # CCCD      \ + (4/5)
        MUL     BX              \ * (y^2)
        MOV     AL, AH          \ Shift right by 8
        MOV     AH, DL
        MOV     DL, DH
        XOR     DH, DH
        SHR     DX              \ /2
        RCR     AX
        ADC     AX, # 0         \ Round
        ADC     DX, # 0
        NEG     DX              \ Negate
        NEG     AX
        SBB     DX, # 0
        ADD     AX, # AAAB      \ + (2/3)
        ADC     DX, # AAAA
        MOV     BP, AX
        MOV     DI, DX          \ Partial result in (DI,BP)
        CALL    FRACT*          \ *(y^2).  Result in (DX,BX)
        MOV     DI, DX
        MOV     BP, BX
        MOV     BX, SI
        POP     CX
        CALL    FRACT*          \ *y.  Result in (DX,BX)
        POP     AX              \ SX
        POP     SI
        POP     BP
        MOV     DI, BX
        MOV     BX, FSP
        MOV     4 [BX], DI
        MOV     2 [BX], DX
        MOV     0 [BX], AX
        NEXT
        END-CODE

\ arctangent table
CREATE ATANTAB          \ Table of arctan (k/16), for k = 0 to 16.
     0000 , 0000 , 0000 ,    3FFB , FFAA , DDB9 ,    3FFC , FEAD , D4D5 ,
     3FFD , BDCB , DA5E ,    3FFD , FADB , AFC9 ,    3FFE , 9B13 , B9B8 ,
     3FFE , B7B0 , CA0F ,    3FFE , D327 , 761E ,    3FFE , ED63 , 382B ,
     3FFF , 832B , F4A7 ,    3FFF , 8F00 , 5D5F ,    3FFF , 9A2F , 80E6 ,
     3FFF , A4BC , 7D19 ,    3FFF , AEAC , 4C39 ,    3FFF , B805 , 3E2C ,
     3FFF , C0CE , 85B9 ,    3FFF , C90F , DAA2 ,

: GATAN1   ( F: r1 -- r2 )         \ 0 <= r1 <= 1.0
        FDUP 4 FSP @ +! >INTFRACT DUP
        IF    >R -4 FSP @ +!
              FNORMALIZE FSWAP R@ IFLOAT -4 FSP @ +!
              F* F1.0 F+ F/ FDUP AUXATAN FNORMALIZE F- R>
              6 * ATANTAB + F@ F+
        ELSE  DROP -4 FSP @ +!
              FNORMALIZE FDUP AUXATAN FNORMALIZE F- FNIP
        THEN ;

: GATAN2   ( F: r1 -- r2 )         \ 0 <= r1
        FPSEXP 4000 <
        IF      GATAN1
        ELSE    F1.0 FSWAP F/ GATAN1 FNEGATE PI -1 FSP @ +! ( F2.0 F/ ) F+
        THEN ;

PREVIOUS DEFINITIONS

: FATAN   ( F: r1 -- r2 )          \ arctangent routine
        [ ALSO HIDDEN ]
        FDUP0<
        IF      FNEGATE GATAN2 FNEGATE
        ELSE    GATAN2
        THEN ;
        PREVIOUS

: FASIN   ( F: r1 -- r2 )          \ arcsine routine
        FDUP FABS F1.0 F>
        IF      [ LAST @ NAME> ] LITERAL 10 FPERR
        ELSE    FDUP F1.0 F+ FOVER FNEGATE F1.0 F+ F* FSQRT F/
                FATAN
        THEN ;

: FACOS   ( F: r1 -- r2 )          \ arccosine routine
        FDUP FABS F1.0 F>
        IF      [ LAST @ NAME> ] LITERAL 10 FPERR
        ELSE    F1.0 FOVER F- FSWAP F1.0 F+ F/ FSQRT FATAN 1 FSP @ +!
        THEN ;

: FTRIG0   ( F: r1 -- r2 )         \ SQRT ( ABS ( 1 - X^2 ))
        FDUP F* F- FABS FSQRT ;

FLOATING
DECIMAL


