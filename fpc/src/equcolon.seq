\ EQUCOLON.SEQ          words to modify VALUEs          by Tom Zimmer

\ Link this file into the FILELIST chain.

FILES DEFINITIONS

VARIABLE EQUCOLON.SEQ

FORTH DEFINITIONS   META IN-META

\ CODE portion of inline manipulation words.

CODE    %@>     ( --- n1 )
\ Fetch from the BODY field of following definition.
                LODSW ES:
                MOV BX, AX
                PUSH 3 [BX]
                NEXT            END-CODE

CODE    %!>     ( n1 --- )
\ Store to BODY field of following definition.
                LODSW ES:
                MOV BX, AX
                POP 3 [BX]
                NEXT            END-CODE

CODE    %U@>    ( --- n1 )
\ Fetch the DATA field of a USER definition in user space.
                LODSW ES:
                MOV BX, AX
                MOV BX, 3 [BX]
                ADD BX, UP
                PUSH 0 [BX]
                NEXT            END-CODE

CODE    %U!>    ( n1 --- )
\ Store into the DATA field of a USER definition in user space.
                LODSW ES:
                MOV BX, AX
                MOV BX, 3 [BX]
                ADD BX, UP
                POP 0 [BX]
                NEXT            END-CODE

CODE   %+!>     ( n1 --- )
\ Increment the BODY field of the following definition by n1.
                POP CX
                LODSW ES:
                MOV BX, AX
                ADD 3 [BX], CX
                NEXT            END-CODE

CODE   %INCR>   ( --- )
\ Increment the BODY field of the following definition by one.
                LODSW ES:
                MOV BX, AX
                INC 3 [BX] WORD
                NEXT            END-CODE

CODE   %DECR>   ( --- )
\ Decrement the BODY field of the following definition by one.
                LODSW ES:
                MOV BX, AX
                DEC 3 [BX] WORD
                NEXT            END-CODE

CODE   %OFF>    ( --- )
\ Clear to zero the BODY field of following definition.
                LODSW ES:
                MOV BX, AX
                MOV 3 [BX], # 0  WORD
                NEXT            END-CODE

CODE   %ON>     ( --- )
\ Set to -1 the BODY field of following definition.
                LODSW ES:
                MOV BX, AX
                MOV 3 [BX], # -1 WORD
                NEXT            END-CODE

CODE   %&>      ( -- a1 )
\ Return the BODY a1 address of the following word in a definition.
                LODSW ES:
                ADD AX, # 3
                1PUSH           END-CODE

\ ***************************************************************************
\ CODE portion ends, and HIGH LEVEL portion begins.
\
\ If you are creating a stand alone application, the following definitions
\ can be omitted.

: ?NOUSER       ( A1 --- A1 )
\ Test a1 to verify it is not a USER definition, if it is, abort.
                DUP >IS OVER >BODY <>
                ABORT" Won't work on USER words." ;

: ?USER         ( a1 --- a1 f1 )
\ test a1, is it a user word, return TRUE if it is else FALSE.
                DUP >IS OVER >BODY <> ;

: @>            ( | <name> --- n1 )
\ Fetch the BODY or USER contents of <name>.
                STATE @
                IF      ' ?USER
                        IF      COMPILE %U@>
                        ELSE    COMPILE %@>
                        THEN    X,
                ELSE    ' >IS @
                THEN    ; IMMEDIATE

: !>            ( n1 | <name> --- )
\ Store into the BODY or USER field of <name>.
\ The word "=:" is an alias of this word.
                STATE @
                IF      ' ?USER
                        IF      COMPILE %U!>
                        ELSE    COMPILE %!>
                        THEN    X,
                ELSE    ' >IS !
                THEN    ; IMMEDIATE

: +!>           ( n1 | <name> --- )
\ Increment the BODY or USER field of <name> by n1.
                ' ?NOUSER
                STATE @
                IF      COMPILE %+!> X,
                ELSE    >IS +!
                THEN    ; IMMEDIATE

: INCR>         ( | <name> --- )
\ Increment the body of <name> by one, used to modify the following
\ VALUE or VARIABLE.
                ' ?NOUSER
                STATE @
                IF      COMPILE %INCR> X,
                ELSE    >IS INCR
                THEN    ; IMMEDIATE

: DECR>         ( | <name> --- )
\ Decrement the body of <name> by one, used to modify the following
\ VALUE or VARIABLE.
                ' ?NOUSER
                STATE @
                IF      COMPILE %DECR> X,
                ELSE    >IS DECR
                THEN    ; IMMEDIATE

: OFF>          ( | <name> --- )
\ Turn off the VALUE or VARIABLE <name> following.
                ' ?NOUSER
                STATE @
                IF      COMPILE %OFF> X,
                ELSE    >IS OFF
                THEN    ; IMMEDIATE

: ON>           ( N1 T1 --- )
\ Turn on the VALUE or VARIABLE <name> following.
                ' ?NOUSER
                STATE @
                IF      COMPILE %ON> X,
                ELSE    >IS ON
                THEN    ; IMMEDIATE

: &>            ( t1 --- a1 )           \ 07/03/89 RB
\ Return the BODY address a1 of word t1, a VALUE of VARIABLE.
                ' ?NOUSER
                STATE @
                IF      COMPILE %&> X,
                ELSE    >IS
                THEN    ; IMMEDIATE

