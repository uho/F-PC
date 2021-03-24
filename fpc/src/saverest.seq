\ SAVEREST.SEQ  Save and restore words for variables or values.

FILES DEFINITIONS

VARIABLE SAVEREST.SEQ

FORTH DEFINITIONS   META IN-META

comment:

        These words allow you to quickly save the contents of a
        variable of value on the return stack, and set it to some other
        value temporarily with SAVE!>.  The previous contents may then
        be restored with RESTORE>.

        Here is an example, first the old way:

                VARIABLE MYSTUFF

                : DEF   ( --- )
                        32 MYSTUFF DUP @ >R !
                        ... Do something with the new MYSTUFF ...
                        R> MYSTUFF ! ;

        Here is an example of how to use SAVE!> and RESTORE>:

                : DEF   ( --- )
                        32 SAVE!> MYSTUFF
                        ... Do something with the new MYSTUFF ...
                        RESTORE> MYSTUFF ;

comment;

CODE %SAVE!>    ( n1 --- )
\ Save the BODY contents of the following definition on the return stack
\ and set its BODY to n1.
                LODSW ES:
                MOV BX, AX
                ADD BX, # 3
                SUB RP, # 2
                MOV AX, 0 [BX]
                MOV 0 [RP], AX
                POP 0 [BX]
                NEXT            END-CODE

CODE %SAVE>     ( --- )
\ Save the BODY contents of the following definition on the return stack.
                LODSW ES:
                MOV BX, AX
                SUB RP, # 2
                MOV AX, 3 [BX]
                MOV 0 [RP], AX
                NEXT            END-CODE

CODE %RESTORE>  ( --- )
\ Restore the BODY of the following definition from the return stack.
                XCHG RP, SP
                POP DX
                XCHG RP, SP
                LODSW ES:
                MOV BX, AX
                MOV 3 [BX], DX
                NEXT            END-CODE

CODE %USAVE!>   ( n1 --- )
\ Save the USER data area for the following definition on the return
\ stack and set it to n1.
                LODSW ES:
                MOV BX, AX
                MOV BX, 3 [BX]          \ get body contents
                ADD BX, UP              \ add to USER table
                SUB RP, # 2
                MOV AX, 0 [BX]          \ save contents in AX
                MOV 0 [RP], AX          \ move AX to return stack
                POP 0 [BX]              \ pop data stack into USER table
                NEXT            END-CODE

CODE %USAVE>    ( --- )
\ Save the USER data area for the following definition on the return
\ stack.
                LODSW ES:
                MOV BX, AX
                SUB RP, # 2
                MOV BX, 3 [BX]          \ get body contents
                ADD BX, UP              \ add to USER table
                MOV AX, 0 [BX]          \ get USER table contents
                MOV 0 [RP], AX
                NEXT            END-CODE

CODE %URESTORE> ( --- )
\ Restore the USER data area of the following definition from the
\ return stack.
                XCHG RP, SP
                POP DX
                XCHG RP, SP
                LODSW ES:
                MOV BX, AX
                MOV BX, 3 [BX]          \ get body contents
                ADD BX, UP              \ add to USER table
                MOV 0 [BX], DX
                NEXT            END-CODE

: ?COMP         ( --- )
\ Abort if we are not in compiling.
                STATE @ 0= ABORT" Use only while compiling" ;

: SAVE!>        ( n1 | <name> --- )
\ Leaves value in <name> on return stack and sets body of <name> to n1,
\ the equivalent of "VARIABLE-NAME DUP @ >R !".
                ?COMP
                ' DUP @REL>ABS DUP
                [  [ASSEMBLER] DOUSER-VARIABLE META ] LITERAL = SWAP
                [  [ASSEMBLER] DOUSER-DEFER    META ] LITERAL = OR
                IF      COMPILE %USAVE!>
                ELSE    COMPILE %SAVE!>
                THEN    X, ; IMMEDIATE

: SAVE>         ( | <name> --- )
\ Saves value in <name> body on the return stack.
\ Equivelant to "VARIABLE-NAME @ >R".
                ?COMP
                ' DUP @REL>ABS DUP
                [  [ASSEMBLER] DOUSER-VARIABLE META ] LITERAL = SWAP
                [  [ASSEMBLER] DOUSER-DEFER    META ] LITERAL = OR
                IF      COMPILE %USAVE>
                ELSE    COMPILE %SAVE>
                THEN    X, ; IMMEDIATE

: RESTORE>      ( | <name> --- )
\ Restore body of <name> from the return stack.
\ Equivelant to "R> VARIABLE-NAME !".
                ?COMP
                ' DUP @REL>ABS DUP
                [  [ASSEMBLER] DOUSER-VARIABLE META ] LITERAL = SWAP
                [  [ASSEMBLER] DOUSER-DEFER    META ] LITERAL = OR
                IF      COMPILE %URESTORE>
                ELSE    COMPILE %RESTORE>
                THEN    X, ; IMMEDIATE

