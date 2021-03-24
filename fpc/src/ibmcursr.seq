\ IBMCURSR      IBM Specific display       Enhancements by Tom Zimmer

DECIMAL

CODE IBM-AT     ( col row -- )          \ set cursor position
                POP AX
                POP DX
                MOV DH, AL
                XOR BH, BH
                MOV AH, # 2
                PUSH SI         PUSH BP
                INT $10
                POP BP          POP SI
                NEXT   C;

\ CODE IBM-SCR@   ( --- N1 )
\               XOR BH, BH
\               MOV AH, # 8
\               PUSH SI         PUSH BP
\               INT $10
\               POP BP          POP SI
\               1PUSH           END-CODE

\ CODE IBM-SCR!   ( N1 --- )
\               XOR BH, BH
\               MOV CX, # 1                     \ one character
\               POP AX
\               MOV BL, AH                      \ attrib to BL
\               MOV AH, # 9
\               PUSH SI         PUSH BP
\               INT $10
\               POP BP          POP SI
\               NEXT            END-CODE

CODE IBM-AT?    ( -- col row )          \ read cursor position
                XOR BH, BH
                MOV AH, # 3
                PUSH SI         PUSH BP
                INT $10
                POP BP          POP SI
                SUB AX, AX
                MOV AL, DH
                MOV DH, AH
                2PUSH           END-CODE

CODE IBM-DARK   ( -- )                  \ Clear the screen
                MOV AH, # $0F
                PUSH SI         PUSH BP
                INT $10
                SUB AH, AH
                INT $10                 \ send it back, clearing the screen
                POP BP          POP SI
                NEXT   C;

CODE LFILLW     (  seg start-addr BYTE-count WORD -- )
                CLD             POP AX
                POP CX
                SHR CX, # 1
                POP DI          POP BX
                MOV DX, ES      MOV ES, BX
                REPNZ           STOSW
                MOV ES, DX
                NEXT            END-CODE

\ : rev-dark      ( --- )
\                 ?dosio
\                 if      ibm-dark
\                 else    blank.color
\                         VIDEO-SEG @ 0 ROWS COLS 2* *
\                         attrib c@ $10 /mod swap $10 * or flip $20 or LFILLW
\                         show.color
\                 then    0 0 at ;

: norm-dark     ( --- )
                ?dosio
                if      ibm-dark
                else    blank.color
                        VIDEO-SEG @ 0 ROWS COLS 2* *
                        attrib c@ flip $20 or LFILLW
                        show.color
                then    0 0 at ;

CODE IBM--LINE  ( -- )
                PUSH SI         PUSH BP
                XOR BH, BH
                MOV AH, # 3
                INT   $10                       \ Read Cursor Position
                MOV CH, DH
                XOR CL, CL                      \ Upper left corner
                MOV DH, ' ROWS >BODY            \ bottom
                DEC DH
                MOV DL, ' COLS >BODY            \ right
                DEC DL
                MOV BH, # 7                     \ filler attribute
                MOV AH, # 6                     \ service scroll up
                MOV AL, # 1                     \ lines to scroll
                INT   $10
                POP BP          POP SI
                NEXT   C;

: >IBM          ( -- )
                ['] IBM-AT    IS AT
                ['] IBM-AT?   IS AT?
                ['] NORM-DARK IS DARK
                ['] IBM--LINE IS -LINE ;        >IBM

: CURSOR_POS_INIT       ( --- )
                IBM-AT? #LINE ! #OUT ! ;

' CURSOR_POS_INIT IS CURSORSET

CURSOR_POS_INIT

\ Cursor shape control words by Tom Zimmer

\ CODE SET-CURSOR ( N1 --- )
\                 POP CX
\                 MOV AH, # 1
\                 PUSH SI         PUSH BP
\                 INT $10
\                 POP BP          POP SI
\                 NEXT C;
\
\ : GET-CURSOR    ( --- SHAPE ) 0 $460 @L ;

: CURSOR-OFF    ( --- ) GET-CURSOR $2000  OR SET-CURSOR ;

: CURSOR-ON     ( --- ) GET-CURSOR $0F0F AND SET-CURSOR ;

ONLY FORTH ALSO HIDDEN DEFINITIONS ALSO

: *NORM-CURSOR  ( --- )
                CROWS DUP 1- FLIP + SET-CURSOR ;

: *BIG-CURSOR   ( --- )
                CROWS SET-CURSOR ;

: *MED-CURSOR   ( --- )
                CROWS DUP 2/ FLIP + SET-CURSOR ;

FORTH DEFINITIONS

DEFER NORM-CURSOR       ' *NORM-CURSOR IS NORM-CURSOR
DEFER BIG-CURSOR        ' *BIG-CURSOR IS BIG-CURSOR
DEFER MED-CURSOR        ' *MED-CURSOR IS MED-CURSOR

DEFINED SRCON NIP
#IF
        SRCON           \ now default to source on for debugging
#THEN

ONLY FORTH ALSO DEFINITIONS

