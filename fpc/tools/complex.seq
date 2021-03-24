\ Complex Floating Point Routines   by Mark Smiley
\ Corrections and additions by R. L. Smith
\ Complex numbers are entered as ordered floating point pairs
\ with the real part second on the f.p. stack, and the
\ imaginary part at the top.  For magnitude and angle, the
\ magnitude is second on the stack and the angle is at the
\ top of the stack.

ANEW COMPLEX
FLOATS
HEX

: ZDROP   ( Z: z -- )
        FDROP FDROP ;

: ZDUP   ( Z: z -- z z )
        FOVER FOVER ;

: ZSWAP   ( Z: z1 z2 -- z2 z1 )
        FSWAP 3 FNSWAP FSWAP 2 FNSWAP ;

: ZNIP   ( Z: z1 z2 -- z1 )
        FROT FNIP FROT FNIP ;

: ZNEGATE ( F: x y --  -x -y )  ( Z: z -- -z )
        FNEGATE FSWAP  FNEGATE FSWAP  ;

: ZCONJUGATE   ( Z: z1 -- z2 )
        FNEGATE ;

: ZJ*   ( Z: z1 -- z2 )  ( Multiply by j , the unit imaginary )
        FNEGATE FSWAP ;

: Z+  ( Z: z1 z2 -- z3 )  ( adds 2 complex numbers )
    FROT F+  F-ROT  F+  FSWAP  ;

: Z-  ( Z: z1 z2 -- z3 )  FROT FSWAP F- F-ROT F- FSWAP  ;

: Z*  ( Z: z1 z2 -- z3 )  ( Multiply 2 complex numbers )
        FOVER 3 FPICK F* FOVER 5 FPICK F* F+ FPOP
        FROT F* F-ROT F* F\- FPUSH ;

: Z/   ( Z: z1 z2 -- z3 )  ( Divide z1 by z2 )
\ Based on my (R. L. Smith) algorithm in ACM
( Vol.6, No. 8 , 1962 )
        FDUP0=
        IF      FDROP FDUP0=
                IF      [ LAST @ NAME> ] LITERAL 1 FPERR
                ELSE    FPCOPY F/ FSWAP FPUSH F/ FSWAP
                THEN
                EXIT
        THEN
        FPSEXP 7FFF AND FOVER FDUP0=
        IF      FDROP FNIP FPCOPY F/
                FSWAP FPUSH F/ FNEGATE DROP EXIT
        THEN
        FPSEXP 7FFF AND < 0=  ( Comp. magnitudes of exponents )
        IF       ( Real part of denom. < imag. part of denom. )
                FDROP ZJ* ZSWAP ZJ* ZSWAP FOVER
        THEN    ( Now imaginary part <= real part )
        FOVER FSWAP F/                         ( F: A B C D R )
        FPCOPY F* F+                           ( F: A B C+D*R )
        F-ROT FDUP 2 FPICK FPUSH FPCOPY F* F-
        F-ROT FPUSH F* F+ FROT FPCOPY F/    ( F: den qr B-A*R )
        FSWAP FPUSH F/ ;

: ZVARIABLE   CREATE  16 ALLOT  DOES>  ;

: Z!  ( F: r1 r2 -- ; addr -- )  FSWAP DUP F!  8 +  F!  ;

: Z@  ( F: -- r1 r2 ; addr -- )  DUP  F@  8 +  F@   ;

: ZMAG   ( F: r1 r2 -- r3 )
        FABS FDUP0=
        IF      FDROP FABS EXIT  THEN
        FPSEXP FSWAP FABS FDUP0=
        IF      DROP FDROP EXIT  THEN
        FPSEXP <
        IF      FSWAP  THEN
        FOVER F/ FDUP F* F1.0 F+ FSQRT F* ;

: TOPOLAR   ( F: x y -- r theta )
        F2DUP FATAN FPOP ZMAG FPUSH ;

: TOCART   ( F: r theta -- x y )
        F2DUP FSIN F* FPOP FCOS F* FPUSH ;

\ Complex Square Root Algorithm

: F^2  ( F: x -- x^2 )   FDUP  F*  ;
: F2/  ( F: x -- x/2 )   FPOP 1- FPUSH  ;
: F2*  ( F: r1 -- r2 )   FPOP 1+ FPUSH  ;

: ZSQRT  ( F: x y -- x1 y1 )
        FDUP0< FOVER FDUP0< FOVER ZMAG
        FROT FABS F+ F2/ FSQRT FSWAP FABS F2/
        FOVER F/
        IF      FSWAP   THEN
        IF      FNEGATE THEN ;

: -ZSQRT ( F: x y -- x1 y1 )   ZSQRT  ZNEGATE  ;

DECIMAL


