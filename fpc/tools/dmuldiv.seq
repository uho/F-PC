\ Division of unsigned quad by double.          08:45 31Oct87RLS

\ Version for sequential files and forward assembler.  Use with Tom Zimmer's
\   FF Forth.

comment:
 This file contains a Code (8086) routine for a true double
 precision divide routine  UMD/MOD .  The numerator is an
 unsigned quad precision number and the divisor is an unsigned
 double precision number.  The result returned is an unsigned
 double quotient (1st and 2nd on stack), and an unsigned double
 remainder (3rd and 4th on stack).  Another routine multiplies
 two unsigned double numbers, yielding an unsigned quad product.
 The file also contains a simple method for using labels in
 code routines in F83.

                  Robert L. Smith
                2300 St. Francis Dr.
                Palo Alto, CA 94303
comment;

comment:

      The divide routine is based on the article "Unsigned
      Division Code Routines" by Robert L. Smith (Forth
      Dimensions, Vol. VIII, No. 6, March/April, 1987), and
      on a subsequent letter "Mods Quad Divides" by Michael
      Barr (Forth Dimensions, Vol. IX, No. 2, July/August,
      1987).

      Note that the F83 assembler uses the sequence
             BX AX MOV
      to mean (in INTEL or MASM mnemonics):
             MOV AX,BX

comment;

comment:

\ Labels for Assembler                          03:54 02Nov87RLS

 30 CONSTANT MAXLABELS       HEX
CREATE SHORT_LABELS    MAXLABELS 4 * ALLOT
: SXBYTE   ( -- )  DUP 80 AND IF  FF00 OR  THEN ;   DECIMAL

: CLEAR_LABELS   ( -- )  SHORT_LABELS MAXLABELS 4 * 0 FILL ;

: CHECKLABEL   ( n -- m ) \ Or abort
     DUP MAXLABELS 1- U> ABORT"  Bad Label "
     2* 2* SHORT_LABELS + ;

: $     ( n1 -- n2 )
     CHECKLABEL DUP @
     IF  @  ELSE  2+ DUP @ SWAP HERE 2+ SWAP !
         DUP 0=  IF  HERE 2+ +  THEN
     THEN ;

comment;

comment:

Typical use would be:

   CODE FOO
   CLEAR_LABELS
     n1 $ JB  ...  n2 $ JA  ...   ( Forward references )
     ...
     n1 $: AX BX ADD  ...         ( Define label n1 )
     ...
     n1 $ #) JMP                  ( Backward reference )
     n2 $: ...                    ( Define label n2 )
   END-CODE

comment;

comment:

: $RESOLVE   ( linkaddr -- )
     @ DUP 0= IF  DROP EXIT  THEN  0
     BEGIN
          + DUP 1- C@ OVER HERE OVER - SWAP 1- C!
          SXBYTE DUP 0=
     UNTIL
     2DROP ;

: $:   ( n -- )
     CHECKLABEL DUP 2+ $RESOLVE  0 OVER 2+ !
     HERE SWAP ! ;

: CLEAR_LABEL   ( n -- )
     CHECKLABEL 4 0 FILL ;

comment;

comment:

MAXLABELS     Maximum number of short labels.
SHORT_LABELS  Vector for 30 short labels.
SXBYTE        Sign extension for a byte.

CLEAR_LABELS  Routine to clear the local short labels.

CHECKLABELS   Verify that the input argument is in the allowed
              range.  Then point to the beginning of the
              label information for that argument.

$             Takes an argument from 0 to 29.  Used to reference
              a label for relative jumps in the assembler.  The
              label may be referenced before and/or after its
              definition.

comment;

\ Unsigned quad divided by double.              10:46 13Oct87RLS

CODE UMD/MOD   ( uquad uddiv -- udquot udmod )
        CLEAR_LABELS
        POP  CX
        POP  DX
        POP  AX
        POP  BX
        POP  DI
        PUSH SI
        PUSH BP
        MOV  BP, SP
        MOV  SI, 4 [BP]
        MOV  BP, CX
        CMP  BP, AX
        JA   6 $
        JNE  7 $
        CMP  DX, BX
        JA   6 $
 7 $: ( INT  0)       \ Remove parens if you have a Divide Interrupt.
        MOV  AX, DI
        MOV  BX, SI
        MOV  SI, # -1
        MOV  DI, SI
        JMP  8 $
 6 $:   MOV  CX, # 32
        CLC
 1 $:   RCL  SI
        RCL  DI
        RCL  BX
        RCL  AX
        JAE  3 $
 2 $:   SUB  BX, DX
        SBB  AX, BP
        STC
        LOOP 1 $
        JMP  5 $
 3 $:   CMP  AX, BP
        JB   4 $
        JNE  2 $
        CMP  BX, DX
        JAE  2 $
 4 $:   CLC
        LOOP 1 $
 5 $:   RCL  SI
        RCL  DI
 8 $:   MOV  CX, SI
        POP  BP
        POP  SI
        POP  DX
        PUSH BX
        PUSH AX
        PUSH CX
        PUSH DI
        NEXT
        END-CODE

comment:

$RESOLVE     Used to resolve forward short label references.

$:           Used to define a local label for short references.
             The input argument is a label number in the range
             of 0 to 29.

CLEAR_LABEL  Clear the specified label.

comment;

\ Double precision Multiply.                    08:40 13Oct87RLS

CODE UMD*   ( ud1 ud2 -- qprod )
        POP  DI
        POP  BX
        POP  CX
        POP  DX
        SUB  SP, # 2            \ Get room for L.S. Product.
        PUSH BP
        PUSH SI
        MOV  SI, DX
        MOV  AX, BX
        MUL  DX                 \ BD
        MOV  BP, SP
        MOV  4 [BP], AX         \ BDlo to stack.
        XCHG SI, DX             \ BDhi to SI, D to DX
        MOV  AX, DI
        MUL  DX                 \ AD
        ADD  SI, AX             \ BDhi + ADlo
        ADC  DX, # 0            \ ADhi + carry
        MOV  AX, BX
        MOV  BX, DX
        MUL  CX                 \ BC
        XOR  BP, BP
        ADD  SI, AX             \ BDhi + ADlo + BClo
        ADC  BX, DX             \ ADhi + BChi
        ADC  BP, # 0            \ Carry into MS part
        MOV  AX, CX
        MUL  DI                 \ AC
        ADD  AX, BX             \ AClo + ADhi + BC hi
        ADC  DX, BP             \ AChi + carrys
        MOV  BX, SI
        POP  SI
        POP  BP
        PUSH BX
        PUSH AX
        PUSH DX
        NEXT
        END-CODE

: D*    ( d1 d2 -- dprod )
     UMD* 2DROP ;

comment:

UMD/MOD    Unsigned division of a quad number by a double,
           yielding an unsigned quotient and remainder.
           If you wish to use Interrupt 0 for reporting
           errors, remove the parentheses from line 6.

comment;

