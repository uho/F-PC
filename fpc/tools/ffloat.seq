\    FFLOAT.SEQ   Faster Hardware Floating point for 8087
\    Enhancements by: Robert L. Smith
comment:
        Based on HFLOAT by Steve Pollack and Mark Smiley and others.
        Preliminary tests show a speed improvement by a factor of two!
        Note that the speed is quite close to the software version
        called SFLOAT (but SFLOAT has less precision).
        Please send bug reports to:

            Robert L. Smith
            2300 St. Francis Dr.
            Palo Alto, CA 94303

            Tel: (415) 856-9321


        Comments are especially welcome regarding compatibility among the
        Intel variants: 8087, 80287, 80387 ...
        Note that the value 8087NPU may be modified prior to loading this
        file.  If it is changed, some speed improvements may be noticed
        in the more recent Floating Point Numeric Processors.

        In comments below, NPU means the Intel Numeric Processing Unit,
        (i.e., 8087, etc., or one of the compatible units).
comment;

comment: Version 3.0  A number of changes and bug fixes have been made.
        Thanks to Richard M. Mayer for pointing out an error in FATAN,
        and for other changes relating to DEGREE mode.
        Errors were discovered in (3VLOAD).
        The hyperbolic functions have been added.
        Improvements have been made in the NPU debugging tools. (See FR. )
        F**N and related functions were taken from SFLOAT and improved.
        The stack depth status was copied from SFLOAT.
        Number conversion FNUMBER works somewhat differently:  If FLOATS
        is executed, and the current base is DECIMAL, then numeric
        strings with an EMBEDDED decimal point are treated as floating
        point numbers.  A numeric string terminated by a decimal point
        is a double number.  If FLOATS is not executed, then the numeric
        string must include an E to indicate a floating point string.
        The base must also be DECIMAL .

        ***** SPECIAL NOTE FOR DEBUGGING WITH THE NPU (80x*87) *****
            I have made modifications to CODEBUG.SEQ to allow serious
            debugging with the floating point unit at the assembly
            language level.  Some modifications have been made to FFLOAT
            to faciliated the changes in CODEBUG.  This may appear as a
            new version of CODEBUG or possibly a completely separate
            file (probable name: FLTBUG.SEQ ).  Other corrections and
            changes have been made to  FDIS.SEQ  to improve disassembly
            of floating point assembly instructions.

            I have also added a file named FLTBUG.SEQ .  This may be
            loaded to enhance the DEBUG or DBG feature so that the
            floating point stack may be viewed as desired while debugging
            high level code.  Both the NPU stack and the parameter stack
            may be view in HEX mode.  The number of significant digits
            in the NPU stack display may also be changed.

comment;

NEEDS FASSEM.SEQ

CR .( FFLOAT Version 3.0         10/26/92 )

comment:

These screens load the higher level 8087 support words.  The floating
point assembler must be loaded prior to these words.

Unless otherwise specified, real is in the Intel 8087 64-bit floating
point (REAL*8) format.

In this version, floating point numbers are stored on the 8087 internal
stack, with the overflow going to a separate external stack.

comment;

DEFER FPERR

ALSO FORTH DEFINITIONS

: 2/?   ( n1 -- n2 n3 )         \ n2 is n1 shifted right by 1.
                                \ n3 is least significant bit of n1 .
        DUP >R 2/ $7FFF AND R> 1 AND ;

CODE OR!   ( n addr -- )        \ Logical OR of contents at addr with n
        POP     BX
        POP     AX
        OR      0 [BX], AX
        NEXT
        END-CODE

CREATE FPSTAT 0 , 0 ,

: .FP.    ( -- )   ." Floating Point " ;

: .NAME   ( n -- )   >NAME .ID ;

: .NAMES   ( n1 n2 -- )   .NAME 2 SPACES SPACE 3 - .NAME CR ;

: (FPERR)  ( F: r -- r ; n1 n2 n3 --  )  \  n2 is CFA, n3 is error flag.
         \ n1 is a possible return address on the parameter stack.
        DUP FPSTAT OR!  CR  BELL FEMIT
 ( 1 )  2/? IF  DROP .FP. ." Division by zero in "         .NAMES EXIT THEN
 ( 2 )  2/? IF  DROP .FP. ." Overflow in "                 .NAMES EXIT THEN
 ( 4 )  2/? IF  DROP .FP. ." argument is negative for "    .NAMES EXIT THEN
 ( 8 )  2/? IF  DROP .FP. ." argument is zero for "        .NAMES EXIT THEN
( 10 )  2/? IF  DROP .FP. ." argument out of range for "   .NAMES EXIT THEN
( 20 )  2/? IF  DROP .FP. ." Overflow for Input in "       .NAMES EXIT THEN
( 40 )  2/? IF  DROP .FP. ." Overflow for Output in "      .NAMES EXIT THEN
( 80 )  2/? IF  DROP      ." Integer overflow for "        .NAMES EXIT THEN
( 100)  2/? IF  DROP .FP. ." Underflow in "                .NAMES EXIT THEN
( 200)  2/? IF  DROP .FP. ." argument invalid for "        .NAMES EXIT THEN
( 400)  2/? IF  DROP .FP. ." Underflow for Input in "      .NAMES EXIT THEN
( 800)  2/? IF  DROP .FP. ." Underflow for Ouput in "      .NAMES EXIT THEN
( 1000) 2/? IF  DROP .FP. ." results inaccurate for "      .NAMES EXIT THEN
( 2000) 2/? IF  DROP .FP. ." stack underflow for "         .NAMES EXIT THEN
( 4000) 2/? IF  DROP .FP. ." stack overflow for "          .NAMES EXIT THEN
        IF ." Unspecified Error " THEN
        DROP QUIT ;

' (FPERR) IS FPERR

CODE CLEARFP    ( -- )
        FCLEX
        NEXT
        END-CODE

64   CONSTANT FSTACK-SIZE

CREATE FSTACK  FSTACK-SIZE 1+ 8* ALLOT  0 , 0 , 0 , 0 ,

FSTACK FSTACK-SIZE 8 * + CONSTANT FSP0

CREATE FLOAT-WORK 10 ALLOT
VARIABLE FVBOS       \ Floating point Virtual Bottom of Stack
VARIABLE FVTOS       \ Floating point Virtual Top of Stack (toward lower mem)

: FCLEAR   ( -- )
         FSP0 FVBOS !  FSP0 FVTOS !  INITFP ;

FCLEAR

CODE FDROP   ( F: r -- )
        CLEAR_LABELS
        MOV     AX, FVTOS
        CMP     AX, FVBOS
        JAE     1 $             \ Jump if empty or underflow on virtual stack
        ADD     AX, # 8
        MOV     FVTOS AX        \ FVTOS + 8 -> FVTOS (drop from virtual stack)
        FSTP    REAL*8 ST(0)    \ drop from NPU
        NEXT
1 $:    JNE     2 $             \ FVTOS >= FVBOS case.  Jump if > .
        ADD     AX, # 8         \ FVTOS = FVBOS
        MOV     FVTOS AX        \ FVTOS + 8 -> FVTOS -> FVBOS
        MOV     FVBOS AX
        CMP     AX, # FSP0      \ Check for underflow
        JA      2 $
        NEXT
2 $:    FINIT                   \ FP Stack has underflowed
        FDISI
        WAIT
        MOV     AX, # FSP0
        MOV     FVBOS AX
        MOV     FVTOS AX
        PUSH    ES
        MOV     AX, 2 [RP]
        MOV     ES, AX
        MOV     BX, 0 [RP]
        SUB     BX, # 2
        MOV     AX, 0 [RP] ES:
        POP     ES
        PUSH    AX
        MOV     BX, # LAST @ NAME>
        PUSH    BX
        MOV     AX, # $2000
        PUSH    AX
        MOV     AX, # ' FPERR
        JMP     AX
        END-CODE

GLOBAL_REF

LABEL (1VLOAD)             \ If NPU stack is empty, load 1 oprnd from mem.
        CLEAR_LABELS
        MOV     BX, FVBOS
        CMP     BX, FVTOS
        JE      1 $
        RET                             \ NPU is not empty, so return
1 $:    CMP     BX, # FSP0              \ NPU is empty.  Check underflow.
        JAE     2 $
        FLD     REAL*8 0 [BX]           \ VBOS = VTOS < FSP0 , so load NPU
        ADD     BX, # 8
        MOV     FVBOS BX                \ Indicate push on virtual stack
        WAIT
        RET
2 $:    POP     AX
        PUSH    ES
        MOV     AX, 2 [RP]
        MOV     ES, AX
        MOV     BX, 0 [RP]
        SUB     BX, # 2
        MOV     AX, 0 [RP] ES:
        POP     ES
        PUSH    AX
        MOV     AX, ES: -2 [SI]
        PUSH    AX
        MOV     AX, # $2000     \ Error: F.P. Stack Underflow
        PUSH    AX
        MOV     AX, # ' FPERR
        JMP     AX
        END-CODE

LABEL (2VLOAD)             \ Possible load from memory stack up to 2 opnds.
        MOV     BX, FVBOS
        MOV     AX, FVTOS
        CMP     BX, AX
        JE      3 $             \ Jump on empty virtual stack
        ADD     AX, # 8
        CMP     BX, AX
        JE      4 $             \ Jump if one element only on virtual and NPU
        RET

3 $:    CMP     BX, # FSP0 $10 -  \ Stack empty.  Are two elements available?
        JA      2 $               \ Jump if two are not available
        FLD     REAL*8 8 [BX]     \ Else load the top two elements
        FLD     REAL*8 0 [BX]
        ADD     BX, # $10         \ and increase the virtual stack.
        MOV     FVBOS BX
        WAIT
        RET

4 $:    CMP     BX, # FSP0 8 -    \ One stack item.  Can we free up one more?
        JA      2 $               \ Jump if we cannot.
        FINCSTP                   \ Bump TOS pointer
        FINCSTP                   \ Bump TOS pointer.
        FLD     REAL*8 0 [BX]     \ Load NPU (Push)
        FDECSTP                   \ Decrement TOS pointer
        ADD     BX, # 8
        MOV     FVBOS BX          \ Adjust virtual stack to match
        WAIT
        RET
        END-CODE

CODE 1VLOAD                     \ Insure at least on stack item on NPU
        CALL    (1VLOAD)
        NEXT
        END-CODE

LABEL (3VLOAD)                  \ Insure at least three real stack items
        CLEAR_LABELS
        MOV     BX, FVBOS
        MOV     AX, FVTOS
        ADD     AX, # $18
        CMP     BX, AX          \ Compare BOS with TOS+3
        JB      4 $
        RET                     \ Three or more items on the stack

4 $:    FINCSTP                 \ Less than three items on stack
        FINCSTP                 \ Adjust NPU stack pointer
        FINCSTP
        SUB     AX, # 8
        CMP     BX, AX          \ Compare BOS with TOS+2
        JE      1 $             \ If equal, go to $1 (2 items on stack)
        SUB     AX, # 8
        CMP     BX, AX          \ Compare BOS with TOS+1  \ 10/11/92 RLS
        JE      2 $             \ If equal, go to $2 (1 item on stack)
        CMP     BX, # FSP0 $18 + \ No items on NPU stack.  Check for underflow.
        JA      3 $
        FLD     $10 [BX]        \ We need to load 3 fp words from RAM
        FLD     8 [BX]
        FLD     0 [BX]
        ADD     WORD FVBOS # $18 \ Adjust virtual stack pointer
        WAIT
        RET

2 $:    CMP     BX, # FSP0 $10 + \ One item on NPU stack. Check UFLO.
        JA      3 $
        FLD     8 [BX]           \ Load two words from RAM
        FLD     0 [BX]
        FDECSTP                  \ Adjust NPU Stack Pointer
        ADD     WORD FVBOS # $10 \ Adjust virtual stack pointer
        WAIT
        RET

1 $:    CMP     BX, # FSP0 8 +   \ Two items on NPU stack.  Check UFLO
        JA      3 $
        FLD     0 [BX]           \ Load one item from RAM
        FDECSTP                  \ Re-adjust NPU Stack Pointer
        FDECSTP
        ADD     WORD FVBOS # 8   \ Adjust Virtual Stack Pointer
        WAIT                     \  10/11/92 RLS
        RET

3 $:    POP     AX
        PUSH    ES
        MOV     AX, 2 [RP]
        MOV     ES, AX
        MOV     BX, 0 [RP]
        SUB     BX, # 2
        MOV     AX, 0 [RP] ES:
        POP     ES
        PUSH    AX
        MOV     AX, ES: -2 [SI]   \ FP stack underflow case.
        PUSH    AX
        MOV     AX, # $2000
        PUSH    AX
        MOV     AX, # ' FPERR
        JMP     AX
        END-CODE

LABEL (1VEMPTY)                 \ Insure at least one empty position in NPU
        CLEAR_LABELS
1 $:    MOV     BX, FVTOS
        ADD     BX, # $40
        CMP     BX, FVBOS       \ Compare TOS+8 with BOS
        JE      2 $             \ If equal, we have a full stack
        RET                     \ Else return.

2 $:    CMP     BX, # FSTACK    \ Full NPU stack.  Check for overflow
        JB      4 $
        FDECSTP                 \ Move pointer to last NPU stack item
        SUB     BX, # 8         \ Adjust VBOS
        MOV     FVBOS BX
        FSTP    REAL*8 0 [BX]   \ Pop NPU stack item.
        WAIT
        RET

4 $:    POP     AX
        PUSH    ES
        MOV     AX, 2 [RP]
        MOV     ES, AX
        MOV     BX, 0 [RP]
        SUB     BX, # 2
        MOV     AX, 0 [RP] ES:
        POP     ES
        PUSH    AX
        MOV     AX, ES: -2 [SI]           \ FP Stack Overflow
        PUSH    AX
        MOV     AX, # $4000
        PUSH    AX
        MOV     AX, # ' FPERR
        JMP     AX
        END-CODE

LABEL (1VL1VE)                \ Equivalent to (1VLOAD) followed by (1VEMPTY)
        MOV     BX, FVBOS
        CMP     BX, FVTOS     \ Compare VBOS with VTOS
        JNE     1 $           \ If non-zero, we have at least 1. Check empty
        CMP     BX, # FSP0    \ Virtual stack empty.  Check for Underflow.
        JAE     3 $
        FLD     REAL*8 0 [BX] \ Load one item
        ADD     BX, # 8       \ and adjust VBOS
        MOV     FVBOS BX
        WAIT
        JMP     1 $

3 $:    POP     AX
        PUSH    ES
        MOV     AX, 2 [RP]
        MOV     ES, AX
        MOV     BX, 0 [RP]
        SUB     BX, # 2
        MOV     AX, 0 [RP] ES:
        POP     ES
        PUSH    AX
        MOV     AX, ES: -2 [SI]   \ FP Stack Underflow
        PUSH    AX
        MOV     AX, # $2000
        PUSH    AX
        MOV     AX, # ' FPERR
        JMP     AX
        END-CODE

LABEL (2VEMPTY)               \ Insure at least two empty stack items
        MOV     BX, FVTOS
        ADD     BX, # $40
        MOV     AX, FVBOS
        CMP     BX, AX        \ Compare VTOS+8 with VBOS
        JE      6 $           \ If equal, NPU is full.  Need to empty 2 items.
        SUB     BX, # 8
        CMP     BX, AX        \ Compare VTOS+7 with VBOS
        JE      5 $           \ If equal, need to empty one item.
        RET

5 $:    CMP     BX, # FSTACK 8 +  \ Check for Overflow
        JB      9 $
        FDECSTP               \ Move NPU stack pointer to last item
        FDECSTP
        SUB     BX, # 8       \ Adjust VBOS
        MOV     FVBOS BX
        FSTP    REAL*8 0 [BX] \ Move NPU item to RAM
        FINCSTP               \ Re-adjust NPU stack pointer
        RET

9 $:    JMP     4 $

6 $:    CMP     BX, # FSTACK 16 +  \ Empty 2 NPU items.  But check for Overflow.
        JB      9 $
        FDECSTP                 \ Adjust NPU stack pointer for 2 pops
        FDECSTP
        SUB     BX, # $10       \ Adjust VBOS
        MOV     FVBOS BX
        FSTP    0 [BX]          \ Move two items from NPU to RAM
        FSTP    8 [BX]
        WAIT
        RET
        END-CODE

LABEL (3VEMPTY)                 \ Insure at least three empty stack items
        MOV     BX, FVTOS
        ADD     BX, # $40
        MOV     AX, FVBOS
        CMP     BX, AX        \ Compare VTOS+8 with VBOS
        JE      7 $           \ If equal, NPU is full.  Need to empty 3 items.
        SUB     BX, # 8
        CMP     BX, AX        \ Compare VTOS+7 with VBOS
        JE      6 $           \ If equal, need to empty two items.
        SUB     BX, # 8
        CMP     BX, AX        \ Compare VTOS+6 with VBOS
        JE      5 $           \ If equal, need to empty one item.
        RET                   \ Else there's enough room.

7 $:    CMP     BX, # FSTACK $18 + \ Empty 3 NPU items.  Check for Overflow.
        JB      8 $
        FDECSTP               \ Adjust NPU stack pointer for 3 pops
        FDECSTP
        FDECSTP
        SUB     BX, # $18     \ Adjust VBOS
        MOV     FVBOS BX
        FSTP    0 [BX]        \ Move three items from NPU to RAM
        FSTP    8 [BX]
        FSTP    16 [BX]
        WAIT
        RET
8 $:    JMP     4 $
        END-CODE

LOCAL_REF

CODE F!   ( F: r -- ; addr -- )
        CALL    (1VLOAD)
        POP     BX
        FSTP    REAL*8 0 [BX]
        ADD     FVTOS # 8 WORD
        WAIT
        NEXT
        END-CODE

CODE F@    ( F: -- r ; addr -- )
        CALL    (1VEMPTY)
        POP     BX
        FLD     REAL*8 0 [BX]
        SUB     FVTOS # 8 WORD
        WAIT
        NEXT
        END-CODE

: FCONSTANT ( F: r -- ) ( compiling)
            ( F: -- r ) ( run-time )
        CREATE HERE 8 ALLOT F!
        DOES>  F@ ;

: FVARIABLE ( -- )      ( compiling)
            ( -- addr ) ( run-time )
        CREATE 8 ALLOT
        DOES> ;

CODE FP>DI    ( F: r -- ; -- 32b )
        SUB     SP, # 4
        MOV     BX, SP
        FRNDINT
        FSTP    INTEGER*4 0 [BX]
        WAIT
        ADD     WORD FVTOS # 8
        NEXT
        END-CODE

CODE FP>QI    ( F: r -- ; -- 64b)
        CALL    (1VLOAD)
        SUB     SP, # 8
        MOV     BX, SP
        FRNDINT
        FSTP    INTEGER*8 0 [BX]
        WAIT
        ADD     WORD FVTOS # 8
        NEXT
        END-CODE

CODE QI>FP    ( F: -- r ; 64b -- )
        CALL    (1VEMPTY)
        MOV     BX, SP
        FLD     INTEGER*8 0 [BX]
        WAIT
        ADD     SP, # 8
        SUB     WORD FVTOS # 8
        NEXT
        END-CODE

CODE FEXAM    ( F: r -- r ; -- n )
        CLEAR_LABELS
        MOV     BX, FVBOS
        CMP     BX, # FSP0
        JAE     1 $
        CALL    (1VLOAD)
1 $:    FXAM
        SUB     SP, # 2
        MOV     BX, SP
        FSTSW   0 [BX]
        WAIT
        AND     0 [BX], # $4700 WORD
        NEXT
        END-CODE

CODE PI    ( F: -- pi )
        CALL    (1VEMPTY)
        FLDPI
        SUB     FVTOS # 8 WORD
        NEXT
        END-CODE

CODE F1.0    ( F: -- 1.0 )
        CALL    (1VEMPTY)
        FLD1
        SUB     FVTOS # 8 WORD
        NEXT
        END-CODE

CODE F0.0    ( F: -- 0.0 )
        CALL    (1VEMPTY)
        FLDZ
        SUB     FVTOS # 8 WORD
        NEXT
        END-CODE

CODE F*   ( F: r1 r2 -- r1*r2)
        CALL    (2VLOAD)
        FMULP   ST(1), ST
        ADD     FVTOS # 8 WORD
        NEXT
        END-CODE


CODE F+    ( F: r1 r2 -- r1+r2)
        CALL    (2VLOAD)
        FADDP   ST(1), ST
        ADD     FVTOS # 8 WORD
        NEXT
        END-CODE

CODE F-    ( F: r1 r2 -- r1-r2)
        CALL    (2VLOAD)
        FSUBRP  ST(1), ST(0)
        ADD     WORD FVTOS # 8
        NEXT
        END-CODE

CODE F\-    ( F: r1 r2 -- r2-r1)
        CALL    (2VLOAD)
        FSUBP   ST(1), ST(0)
        ADD     WORD FVTOS # 8
        NEXT
        END-CODE

CODE F/    ( F: r1 r2 -- r1/r2)
        CALL    (2VLOAD)
        FDIVRP  ST(1), ST(0)
        ADD     WORD FVTOS # 8
        NEXT
        END-CODE

CODE FABS    ( F: r1 -- |r1|)
        CALL    (1VLOAD)
        FABS,
        NEXT
        END-CODE

CODE FNEGATE    ( F: r1 -- -r1 )
        CALL    (1VLOAD)
        FCHS
        NEXT
        END-CODE

CODE FSQRT    ( F: r1 -- SQRT[r1])
        CALL    (1VLOAD)
        FSQRT,
        NEXT
        END-CODE

CODE FLOG    ( F: r1 -- LOG10[r1])
        CALL    (1VL1VE)
        FLDLG2
        FXCH    ST(1)
        FYL2X
        NEXT
        END-CODE

CODE FLN    ( F: r1 -- LN[r1])
        CALL    (1VL1VE)
        FLDLN2
        FXCH    ST(1)
        FYL2X
        NEXT
        END-CODE

CODE 1/F  ( F: r -- r^-1)
        CALL    (1VL1VE)
        FLD1
        FDIVP   ST(1), ST(0)
        NEXT
        END-CODE

CODE F2*   ( F: r1 -- r2 )
        CALL    (1VL1VE)
        FLD1
        FXCH    ST(1)
        FSCALE
        FSTP    ST(1)
        NEXT
        END-CODE

CODE F2/   ( F: r1 -- r2 )
        CALL    (1VL1VE)
        CALL    (1VEMPTY)
        FLD1
        FCHS
        FXCH    ST(1)
        FSCALE
        FSTP    ST(1)
        NEXT
        END-CODE

CODE F2**N*    ( F: r1 -- r2 ; n -- )
        CALL    (1VL1VE)
        MOV     BX, SP
        FLD     INTEGER*2 0 [BX]
        ADD     SP, # 2
        FXCH    ST(1)
        FSCALE
        FSTP    ST(1)
        NEXT
        END-CODE

CODE FLOAT    ( F: -- r ; d -- )
        CALL    (1VEMPTY)
        MOV     BX, SP
        MOV     AX, 0 [BX]
        MOV     CX, 2 [BX]
        MOV     2 [BX], AX
        MOV     0 [BX], CX
        FLD     INTEGER*4 0 [BX]
        ADD     SP, # 4
        SUB     FVTOS # 8 WORD
        WAIT
        NEXT
        END-CODE

: (ROUND)    ( F: r -- ; n -- d )
        FPCW> DUP >R  $F3FF AND OR >FPCW
        1VLOAD FP>DI SWAP R> >FPCW ;

: FIX   ( F: r -- ; -- d )  $0000 (ROUND) ;

: INT   ( F: r -- ; -- d )  $0C00 (ROUND) ;

: RND>+INF  ( F: r -- ; -- d )  $0800 (ROUND) ;

: RND>-INF  ( F: r -- ; -- d )  $0400 (ROUND) ;

CODE FDUP    ( F: r -- r r )
        CALL    (1VL1VE)
        FLD     ST
        SUB     FVTOS # 8 WORD
        NEXT
        END-CODE

CODE FOVER    ( F: r1 r2 -- r1 r2 r1 )
        CALL    (2VLOAD)
        CALL    (1VEMPTY)
        FLD     ST(1)
        SUB     FVTOS # 8 WORD
        NEXT
        END-CODE

CODE FSWAP   ( F: r1 r2 -- r2 r1 )
        CALL    (2VLOAD)
        FXCH    ST(1)
        NEXT
        END-CODE

CODE FNSWAP   ( F: rn rn-1 ... r1 r0 -- r0 rn-1 ... r1 rn ; n -- )
        CLEAR_LABELS
        CALL    (1VLOAD)
        POP     BX
        SHL     BX, 1
        JZ      10 $
        SHL     BX, 1
        SHL     BX, 1
        MOV     CX, FVTOS
        MOV     AX, FVBOS
        SUB     AX, CX
        CMP     BX, AX
        JA      8 $
        CMP     BX, # 4 8 *
        JA      6 $
        JB      2 $
        FXCH    ST(4)
10 $:   RET
2 $:    CMP     BX, # 2 8 *
        JB      1 $
        JA      3 $
        FXCH    ST(2)
        RET
1 $:    FXCH    ST(1)
        RET
3 $:    FXCH    ST(3)
        RET
6 $:    CMP     BX, # 6 8 *
        JB      5 $
        JA      7 $
        FXCH    ST(6)
        RET
5 $:    FXCH    ST(5)
        RET
7 $:    FXCH    ST(7)
        RET
8 $:    ADD     BX, CX
        FSTP    REAL*8 FLOAT-WORK
        FLD     REAL*8 0 [BX]
        MOV     BX, CX
        MOV     DI, # 7
9 $:    MOV     AL, FLOAT-WORK [DI]
        MOV     0 [BX+DI], AL
        DEC     DI
        JNS     9 $
        RET
        END-CODE

CODE FROT    ( F: r1 r2 r3 -- r2 r3 r1 )
        CALL    (3VLOAD)
        FXCH    ST(1)           \ r1 r3 r2
        FXCH    ST(2)           \ r2 r3 r1
        NEXT
        END-CODE

CODE F-ROT    ( F: r1 r2 r3 -- r3 r1 r2 )
        CALL    (3VLOAD)
        FXCH    ST(2)           \ r3 r2 r1
        FXCH    ST(1)           \ r3 r1 r2
        NEXT
        END-CODE

CODE FNIP    ( F: r1 r2 -- r2 )
        CALL    (2VLOAD)
        FXCH    ST(1)
        FSTP    ST(0)
        ADD     WORD FVTOS # 8
        NEXT
        END-CODE

CODE FTUCK    ( F: r1 r2 -- r2 r1 r2 )
        CALL    (2VLOAD)
        CALL    (1VEMPTY)
        FXCH    ST(1)            \ r2 r1
        FLD     ST(1)
        SUB     WORD FVTOS # 8
        NEXT
        END-CODE

CODE FPICK    ( F: rn ... r1 r0 -- rn ... r1 r0 rn ; n -- )
        CLEAR_LABELS
        CALL    (1VEMPTY)
        POP     BX
        SHL     BX, 1
        SHL     BX, 1
        SHL     BX, 1
        MOV     CX, FVTOS
        MOV     AX, FVBOS
        SUB     WORD FVTOS # 8
        SUB     AX, CX
        CMP     BX, AX
        JAE     8 $
        CMP     BX, # 3 8 *
        JA      5 $
        JB      1 $
        FLD     ST(3)
        NEXT
1 $:    CMP     BX, # 1 8 *
        JB      0 $
        JA      2 $
        FLD     ST(1)
        NEXT
0 $:    FLD     ST(0)
        NEXT
2 $:    FLD     ST(2)
        NEXT
5 $:    CMP     BX, # 5 8 *
        JB      4 $
        JA      6 $
        FLD     ST(5)
        NEXT
4 $:    FLD     ST(4)
        NEXT
6 $:    FLD     ST(6)
        NEXT
8 $:    ADD     BX, CX
        FLD     REAL*8 0 [BX]
        NEXT
        END-CODE

CODE [RVS0]     ( F: r -- ; -- fpsw )
        CALL    (1VLOAD)
        FTST
        SUB     SP, # 2
        MOV     BX, SP
        FSTSW   0 [BX]
        FSTP    ST(0)
        ADD     WORD FVTOS # 8
        WAIT
        NEXT
        END-CODE

: C3C0X    ( fpsw -- n )
        DUP $04000 AND
        IF      2
        ELSE    0
        THEN
        SWAP $00100 AND
        IF      1+
        THEN ;

: F0=   ( F: r -- ; -- f )
        [RVS0] C3C0X 2 = ;

: FDUP0=   ( F: r -- r ; -- f )
        FDUP F0= ;

: F0<  ( F: r -- ; -- f)  [RVS0] C3C0X 1 = ;

: F0>  ( F: r -- ; -- f)  [RVS0] C3C0X 0= ;

CODE [RVSR]    ( F: r1 r2 -- ; -- fpsw )
        CALL    (2VLOAD)
        FXCH    ST(1)
        FCOMPP
        ADD     WORD FVTOS # $10
        SUB     SP, # 2
        MOV     BX, SP
        FSTSW   0 [BX]
        WAIT
        NEXT
        END-CODE

: F=    ( F: r1 r2 -- ; -- f )
        [RVSR] C3C0X 2 = ;

: F<    ( F: r1 r2 -- ; -- f )
        [RVSR] C3C0X 1 = ;

: F>    ( F: r1 r2 -- ; -- f )
        [RVSR] C3C0X 0=  ;

: FMIN   ( F: r1 r2 -- rmin )
        FOVER FOVER F<
        IF      FDROP
        ELSE
                FNIP
        THEN ;

: FMAX    ( F: r1 r2 -- rmax )
        FOVER FOVER F>
        IF      FDROP
        ELSE
                FNIP
        THEN ;

CODE (FLIT)   ( F: -- r )
        CALL    (1VEMPTY)
        FLD     REAL*8 ES: 0 [SI]
        SUB     WORD FVTOS # 8
        WAIT
        ADD     SI, # 8
        NEXT
        END-CODE

: FLITERAL  ( F: r -- )
        COMPILE (FLIT) FLOAT-WORK F!
        4 0 DO
                FLOAT-WORK I 2* +  @ X,
        LOOP
        ; IMMEDIATE

VARIABLE TRIG-MODE     TRIG-MODE OFF

: DEGREES    ( -- )
        TRIG-MODE ON ;

: RADIANS    ( -- )
        TRIG-MODE OFF ;

      PI F2* FCONSTANT 2PI

  PI F2/ F2/ FCONSTANT PI/4

      PI F2/ FCONSTANT PI/2


: DEG->RAD   ( F: r1 -- r2 )
        [ 180. FLOAT ] FLITERAL F/
        PI F* ;

: RAD->DEG    ( F: r1 -- r2 )
        PI F/ [ 180. FLOAT ] FLITERAL F* ;

INITFP CLEARFP

CODE [SIN]    ( F: r -- sin<r> )
        CALL    (1VLOAD)          \ radian argument
        CALL    (2VEMPTY)
        FLD1                      \ Load F1.0
        FCHS
        FXCH    ST(1)
        FSCALE                    \ arg/2
        FSTP    ST(1)             \  12/27/90 10:45:40.59 RLS
        FPTAN                     \ Partial tangent -> y, x
        FXCH    ST(1)
        FDIVRP  ST(1), ST(0)      \ y/x
        FLD     ST(0)             \ dup
        FLD     ST(0)             \ dup
        FMULP   ST(1), ST(0)
        FLD1
        FADDP   ST(1), ST(0)      \ 1 + (y/x)**2
        FXCH    ST(1)
        FLD1
        FLD     ST(0)
        FADDP   ST(1), ST(0)      \ 2.0
        FMULP   ST(1), ST(0)      \ 2(y/x)
        FDIVP   ST(1), ST(0)      \ 2(y/x)/(1+(y/x)**2)
        NEXT
        END-CODE

CODE [COS]    ( F: r -- cos<r> )
        CALL    (1VLOAD)
        CALL    (2VEMPTY)
        FLD1
        FCHS
        FXCH    ST(1)
        FSCALE
        FSTP    ST(1)
        FPTAN
        FXCH    ST(1)
        FDIVRP  ST(1), ST(0)
        FLD     ST(0)
        FMULP   ST(1), ST(0)
        FLD     ST(0)
        FLD1
        FADDP   ST(1), ST(0)
        FXCH    ST(1)
        FLD1
        FSUBRP  ST(1), ST(0)
        FDIVP   ST(1), ST(0)
        NEXT
        END-CODE

CODE [TAN]    ( F: r -- tan<r> )
        CALL    (1VL1VE)
        FPTAN
        FXCH    ST(1)
        FDIVP   ST(1), ST(0)
        NEXT
        END-CODE

: ?DEG->RAD   ( F: r1 -- r2 )
        TRIG-MODE @
        IF      DEG->RAD   THEN ;

: ?RAD->DEG   ( F: r1 -- r2 )
        TRIG-MODE @
        IF      RAD->DEG   THEN ;

F1.0 -53 F2**N* FCONSTANT SMALL-ANGLE

: FSIN1   ( F: r1 -- r2 )
        FDUP SMALL-ANGLE F>
        IF      [SIN]  THEN ;

: FCOS1   ( F: r1 -- r2 )
        FDUP SMALL-ANGLE F>
        IF      [COS]
        ELSE    FDROP F1.0
        THEN ;

: FSIN   ( F: r  -- SIN<r> )
        ?DEG->RAD FDUP F0< FABS
        FDUP PI/4 F/ INT 2DUP FLOAT PI/4 F* F- DROP
        DUP 2/ 2/ 1 AND NEGATE SWAP 3 AND
        DUP 0 = IF  DROP FSIN1                ELSE
        DUP 1 = IF  DROP PI/4 FSWAP F- FCOS1  ELSE
            2 = IF  FCOS1                     ELSE
                    PI/4 FSWAP F- FSIN1
        THEN THEN THEN 
        XOR IF  FNEGATE THEN ;

: FCOS ( F: r -- COS<r> )
        ?DEG->RAD
        FABS FDUP PI/4 F/ INT 2DUP FLOAT PI/4 F* F- DROP
        DUP 3 AND
        DUP 0 = IF  DROP FCOS1                ELSE
        DUP 1 = IF  DROP PI/4 FSWAP F- FSIN1  ELSE
            2 = IF  FSIN1                     ELSE
                    PI/4 FSWAP F- FCOS1
        THEN THEN THEN 
        2+ 2/ 2/ 1 AND
        IF FNEGATE THEN ;

F0.0 1/F FCONSTANT INFINITY

: FINFINITY=   ( F: r1 -- ; -- flag )
     1VLOAD FEXAM FDROP $0D00 AND $0500 = ;

FCLEAR

: FTAN1   ( F: r1 -- r2 )
        FDUP SMALL-ANGLE F>
        IF      [TAN]  THEN ;

: TANARG<>0 ( F: r -- TAN<r> ; n -- )
        [ FORTH ] 4 MOD
        DUP 0 = IF DROP FTAN1                          EXIT THEN
        DUP 1 = IF DROP PI/4 FSWAP F- FTAN1 1/F        EXIT THEN
        DUP 2 = IF DROP FTAN1 FNEGATE 1/F              EXIT THEN
        DUP 3 = IF DROP PI/4 FSWAP F- FTAN1 FNEGATE    EXIT THEN ;

: TANARG=0    ( F: -- TAN<r> ; n -- )
        [ FORTH ] 4 MOD
        DUP 0 = IF DROP F0.0           EXIT THEN
        DUP 1 = IF DROP F1.0           EXIT THEN
        DUP 2 = IF DROP INFINITY       EXIT THEN
        DUP 3 = IF DROP F1.0 FNEGATE   EXIT THEN ;

: FTAN    ( F: r -- TAN<r> )
        ?DEG->RAD FDUP F0< FABS
        FDUP PI/4 F/ INT 2DUP FLOAT PI/4 F* F- DROP 4 MOD
        FDUP F0=
        IF  FDROP TANARG=0
        ELSE  TANARG<>0  THEN
        IF FNEGATE THEN ;

ALSO ASSEMBLER ALSO

LABEL (POWER-1)    ( F: log2x y -- [x^y]-1 )    \ Takes 4 f.p. stack spaces
        CALL    (2VLOAD)
        CALL    (2VEMPTY)
        FMULP   ST(1), ST(0)      \ z * y  , where z = log2(x)
        FLD     ST(0)             \ DUP
        FSTCW   CWTEMP            \ Save current Control Word
        MOV     AX, CWTEMP
        MOV     CX, AX
        AND     AX, # $0F3FF
        OR      AX, # $00400      \ Round toward neg. inf.
        MOV     CWTEMP AX
        FLDCW   CWTEMP
        FRNDINT                   \ Take floor of z*y
        MOV     CWTEMP CX
        FLDCW   CWTEMP            \ Restore Control word.
        FST     REAL*8 CWTEMP     \ Save copy of floored value = I
        FXCH    ST(1)
        FSUBP   ST(1), ST(0)      \ (z*y) - floor(z*y) -> fract
        FLD1                      \ f 1
        FCHS                      \ f -1
        FXCH    ST(1)             \ -1 f
        FSCALE                    \ -1 f/2
        FSTP    ST(1)             \ f/2
        F2XM1                     \ 2^(fract/2) - 1
        FLD     ST(0)             \ dup
        FLD1                      \ (2^f/2 - 1) (2^f/2 - 1) 1
        FADDP   ST(1), ST(0)      \ (2^f/2 - 1) 2^f/2
        FLD1                      \ 3 items on FP stack
        FADDP   ST(1), ST(0)      \ (2^f/2 - 1) (2^f/2 + 1)
        FMULP   ST(1), ST(0)      \ 2^f - 1
        FLD     REAL*8 CWTEMP
        FXCH    ST(1)
        FSCALE                    \ 2^(z*y) - 2^I
        FXCH    ST(1)
        FLD1                      \ 3 items on FP stack
        FSCALE                    \ 2^I
        FLD1                      \ 4 items on FP stack
        FSUBRP  ST(1), ST(0)      \ 2^I - 1
        FSTP    ST(1)
        FADDP   ST(1), ST(0)
        ADD     WORD FVTOS # 8
        WAIT
        RET
        END-CODE

PREVIOUS PREVIOUS \ FORTH

CODE (FALN)    ( F: r -- e^r )
        CALL    (1VL1VE)
        FLDL2E
        CALL    (POWER)
        NEXT
        END-CODE

CODE [FALOG]    ( F: r -- 10^r )
        CALL    (1VL1VE)
        FLDL2T
        CALL    (POWER)
        NEXT
        END-CODE

: FEXP ( F: r -- e^r )
        FDUP 699. FLOAT  F>
        IF       [ LAST @ ] LITERAL NAME> DUP $10 FPERR
\                ." FALN ARGUMENT TOO LARGE" FDROP QUIT
        THEN
        (FALN) ;

: FALN   FEXP ;

: FALOG    ( F: r -- 10^r )
        FDUP  304. FLOAT  F>
        IF   ." FALOG ARGUMENT TOO LARGE" FDROP QUIT
        THEN
        [FALOG] ;

: FLOATDPL    ( F: -- r ; d -- )        \ Float a double, using DPL
        FLOAT DPL @ 0 FLOAT FALOG F/ ;

: F**    ( F: r1 r2 -- r1^r2 )
        FSWAP FLOG F* FALOG ;

CODE F**+N     ( F: r1 -- r2 ; n -- )
        CLEAR_LABELS
        CALL    (1VLOAD)
        CALL    (2VEMPTY)
        POP     AX
     0= IF      NEXT
        THEN
        FLD1
1 $:    SHR     AX, # 1
        JNC     2 $
        FMUL    ST(0), ST(1)
2 $:    JZ      3 $
        FXCH    ST(1)
        FMUL    ST(0), ST(0)
        FXCH    ST(1)
        JMP     1 $
3 $:    FXCH    ST(1)
        FSTP    ST(0)
        NEXT
        END-CODE

: F**N     ( F: r1 -- r2 ; n -- )  \ Floating number raised to integer power.
        DUP 0<
        IF      ABS F**+N F1.0 FSWAP F/
        ELSE    F**+N
        THEN ;

: F**N*    ( F: r1 r2 -- r3 ; n -- )    \ Raise r2 to nth power and
                                        \ and multiply by r1.
        DUP 0<
        IF      ABS F**+N F/
        ELSE    F**+N F*
        THEN ;

CREATE (PI/2)
        $18 C, $2D C, $44 C, $54 C, $FB C, $21 C, $F9 C, $3F C,

CREATE (PI/4)
        $18 C, $2D C, $44 C, $54 C, $FB C, $21 C, $E9 C, $3F C,

LABEL (FATAN)   ( F: z -- arctan )
        FLD1
        FCOM    ST(1)           \ Now compare argument against 1
        FSTSW   FLOAT-WORK
        WAIT
        MOV     AX, FLOAT-WORK
        AND     AH, # $45
     0= IF      \ 1 > z
                FPATAN
                RET
        THEN
                \ Check further
        CMP     AH, # 1
     0= IF      \ 1 < z
                FXCH  ST(1)
                FPATAN
                FLD1
                FLD   ST(0)
                FADDP ST(1), ST(0)
                FLDPI
                FDIVP ST(1), ST(0)
                FSUBP ST(1), ST(0)
                RET
        THEN
                \ Assume that z = 1
        FSTP    ST(0)                   \ Pop the 1
        FSTP    ST(0)                   \ Pop z
        FLD     REAL*8 (PI/4)           \ Load pi/4
        RET
        END-CODE

CODE FATANA    ( F: r -- arctan[r] )
        CALL    (1VL1VE)
        FXAM                    \ Only useful way to test for infinity
        FSTSW   FLOAT-WORK
        MOV     AX, FLOAT-WORK
        AND     AX, # $4700
        TEST    AH, # 1
    0<> IF      \ Special case
                CMP     AH, # 5
             0= IF      \ +infinity. Result is pi/2
                        FSTP    ST(0)
                        FLD     REAL*8 (PI/2)
                        NEXT
                THEN
                CMP     AH, # 7
             0= IF      \ -infinity.  Result is -pi/2
                        FSTP    ST(0)
                        FLD     REAL*8 (PI/2)
                        FCHS
                        NEXT
                THEN
                \ Something invalid
                SUB     BX, # 2
                MOV     AX, 0 [RP] ES:
                POP     ES
                PUSH    AX
                MOV     AX, ES: -2 [SI]
                PUSH    AX
                MOV     AX, # $200
                PUSH    AX
                MOV     AX, # ' FPERR
                JMP     AX
        THEN
        FTST                            \ Now test for other cases
        FSTSW   FLOAT-WORK
        WAIT
        MOV     AX, FLOAT-WORK
        AND     AX, # $4500
     0= IF                              \ Normal positive argument
                CALL  (FATAN)
                NEXT
        THEN
                        \ Check for negative and non-zero
        CMP     AH, # 1
     0= IF      \ Ordinary negative argument
                FCHS
                CALL  (FATAN)
                FCHS
                NEXT
        THEN
                        \ Argument must be zero
        NEXT            \ Just return +0 or -0
        END-CODE

: FATAN   ( F: r -- arctan[r] )
        FATANA ?RAD->DEG ;

: ARCRANGE    ( F: r -- r ; -- f )
        FDUP  F1.0  F>   FDUP  F1.0 FNEGATE F< OR ;

: FASIN    ( F: r -- arcsin[r] )
        ARCRANGE
        IF      FDROP ." INVALID FASIN ARGUMENT" QUIT
        ELSE
                FDUP  F0< FABS  F1.0  FOVER  FDUP  F*  F-  FSQRT
                F/  FATAN
                IF FNEGATE  THEN
        THEN ;

: FACOS    ( F: r -- arccos[r] )
        ARCRANGE
        IF      3 RPICK 2 RPICK 2- @L 3 + [ LAST @ ] LITERAL NAME> $10 FPERR
        ELSE    FDUP  F0<   FABS  F1.0  FOVER F- FSWAP
                F1.0 F+ F/ FSQRT FATANA FDUP F+
                IF      PI  FSWAP  F-
                THEN
                ?RAD->DEG
        THEN ;

CREATE (F2.0)     0 , 0 , 0 , $4000 ,   \ REAL*8

CODE (FTANH)   ( F: r -- tanh[r] )
        CALL    (1VLOAD)
        CALL    (2VEMPTY)
        FLD1
        FXCH    ST(1)
        FSCALE                     \ 2*(abs(r))
        FSTP    ST(1)              \ FNIP
        FLDL2E                     \ log to base 2 of e
        SUB     FVTOS [] # 8
        CALL    (POWER-1)          \ e**2r - 1
        FLD     ST                 \ FDUP
        FLD     REAL*8 (F2.0)      \ F2.0
        FADDP   ST(1), ST(0)       \ e**2r + 1
        FDIVRP  ST(1), ST(0)       \ tanh
        NEXT
        END-CODE

: FTANH   ( F: r -- tanh[r] )
        FDUP  699. FLOAT  F>
        IF   ." FTANH ARGUMENT TOO LARGE" FDROP QUIT
        THEN
        FDUP F0<
        IF      FABS FDUP 699. FLOAT F>
                IF      FDROP F1.0
                ELSE    (FTANH) FNEGATE
                THEN
        ELSE    (FTANH)
        THEN ;

CODE (FSINH)   ( F: r -- sinh[r] )
        CALL    (1VLOAD)
        CALL    (2VEMPTY)
        FLDL2E                  \ log to base 2 of e
        SUB     FVTOS [] # 8
        CALL    (POWER-1)       \ e**r - 1
        FLD     ST              \ FDUP
        FLD1                    \ 1.0
        FADDP   ST(1), ST(0)    \ e**r
        FLD     ST(1)           \ FOVER
        FDIVP   ST(1), ST(0)    \ F/
        FADDP   ST(1), ST(0)    \ F+
        FLD1                    \
        FCHS
        FXCH    ST(1)
        FSCALE                  \ r/2
        FSTP    ST(1)           \ FNIP
        NEXT
        END-CODE

: <FSINH>   ( F: r -- sinh[r] )
        FDUP 699. FLOAT  F>
        IF      ." FSINH ARGUMENT TOO LARGE IN MAGNITUDE " FDROP QUIT
        THEN
        (FSINH) ;

: FSINH   ( F: r -- sinh[r] )
        FDUP F0<
        IF      FABS (FSINH) FNEGATE
        ELSE    (FSINH)
        THEN ;

: FCOSH   ( F: r -- cosh[r] )
        FABS
        FDUP  699. FLOAT  F>
        IF      ." FCOSH ARGUMENT TOO LARGE IN MAGNITUDE " FDROP QUIT
        THEN
        FEXP F1.0 FOVER F/ F+ F2/ ;

CREATE F.25  0 , 0 , 0 , $0D0 C, $0FF C,

CODE (FASINH1)   ( F: r -- arcsinh[r] )
        CALL    (1VLOAD)
        CALL    (3VEMPTY)
        FLD     ST              \ x  x
        FLD     ST              \ x  x  x
        FMUL    ST(0), ST(1)    \ x  x^2
        FLD1                    \ x  x^2  1
        FADDP   ST(1), ST(0)    \ x  1 + x^2
        FSQRT,                  \ x  sqrt(1+x^2)
        FADDP   ST(1), ST(0)    \ x+sqrt(1+x^2) = y
        FLDLN2
        FXCH    ST(1)
        FYL2X                   \ ln(y) = asinh(x)
        NEXT
        END-CODE

F1.0 40 F2**N* FCONSTANT BIG-NUM

: (FASINH)   ( F: R -- arcsinh[r] )
        FDUP BIG-NUM F>
        IF      FDUP F+ FLN
        ELSE    (FASINH1)
        THEN ;

: FASINH   ( F: r -- arcsinh[r] )
        FDUP F0<
        IF      FABS (FASINH) FNEGATE
        ELSE    (FASINH)
        THEN ;

CODE (FACOSH)    ( F: r -- arccosh[r] )
        CALL    (1VLOAD)
        CALL    (2VEMPTY)
        FLD1                    \ r, 1
        FADD    ST(0), ST(1)    \ r, r+1
        FLD1                    \ r, r+1, 1
        FSUBR   ST(0), ST(2)    \ r, r+1, r-1
        FMULP   ST(1), ST(0)    \ r, (r**2)-1
        FSQRT,                  \ r, sqrt()
        FADDP   ST(1), ST(0)
        FLDLN2
        FXCH    ST(1)
        FYL2X
        NEXT
        END-CODE

: FACOSH   ( F: r -- arccosh[r] )
        FABS FDUP
        F1.0 F<
        IF      CR ." Invalid argument for FACOSH " FDROP QUIT
        THEN
        FDUP BIG-NUM F>
        IF      FDUP F+ FLN
        ELSE    (FACOSH)
        THEN ;

F1.0 F1.0 2 0 FLOAT FSQRT F/ F- FCONSTANT 1-SQRT1/2

LABEL (FATANH)   ( F: r -- arctanh[r] )
        FLD1                    \ r, 1
        FSUB    ST(0), ST(1)    \ r, 1-r
        FTST
        FSTSW   FLOAT-WORK
        WAIT
        MOV     AX, FLOAT-WORK
        SAHF
        U<= IF                          \ 1-r is <= 0 or invalid
                0<> IF                  \ 1-r < 0
                        POP     AX
                        PUSH    ES
                        MOV     AX, 2 [RP]
                        MOV     ES, AX
                        MOV     BX, 0 [RP]
                        SUB     BX, # 2
                        MOV     AX, 0 [RP] ES:
                        POP     ES
                        PUSH    AX
                        MOV     AX, ES: -2 [SI]
                        PUSH    AX
                        MOV     AX, # $10         \ Argument out of range
                        PUSH    AX
                        MOV     AX, # ' FPERR
                        JMP     AX
                ELSE                    \ 1-r = 0 or invalid
                        U< IF                   \ Invalid argument
                                SUB     BX, # 2
                                MOV     AX, 0 [RP] ES:
                                POP     ES
                                PUSH    AX
                                MOV     AX, ES: -2 [SI]
                                PUSH    AX
                                MOV     AX, # $200
                                PUSH    AX
                                MOV     AX, # ' FPERR
                                JMP     AX
                        ELSE            \ r = 1, result is infinity
                                FSTP    ST(0)
                                FSTP    ST(0)
                                MOV     BX, # ' INFINITY
                                FLD     REAL*8 0 [BX]
                                RET
                        THEN
                THEN
        THEN
        FXCH    ST(1)           \ 1-r, r
        FLD1                    \ 1-r, r, 1
        FXCH    ST(1)           \ 1-r, 1, r
        FSCALE                  \ 1-r, 1, 2r
        FSTP    ST(1)           \ 1-r, 2r
        FDIVP   ST(1), ST(0)    \ 2r/(1-r) = z
        FLDLN2                  \ z, ln2
        FXCH    ST(1)           \ ln2, z
\ Need to compare z with (1-1/sqrt(2)).  If less,use fyl2xp1, else add 1,
\  and use fyl2x ?
        FLD     REAL*8 ' 1-SQRT1/2 >BODY
        FCOMP   ST(1)
        FSTSW   FLOAT-WORK
        WAIT
        MOV     AX, FLOAT-WORK
        SAHF
        U<= IF                  \ 1-sqrt1/2 <= z
                FLD1
                FADDP   ST(1), ST(0)
                FYL2X
        ELSE
                FYL2XP1                 \ 2*arctanh
        THEN
        FLD1
        FCHS
        FXCH    ST(1)
        FSCALE
        FSTP    ST(1)
        RET
        END-CODE

CODE FATANH   ( F: r -- arctanh[r] )
        CALL    (1VLOAD)
        CALL    (2VEMPTY)
        FTST
        FSTSW   FLOAT-WORK
        WAIT
        MOV     AX, FLOAT-WORK
        SAHF
        U<=
        IF      FCHS
                CALL  (FATANH)
                FCHS
        ELSE
                CALL  (FATANH)
        THEN
        NEXT
        END-CODE

FALSE VALUE FP?

: FLOATS    ( -- )
        TRUE IS FP?   ;

: DOUBLES    ( -- )
        FALSE IS FP? ;

VARIABLE EXP?  EXP? OFF

VARIABLE FLOATING   FLOATING OFF

: FLOATING?
        FLOATING @ ;

: (FP-CHECK)    ( flg addr -- flg' addr )
        [ FORTH ] DUP C@ DUP ASCII e =
        IF      DROP ASCII E OVER C!  EXP? ON  EXIT
        THEN
        DUP ASCII 0 ASCII 9 BETWEEN
        IF      DOUBLE? EXP? @ 0= AND
                IF      DPL INCR
                THEN
                DROP EXIT
        THEN
        DUP ASCII E =
        IF      DROP EXP? ON      EXIT
        THEN
        DUP ASCII - =
        IF      DROP EXIT
        THEN
        DUP ASCII + =
        IF      DROP EXIT
        THEN
        ASCII . =
        IF      DPL INCR EXIT
        THEN
        NIP 0 SWAP ;

: FP-CHECK    ( addr -- addr f )
        EXP? OFF DPL -1! DUP TRUE SWAP COUNT BOUNDS
        DO
                I (FP-CHECK) DROP
        LOOP ;

CREATE (I10)  10 ,

CODE F10.0    ( F: -- r2 )
        CALL    (1VEMPTY)
        FLD     INTEGER*2 (I10)
        SUB     WORD FVTOS # 10
        NEXT
        END-CODE

CODE FMUL10    ( F: r1 -- r2 )
        CALL    (1VL1VE)
        FLD     INTEGER*2 (I10)
        FMULP   ST(1), ST(0)
        NEXT
        END-CODE

CODE (FADDI)    ( F: r1 -- r2 ; n -- )
        CALL    (1VL1VE)
        MOV     BX, SP
        FLD     INTEGER*2 0 [BX]
        FADDP   ST(1), ST(0)
        ADD     SP, # 2
        NEXT
        END-CODE

: QCONVERT    ( +q1 adr1 -- +q2 adr2 )
        >R QI>FP R>
        BEGIN
                1+ DUP >R C@ 10 DIGIT
        WHILE
                FMUL10  (FADDI) DOUBLE? IF 1 DPL +! THEN R>
        REPEAT
        DROP FP>QI R> ;

CODE QNEGATE    ( +q -- -q )
        MOV     BX, SP
        FLD     0 [BX] INTEGER*8
        FCHS
        FSTP    0 [BX]
        NEXT
        END-CODE

: QFLOAT    ( F: -- r ; q -- )
        DPL @ 0 MAX DPL !
        QI>FP ( FP>R ) DPL @ S>D FLOAT FALOG F/ ;

: (MANTISSA)   ( F: -- r ; addr1 -- addr2 )
        DUP 1+ C@ ASCII + = ?MISSING            ( lead "+" invalid)
        DUP 1+ C@ ASCII - = DUP >R IF 1+ THEN   ( check for lead "-")
        -1 DPL ! >R 0 0 0 0 R>
        BEGIN
                QCONVERT DUP C@ ASCII . =       ( convert till "E" )
        WHILE
                0 DPL !                         ( reset DPL at "." )
        REPEAT
        R> SWAP >R
        IF QNEGATE
        THEN
        QFLOAT R> ;                             ( set sign and float )

: (EXP)    ( addr -- d )
        DUP C@ BL =
        IF      DROP 0 0 EXIT                   ( space is a delimiter )
        THEN
        1+ DUP C@ ASCII + =
        IF 1+ THEN                              ( bypass "+" if present)
        DUP C@ ASCII - = DUP >R
        IF 1+ THEN                              ( check for "-")
        0 DPL ! 0 0 ROT 1- CONVERT DROP         ( convert it )
        2DUP 308. DU< 0= ?MISSING R>
        IF DNEGATE THEN 0 DPL ! ;

: FNUMBER    ( addr -- r | n | d | ; )
        [ FORTH ] FLOATING OFF FP-CHECK BASE @ 10 = AND
        IF      EXP? @ FP? DPL @ 0 > AND OR
                IF      (MANTISSA) (EXP) FLOAT FALOG F*
                        DPL OFF FLOATING ON EXIT
                THEN
        THEN
        (NUMBER) ;

' FNUMBER IS NUMBER

: F]    ( -- )
        STATE ON
        BEGIN
                ?STACK  DEFINED DUP
                IF    0>
                        IF   EXECUTE
                        ELSE  X,
                        THEN
                ELSE
                        DROP  NUMBER  FLOATING?
                        IF
                                [COMPILE] FLITERAL ELSE DOUBLE?
                                IF
                                        [COMPILE] DLITERAL
                                ELSE
                                        DROP [COMPILE] LITERAL
                                THEN
                        THEN
                THEN
                TRUE  DONE?
        UNTIL ;

' F] IS ]

: FMAG   ( F: r -- r ; -- n )
        FDUP FABS FLOG RND>-INF DROP ;

CREATE FLOAT-BCD 10 ALLOT

VARIABLE #BCD   17 #BCD !

CODE R>BCD!    ( F: r -- ; n -- ; full precision bcd-string to FLOAT-BCD )
       CALL     (1VLOAD)
       CALL     (2VEMPTY)
       MOV      AX, #BCD
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
       FSTP     FLOAT-BCD BCD
       WAIT
       ADD      WORD FVTOS # 8
       NEXT
       END-CODE

: .DIGITS    ( last first -- )
        2DUP > ABORT"  FP I/O error. "
        DO I 1- 2/ FLOAT-BCD + C@ 16 /MOD I 2 MOD
             IF   DROP  ELSE   NIP  THEN
             ASCII 0 + FEMIT
         -1 +LOOP ;

: FULL2    ( n -- )
        0 <# # # #> TYPE ;

CODE FIXBCD   ( n1 -- n2 | FLOAT-BCD possibly changed )
        CLEAR_LABELS
        CALL    (2VEMPTY)
        MOV     AL, FLOAT-BCD 8 +
        CMP     AL, # $10
        JB      1 $
        MOV     BX, SP
        INC     0 [BX] WORD
        FLD     BCD FLOAT-BCD
        FLD     INTEGER*2 (I10)
        FDIVRP  ST(1), ST(0)
        FSTP    BCD FLOAT-BCD
        WAIT
1 $:    NEXT
        END-CODE

: F.SPECIAL   ( F: r -- ; cc n -- )  \ Display special f-p numbers.
        SWAP DUP $0100 AND 0=
        IF      FDROP DROP SPACES EXIT  THEN
        DUP $4000 >
        IF      DROP " EMPTY"
        ELSE    DUP $0200 AND
                IF      ." -"
                ELSE    ." +"
                THEN
                $0400 >
                IF      FDROP " INFINITY"
                ELSE    FLOAT-WORK F!  FLOAT-WORK 2@ D0= >R
                        FLOAT-WORK 4 + 2@ SWAP
                        $7FFF AND 0 $7FF8 D= R> AND
                        IF   " INDEFINITE"
                        ELSE   " NAN"
                        THEN
                THEN
        THEN
        ROT $.R ;

: E.    ( F: r -- )
        FEXAM DUP $0100 AND
        IF      24 F.SPECIAL EXIT
        THEN
        $4500 AND $4000 =
        IF      FDROP SPACE ." .00000000000000000E+00 " EXIT
        THEN
        FMAG DUP R>BCD! FIXBCD FLOAT-BCD 9 + C@
        IF      ASCII - ELSE BL
        THEN
        FEMIT ASCII . FEMIT
        1 17 .DIGITS ASCII E FEMIT 1+ DUP 0<
        IF      ASCII - ELSE ASCII +
        THEN
        FEMIT ABS DUP 99 <
        IF      FULL2 SPACE
        ELSE  .
        THEN ;

VARIABLE #PLACES

: PLACES    ( n -- )
        17 MIN 1 MAX #PLACES ! ;

        4 PLACES

CODE FPARSE    ( F: r -- int-part frac-part )
        CALL    (1VLOAD)
        CALL    (2VEMPTY)
        FLD     ST0
        FSTCW   FLOAT-WORK
        WAIT
        MOV     AX, FLOAT-WORK
        MOV     CX, AX
        OR      AX, # $00C00
        MOV     FLOAT-WORK AX
        FLDCW   FLOAT-WORK
        FRNDINT
        MOV     FLOAT-WORK CX
        FLDCW   FLOAT-WORK
        FXCH    ST(1)
        FLD     ST(1)
        FSUBP   ST1, ST0
        SUB     WORD FVTOS # 8
        WAIT
        NEXT
        END-CODE

: .INT    ( F: r -- )
        FDUP F0=
        IF
                FDROP ASCII 0 FEMIT
        ELSE
                #BCD @ DUP FMAG DUP R>BCD!
                FIXBCD - SWAP .DIGITS
        THEN ;

CODE FRNDFRC    ( F: +r1 -- +r2 )
        CALL    (1VLOAD)
        CALL    (2VEMPTY)
        FLD     INTEGER*2 #PLACES
        FCHS
        FLDL2T
        CALL    (POWER)
        FLD     REAL*8 (F2.0)
        FDIVRP  ST(1), ST(0)
        FADDP   ST(1), ST(0)
        NEXT
        END-CODE

: .FRAC    ( F: r -- )
        FDUP F0=
        IF
                FDROP #PLACES @ 0
                DO ASCII 0 FEMIT
                LOOP
        ELSE
                -1 R>BCD! #BCD @ DUP #PLACES @ 1- - SWAP .DIGITS
        THEN ;

: F.    ( r -- )
        FEXAM DUP $0100 AND
        IF      SPACE #PLACES @ 3 + F.SPECIAL EXIT
        THEN
        DROP FDUP F0<
        IF      ASCII - ELSE BL
        THEN
        FEMIT FABS FPARSE FRNDFRC
        FDUP INT FLOAT FROT F+ .INT ASCII . FEMIT .FRAC SPACE ;

: E.R0    ( #dec   #col -- )
        OVER - 5 - SPACES ASCII . FEMIT 0
        DO
                ASCII 0 FEMIT
        LOOP
        ." E+00" ;

: E.R#    ( F: r -- ; #dec -- )
        >R FDUP F0<
        IF      ASCII -
        ELSE    BL
        THEN
        FEMIT ASCII . FEMIT
        FABS R> #PLACES @ >R PLACES  FMAG DUP >R
        1+ S>D FLOAT FALOG F/ FMAG >R FRNDFRC FMAG DUP R> - >R
        R>BCD! #PLACES @ 17 DUP ROT - 1+ SWAP .DIGITS
        ASCII E FEMIT R> R> + 1+
        DUP 0<
        IF      ASCII - ELSE ASCII +
        THEN
        FEMIT ABS DUP 100 <
        IF      FULL2
        ELSE    .
        THEN
        R> PLACES ;

: E.R    ( F: r -- ; #dec #col -- )
        FEXAM  DUP $0100 AND
        IF      -ROT NIP F.SPECIAL EXIT
        THEN
        $4500 AND $4000 =                       \ Test for zero
        IF      FDROP E.R0 EXIT
        THEN
        FDUP FABS FLOG
        FDUP FABS 100. FLOAT F< 0=              \ Test exponent
        IF      7 - 0 MAX SPACES DROP
                F0< FDROP
                IF      ."  SMALL "
                ELSE    ."  LARGE "
                THEN
                EXIT
        THEN
        FDROP
        2DUP SWAP - 6 - 0<                      \ get # characters
        IF      0                                \ too big, print *'s
                DO      ASCII * FEMIT
                LOOP
                DROP FDROP
        ELSE
                OVER - 6 - 0 MAX SPACES E.R# \ ok, print it
        THEN ;

: F.R0    ( #dec   #col -- )
        2DUP SWAP - 3 - 0<
        IF      0 DO ASCII * FEMIT LOOP
                DROP
        ELSE
                OVER - 2- SPACES ." 0." 0
                DO ASCII 0 FEMIT LOOP
        THEN ;

VARIABLE F.R+-

VARIABLE F.R#INT

: (F.R)    ( |r| #dec   #col -- +frac #dec )
        F.R#INT @ - OVER - 2 - SPACES           \ output lead blanks
        F.R+- @
        IF
                ASCII -
        ELSE
                BL
        THEN
        FEMIT                                   \ output sign
        >R FSWAP F.R#INT @  R>BCD! F.R#INT @    \ convert to BCD
        #BCD @ DUP ROT - SWAP 1- .DIGITS R> ;   \ output digits

: F.R ( F: r -- ; #dec #col -- )
        FEXAM DUP $0100 AND
        IF      -ROT NIP F.SPECIAL EXIT
        THEN
        $4500 AND $4000 =                       \ test for a zero
        IF                                      \ if found, handle specially
                FDROP F.R0 EXIT
        THEN
        FDUP FINFINITY=
        IF   ." INFINITY " EXIT   THEN
        FDUP F0< F.R+- ! FDUP                   \ store the sign flag
        FABS OVER #PLACES @ SWAP #PLACES !
        >R FRNDFRC R> PLACES
                        \ round the number to the proper number of digits
        FMAG 1+ 1 MAX DUP F.R#INT !             \ get exponent
        >R 2DUP R> - SWAP - 2 - 0<              \ get the digit count
        IF
                FDROP ( SPACE ) E.R                 \ too big, use E.R
        ELSE
                FNIP FPARSE (F.R) ASCII . FEMIT  \ output integer
                >R 0 R>BCD! R> #BCD @ DUP ROT - SWAP 1- .DIGITS
        THEN ;   \ convert and output fractional part

: FDEPTH     ( -- n )
        FSP0 FVTOS @ - 8 / ;

72 rmargin !

: .FS  ( -- )
        FDEPTH ?DUP
        IF      CR 0
                DO
                        FDEPTH I - 1- FPICK
                        8 ?LINE 3 10 F.R  KEY? ?LEAVE
                LOOP
        ELSE ." Empty "
        THEN ;

: ROUND    ( F: r -- ; -- d )
        FDUP  F0>
        IF      RND>-INF
        ELSE    RND>+INF
        THEN ;

: IFLOAT    ( F: -- r ; n -- )
        S>D  FLOAT  ;

: R>N    ( F: r -- ; -- n )
        ROUND  ( INT )  DROP  ;
        ( Like  F>S  in PLOT.BLK )

: F2DUP    ( F: r1 r2 -- r1 r2 r1 r2 )
        FOVER  FOVER  ;

: FMOD    ( F: r1 r2 -- r3 )
        F2DUP F/  INT  FLOAT  F*  F-  ;

: F,    ( F: r -- )
    HERE  8 ALLOT  F!  ;

: FARRAY   ( Comp:  rn ... r1 r0 n+1 -- ) ( Run:  k -- rk_addr)
    CREATE
        DUP  ,   0   DO   F,   LOOP
    DOES>                               ( index pfa )
        SWAP DUP  0<
        IF
                DROP  @
        ELSE
                8 * 2+  +
        THEN    ;

: ?FSTACK  ( -- )
        FVTOS @ FSP0 SWAP U<
        IF      FCLEAR TRUE ABORT" Floating Point Stack Underflow "
        THEN
        FVTOS @ FSP0 FSTACK-SIZE 8 * - U<
        IF      FCLEAR TRUE ABORT" Floating point Stack Overflow "
        THEN
        FPSW> DUP 1 AND
        IF      FCLEAR  CR ." Invalid Floating Point Operation. "
        THEN
        DUP 4 AND
        IF      FCLEAR  CR ." Floating Point Divsion by zero. "
        THEN
        8 AND
        IF      FCLEAR  CR ." Floating Point Overflow. "
        THEN
        (?STACK) ;

' ?FSTACK IS ?STACK

CREATE FR 94 ALLOT

: FFILL  INITFP FR 14 + 80 -1 FILL  FR >FREGS INITFP ;

CREATE TAGLINE
        ," Valid  "
        ," Zero   "
        ," Special"
        ," Empty  "

: .TAG   ( n1 -- )              \ Print an alpha version of the tag.
        FR (TAG) 8 * TAGLINE + COUNT TYPE ;

CREATE EXCEPTIONAL ," PUOZDI"

: .FPSTATUS   ( -- )
        FR 2+ @ DUP FLIP $47 AND ."  CC = " DUP 7 >
        1 AND $30 + FEMIT 1 SPACES 7 AND
        3 0
        DO      DUP 3 > 1 AND $30 + FEMIT 1 SPACES
                3 AND 2*
        LOOP
        3 SPACES
        DROP $3F AND 7 1
        DO      DUP $1F >
                IF      I EXCEPTIONAL + C@ FEMIT
                        ASCII E FEMIT 1 SPACES
                THEN
                $1F AND 2*
        LOOP
        DROP ;

: FR.   ( -- )
        FR >FREGS> BASE @ HEX CR 14 0
        DO FR I + @ 0 <# # # # # #> SPACE TYPE 2 +LOOP
        3 SPACES .FPSTATUS
        8 0
        DO      CR SPACE 10 0
                DO      FR 14 + J 10 * + I + C@ .2W
                LOOP
                I .TAG
        LOOP
        CR ." FVTOS = " FVTOS @ H.  ."  FVBOS = " FVBOS @ H.
        ."  FBASE = " FSP0 H.  BASE ! ;

ALSO HIDDEN DEFINITIONS

: <.FSTAT>   ( -- )
        #OUT @ #LINE @ >R >R  ATTRIB C@ >R  BASE @ >R
        DECIMAL 74 0 AT FDEPTH DUP
        IF      >ATTRIB4 SPACE {.} SPACE >ATTRIB1
        ELSE    >ATTRIB1 DROP ."  {0}  "
        THEN
        R> BASE ! R> ATTRIB C! R> R> AT ;

PREVIOUS DEFINITIONS

: .FSTATUS   ( -- )
        [ HIDDEN ALSO ]
        DEFERS STATUS
        ?STACK STATV @
        IF   <.FSTAT>
        THEN ;

' .FSTATUS IS STATUS

PREVIOUS PREVIOUS

\ .( ..Loaded)

