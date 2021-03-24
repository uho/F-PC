\ KERNEL1.SEQ  Source code for KERNEL1.COM,   modified by Tom Zimmer

ONLY FORTH   META ALSO FORTH

TRUE    CONSTANT INLINE_NEXT    \ Enable Inline NEXT

DECIMAL

: ?.INLINE      ( --- )         \ Print state of INLINE_NEXT
                CR ." NEXT is currently " INLINE_NEXT >REV
                IF      [ASSEMBLER] INLINEON  [FORTH]
                        ."  INLINE. "
                ELSE    [ASSEMBLER] INLINEOFF [FORTH]
                        ."  NOT " >NORM ."  INLINE. "
                THEN    >NORM CR ;
?.INLINE

   256 DP-T !           \ Set Dictionary pointer
     0 DP-X !           \ Set LIST DP

IN-META

: ]]   ]   ;
: [[   [COMPILE] [   ; FORTH IMMEDIATE META

FORWARD: DEFINITIONS
FORWARD: [

LABEL ORIGIN    JMP HERE 8000 + \ jump to cold start: will be patched
                JMP HERE 8000 + \ jump to warm start: will be patched
                END-CODE

LABEL DPUSH     PUSH DX         END-CODE
LABEL APUSH     PUSH AX         END-CODE
LABEL >NEXT     LODSW ES:
                JMP AX          END-CODE

\ Create the FORTH vocabulary as the first definition in dictionary.

HERE-T ,-Y                      \ valid "previous" CFA for "CREATE

HERE-Y HERE-T CNHASH !-Y        \ first entry in >NAME hash table

HERE-T DUP 100 + CURRENT-T !    \ harmless

HERE-Y VOCABULARY FORTH   FORTH DEFINITIONS

0 OVER 2+ !-Y ( link )

2+ SWAP  >BODY-T
'F'   2*                        \ Hash in First char shifted left one
'O' + 2*                        \ Plus second char, sum shifted left one
5 +                             \ Plus length byte
#TTHREADS 1- AND 2*             \ Determine which thread FORTH goes in.
+ !-T                           \ store it in the proper thread.

IN-META

VOCABULARY FILES

FILES DEFINITIONS

\ Create the linked list of files that have been loaded.

VARIABLE META86.SEQ VARIABLE KERNEL1.SEQ

FORTH DEFINITIONS

VARIABLE XSEG
VARIABLE YSEG
VARIABLE BKABLE                         \ are we breakable?
VARIABLE BKFLAG                         \ was shift-control-break pressed?

CLEAR_ALL_LABELS

LABEL ABNORM    MOV AX, # $AD26         \ Value to restore in >NEXT
                MOV >NEXT AX            \ Restore it
                MOV AX, # $E0FF         \ Value to restore in >NEXT + 2
                MOV >NEXT 2+ AX         \ Restore it
                XOR AX, AX
                MOV DS, AX
                MOV BX, # $471
                MOV 0 [BX], AL
                MOV AX, CS
                MOV DS, AX
                JMP ORIGIN 3 +  END-CODE

LABEL BIOSBK    PUSH AX         \ control-break & shift-control-break
                PUSH DS         \ handler
                SUB AX, AX
                MOV DS, AX              \ DS = 0
                MOV $471 # $00          \ clear the control break flag
                MOV AL, $417            \ get shift-key status at $0000:0417
                AND AL, # 3             \ if neither shift bit is set
                JZ 0 $                  \ branch to all done
                MOV CS: BKFLAG # $FFFF WORD     \ turn on SHIFT-CONTROL-BREAK
        0 $:    CMP CS: BKABLE # $0000 WORD     \ if breakable is reset, then
                JZ 1 $                          \ branch around abort func
                MOV AL, # $E9
                MOV CS: >NEXT AL
                MOV AX, # ABNORM >NEXT - 3 -
                MOV CS: >NEXT 1+ AX
        1 $:    POP DS
                POP AX
                IRET            END-CODE

LABEL DOSBK     PUSH AX
                MOV AH, # 0             \ throw away BREAK KEY
                INT $16
                POP AX
                CLC
                RETF            END-CODE

LABEL NEST              \ JMP = 15 cycles
        XCHG RP, SP     \  4 cycles
        PUSH ES         \ 10 cycles
        PUSH IP         \ 11 cycles
        XCHG RP, SP     \  4 cycles
        MOV DI, AX      \  2 cycles
        MOV AX, 3 [DI]  \ 18 cycles     \ get relative segment
\        ADD AX, XSEG    \ 15 cycles     \ adjust by base of list space

\ Patch the following ADD to add the current value of XSEG as of this
\ invocation of F-PC. Patched by COLD in KERNEL4.SEQ
LABEL NESTPATCH
        ADD AX, # XSEG  \ really patched later to add actual XSEG value.

        MOV ES, AX      \  2 cycles     \ move into ES
        SUB IP, IP      \  3 cycles     \ clear IP
        NEXT
        END-CODE
META

CODE EXIT       ( -- )  \ Terminate a high-level definition
                XCHG RP, SP     \ 4 cycles
                POP IP          \ 8 cycles
                POP ES          \ 8 cycles
                XCHG RP, SP     \ 4 cycles
                NEXT
                END-CODE

CODE ?EXIT      ( f1 -- )  \ If boolean f1 is true, exit from definition.
                POP CX
          CX<>0 IF      JMP ' EXIT
                THEN
                NEXT            END-CODE

CODE UNNEST     ( --- )   \ Same as EXIT
                XCHG RP, SP     \ 4 cycles
                POP IP          \ 8 cycles
                POP ES          \ 8 cycles
                XCHG RP, SP     \ 4 cycles
                NEXT
                END-CODE

LABEL DODOES  ( addr1 addr2 -- addr1 )
\ The two addresses result from two calls.
        XCHG RP, SP     \  4 cycles
        PUSH ES         \ 10 cycles
        PUSH IP         \ 11 cycles
        XCHG RP, SP     \  4 cycles
        POP DI
        MOV AX, 0 [DI]
\        ADD AX, XSEG

\ Patch the following ADD to add the current value of XSEG as of this
\ invocation of F-PC. Patched by COLD in KERNEL4.SEQ
LABEL DOESPATCH
        ADD AX, # XSEG  \ really patched later to add actual XSEG value.

        MOV ES, AX
        SUB IP, IP
        NEXT            END-CODE

VARIABLE UP     \ Pointer to current USER area

LABEL DOCONSTANT  \ This code level word is CALLed.
                MOV BX, AX
                PUSH 3 [BX]
                NEXT            END-CODE

LABEL DOVALUE                           \ Save as constant, but it is assumed
                MOV BX, AX
                PUSH 3 [BX]     \ the user may change it.
                NEXT            END-CODE

LABEL DOUSER-VARIABLE   \ CALLed to fetch from USER area.
                POP BX
                MOV AX, 0 [BX]
                ADD AX, UP
                1PUSH           END-CODE

CODE (LIT)      ( -- n )  \ Fetches an in-line word
                LODSW ES:       1PUSH           END-CODE

CODE <'>        ( -- n )  \ Fetches an in-line word (same as (LIT) )
                LODSW ES:       1PUSH           END-CODE

T: LITERAL      ( n -- ) [TARGET] (LIT)   ,-X   T;
T: DLITERAL     ( d -- ) SWAP [TARGET] (LIT) ,-X   [TARGET] (LIT) ,-X   T;
T: ASCII        ( -- )   [COMPILE] ASCII [[ TRANSITION ]] LITERAL [META]  T;
T: [']          ( -- )   'T >BODY @
                         [[ TRANSITION ]] LITERAL  [META]   T;
: CONSTANT      ( n -- )  \ a defining word that creates constants
                RECREATE 233 C,-T
                [[ ASSEMBLER DOCONSTANT ]] LITERAL HERE 2+ - ,-T
                DUP ,-T   CONSTANT   ;

: VALUE         ( n -- )  \ Internally the same as CONSTANT
                RECREATE 233 C,-T
                [[ ASSEMBLER DOVALUE    ]] LITERAL HERE 2+ - ,-T
                DUP ,-T   VALUE      ;

FORWARD: <(;CODE)>
T: DOES>        ( -- )
                [FORWARD] <(;CODE)> HERE-T ,-X
                HERE-T  ( DOES-OP ) 232 C,-T
                [[ ASSEMBLER DODOES ]] LITERAL HERE 2+ - ,-T
                HERE-X PARAGRAPH-X + DUP DPSEG-X ! SEG-X @ - ,-T
                DP-X OFF T;

: NUMERIC   ( -- )
                [FORTH] HERE [META] NUMBER   DPL @ 1+
                IF      [[ TRANSITION ]] DLITERAL [META]
                ELSE    DROP   [[ TRANSITION ]] LITERAL [META]   THEN  ;

: UNDEFINED     ( -- )
                HERE-X >XREL 0 ,-X
                CR >IN @ BL WORD COUNT TYPE >IN !
                15 #OUT @ - SPACES .SEQHANDLE
                40 #OUT @ - SPACES loadline @ 4 .R
                ."   Forward reference or unresolved."
                IN-FORWARD  [FORTH] CREATE [META] TRANSITION
                [FORTH] ,   FALSE ,   [META]
                DOES>   FORWARD-CODE   ;

[FORTH] VARIABLE T-IN      META

: ]             ( -- )  \ Return to compilation state.
                STATE-T ON   IN-TRANSITION
        BEGIN   >IN @ T-IN !
                BEGIN   BL WORD DUP C@ 0=       \ If nothing in line
                        ?FILLBUFF               \ Optionally refill buffer
                        INLENGTH 0> AND         \ and input buf not empty
                WHILE   DROP 0 T-IN !
\                         ?LISTING
\                         IF      CR BASE @ >R HEX
\                                 HERE-T 4 .R SPACE
\                                 LINESTRT HERE-T OVER - 5 MIN BOUNDS
\                                 ?DO     I C@-T 0 <# # # BL HOLD #> TYPE
\                                 LOOP    22 #OUT @ - SPACES
\                                 TIB #TIB @ TYPE
\                                 R> BASE !
\                         THEN
                        FILLTIB            \ refill the buffer
\                         HERE-T =: LINESTRT
                REPEAT  ?UPPERCASE FIND
                IF      EXECUTE
                ELSE    COUNT NUMERIC?
                        IF      NUMERIC
                        ELSE    T-IN @ >IN !   UNDEFINED
                        THEN
                THEN    STATE-T @ 0=
        UNTIL ;

T: [   ( -- )   IN-META   STATE-T OFF   T;

T: ;   ( -- )   [TARGET] UNNEST   [[ TRANSITION ]] [   T;

 : :   ( -- )   TARGET-CREATE   233 C,-T        \ a JUMP instruction
                [[ ASSEMBLER NEST ]] LITERAL HERE 2+ - ,-T
                HERE-X PARAGRAPH-X + DUP DPSEG-X !
                SEG-X @ - ( DUP H. ) ,-T
                DP-X OFF ] ;                              \ compile body addr

ASSEMBLER LOCAL_REF CLEAR_LABELS META

CODE DOBEGIN    ( -- )  \ really a NOOP
                NEXT    END-CODE

CODE DOCASE     ( -- )  \ really a NOOP
                NEXT    END-CODE

CODE DOENDCASE  ( -- )  \ really a NOOP
                NEXT            END-CODE

CODE DOTHEN     ( -- )  \ really a NOOP
                NEXT    END-CODE

CODE DOAGAIN    ( -- )  \ an unconditional branch
                MOV ES: IP, 0 [IP]
                NEXT           END-CODE

CODE DOREPEAT   ( -- )  \ an unconditional branch
                MOV ES: IP, 0 [IP]
                NEXT           END-CODE

CODE ?WHILE     ( f -- )  \ branch if flag is zero
                POP CX
          CX<>0 IF      ADD IP, # 2
                        NEXT
                THEN
                MOV ES: IP, 0 [IP]
                NEXT           END-CODE

CODE ?UNTIL     ( f -- )  \ branch if flag is zero
                POP CX
          CX<>0 IF      ADD IP, # 2
                        NEXT
                THEN
                MOV ES: IP, 0 [IP]
                NEXT           END-CODE

CODE BRANCH     ( -- )  \ Unconditional branch
                MOV ES: IP, 0 [IP]
                NEXT            END-CODE

CODE DOENDOF    ( -- )  \ Unconditional branch
                MOV ES: IP, 0 [IP]
                NEXT            END-CODE

CODE ?BRANCH    ( f -- )  \ Branch if flag is zero
                POP CX
          CX<>0 IF      ADD IP, # 2
                        NEXT
                THEN
                MOV ES: IP, 0 [IP]
                NEXT            END-CODE

CODE NEXT|      ( n1 --- )  \ Primitive form of NEXT (as in FOR - NEXT loops)
                SUB 0 [RP], # 1 WORD
            U>= IF      MOV IP, ES: 0 [IP]
                        NEXT
                THEN
                ADD RP, # 2
                ADD IP, # 2
                NEXT    END-CODE

T: BEGIN        [TARGET] DOBEGIN X?<MARK      T;
T: FOR          [TARGET] >R      X?<MARK      T;
T: NEXT         [TARGET] NEXT|   X?<RESOLVE   T;
T: AGAIN        [TARGET] DOAGAIN X?<RESOLVE   T;
T: UNTIL        [TARGET] ?UNTIL  X?<RESOLVE   T;
T: IF           [TARGET] ?BRANCH X?>MARK      T;
T: FORWARD      [TARGET] BRANCH  X?>MARK      T;
T: THEN         [TARGET] DOTHEN  X?>RESOLVE   T;
T: AFT          2DROP [TARGET] BRANCH X?>MARK X?<MARK 2SWAP   T;
T: ELSE         [TARGET] BRANCH  X?>MARK   2SWAP X?>RESOLVE   T;
T: WHILE        [TARGET] ?WHILE  X?>MARK   2SWAP              T;
T: REPEAT       [TARGET] DOREPEAT X?<RESOLVE X?>RESOLVE       T;
T: CONTINUE     2OVER [TARGET] DOREPEAT X?<RESOLVE X?>RESOLVE T;
T: BREAK        [TARGET] EXIT [TARGET] DOTHEN X?>RESOLVE      T;

CODE UNDO       ( --- )  \ Clean up Return Stack so we can EXIT from DO-loop.
                ADD RP, # 6
                NEXT            END-CODE

CODE (LOOP)     ( -- )  \ Primitive form of LOOP
                INC 0 [RP] WORD
           OV<> IF
                        MOV ES: IP, 0 [IP]
                        NEXT
                THEN
                ADD RP, # 6     ADD IP, # 2
                NEXT            END-CODE

CODE (+LOOP)    ( n -- )  \ Primitive form of +LOOP
                POP AX          ADD 0 [RP], AX
           OV<> IF
                        MOV ES: IP, 0 [IP]
                        NEXT
                THEN
                ADD RP, # 6     ADD IP, # 2
                NEXT            END-CODE

CODE (DO)       ( l i -- )  \ Primitive form of DO
                POP DX          POP BX
                XCHG RP, SP             \ 4
                LODSW ES:               \ 12 + 2
                PUSH AX                 \ 11
                ADD BX, # $8000          \ 4
                PUSH BX                 \ 11
                SUB DX, BX              \ 3
                PUSH DX                 \ 11
                XCHG RP, SP             \ 4     = 62
                NEXT            END-CODE

CODE (?DO)      ( l i -- )  \ Primitive form of ?DO
                POP DX          POP BX
                CMP BX, DX
             0= IF
                        MOV ES: IP, 0 [IP]
                        NEXT
                THEN
                XCHG RP, SP             \ 4
                LODSW ES:               \ 12 + 2
                PUSH AX                 \ 11
                ADD BX, # $8000          \ 4
                PUSH BX                 \ 11
                SUB DX, BX              \ 3
                PUSH DX                 \ 11
                XCHG RP, SP             \ 4     = 62
                NEXT            END-CODE

CODE (OF)       ( n1 n2 -- n1 )  ( or )  ( n1 n1 -- )  \ Primitive form of OF
                POP AX          MOV DI, SP
                CMP AX, 0 [DI]
            0<> IF      MOV ES: IP, 0 [IP]
                        NEXT
                THEN
                ADD SP, # 2
                ADD IP, # 2
                NEXT            END-CODE

CODE BOUNDS     ( n1 n2 --- n3 n4 )  \ Calculate limits used in DO-loop
                POP DX          POP AX          ADD DX, AX
                2PUSH           END-CODE

T: ?DO          [TARGET] (?DO)   X?>MARK   T;
T: DO           [TARGET] (DO)    X?>MARK   T;
T: LOOP         [TARGET] (LOOP)    2DUP 2+   X?<RESOLVE   X?>RESOLVE   T;
T: +LOOP        [TARGET] (+LOOP)   2DUP 2+   X?<RESOLVE   X?>RESOLVE   T;

ASSEMBLER  >NEXT META CONSTANT >NEXT
                \ Label to jump to when we are NOT using in-line NEXT
ASSEMBLER   NEST META CONSTANT >NEST
                \ Address of the nesting function
ASSEMBLER DODOES META CONSTANT >DOES
                \ Address of the doesing function
ASSEMBLER BIOSBK META CONSTANT >BIOSBK
                \ Address of the bios break function

CODE EXECUTE    ( cfa -- )  \ Execute the word whose CFA is on the stack.
                POP AX          JMP AX          END-CODE

CODE PERFORM    ( addr-of-cfa -- )      \ Performs the function  @ EXECUTE
                POP BX          MOV AX, 0 [BX]
                JMP AX          END-CODE

CODE GOTO       ( --   ;A rmb )         \ 07/03/89 RB
\  terminates execution of the current colon def used to avoid return
\ stack loading, and for execution speed by combining exit and next
\ also used by coroutines
                LODSW ES:
                XCHG SP, RP
                POP IP
                POP ES
                XCHG SP, RP
                JMP AX   END-CODE
\ used only in colon definitions:   : xx   goto yy ;

LABEL DODEFER   ( addr -- )  \ run-time code for a DEFERed word
                POP BX          MOV AX, 0 [BX]
                JMP AX          END-CODE

CODE EXEC:      ( n1 -- )  \ execute the n-th word following EXEC:
                POP BX
                SHL BX, # 1
                ADD IP, BX
                LODSW ES:
                XCHG RP, SP     \ 4
                POP IP          \ 8
                POP ES          \ 8
                XCHG RP, SP     \ 4     = 24
                JMP AX          END-CODE

LABEL DOUSER-DEFER   ( addr -- )  \ run-time codef for a USER DEFERed word
                POP BX          MOV BX, 0 [BX]
                ADD BX, UP      MOV AX, 0 [BX]
                JMP AX          END-CODE

CODE GO         \ execute CODE at specified address
                RET             END-CODE        ( addr --- )

CODE NOOP       \  Does nothing  (No-Operation)
                NEXT            END-CODE

CODE PAUSE      \  A NOP that can be patched!  Used by Multi-tasker.
                NOP                             \ Gets patched
                NOP
                NOP
                NEXT            END-CODE

CODE I          ( -- n )
\  get the current index of the innermost loop
                MOV AX, 0 [RP]  ADD AX, 2 [RP]
                1PUSH           END-CODE

CODE J          ( -- n )
\  Get the index of the second most inner loop.
                MOV AX, 6 [RP]  ADD AX, 8 [RP]
                1PUSH           END-CODE

CODE K          ( -- n )
\  Get the index of the third most inner loop.
                MOV AX, 12 [RP] ADD AX, 14 [RP]
                1PUSH           END-CODE

CODE (LEAVE)    ( -- )
\ run time version of LEAVE to jump past the end of a DO-LOOP
                MOV IP, 4 [RP]
                ADD RP, # 6
                NEXT            END-CODE

CODE (?LEAVE)   ( f -- )
\ If the flag is non-zero, jump out of the DO-LOOP.
                POP AX
                OR AX, AX
             0= IF      NEXT
                THEN
                MOV IP, 4 [RP]
                ADD RP, # 6
                NEXT            END-CODE

T: LEAVE        [TARGET] (LEAVE)   T;
T: ?LEAVE       [TARGET] (?LEAVE)  T;

CODE @          ( addr -- n )   \ Fetch a 16 bit value from addr
                POP BX          PUSH 0 [BX]
                NEXT            END-CODE

CODE !          ( n addr -- )   \ Store value n into the address addr
                POP BX          POP 0 [BX]
                NEXT            END-CODE

CODE C@         ( addr -- char )
\ Fetch an 8 bit value from addr.  Fill high part with zeros.
                POP BX          SUB AX, AX      MOV AL, 0 [BX]
                1PUSH           END-CODE

CODE C!         ( char addr -- )
\ Store the least significant 8 bits of char at the specified addr
                POP BX          POP AX          MOV 0 [BX], AL
                NEXT            END-CODE

\ ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
\ ³******************** !!!!!! CMOVE WARNING !!!!!! ********************³
\ ³******************** !!!!!! CMOVE WARNING !!!!!! ********************³
\ ³******************** !!!!!! CMOVE WARNING !!!!!! ********************³
\ ³                                                                     ³
\ ³ This version of CMOVE has been modified to use MOVSW when possible! ³
\ ³ The result of this change is that you CANNOT use CMOVE to perform a ³
\ ³ FILL!  You MUST use FILL for that!! (FILL is more efficient anyway!)³
\ ³                                                                     ³
\ ³   If you need 100 percent compatibility, remove the marked lines    ³
\ ³   and rebuild F-PC with FMETA.BAT and EXTEND.BAT.                   ³
\ ³                                                                     ³
\ ³******************** !!!!!! CMOVE WARNING !!!!!! ********************³
\ ³******************** !!!!!! CMOVE WARNING !!!!!! ********************³
\ ³******************** !!!!!! CMOVE WARNING !!!!!! ********************³
\ ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

CODE CMOVE      (  from to count -- )
\ Move "count" bytes from "from" to "to" address.
                MOV BX, IP      MOV AX, DS
                POP CX          POP DI          POP IP
                MOV DX, ES      MOV ES, AX
\ **************************************************************************
\ SEE THE NOTE ABOVE CMOVE FOR SPECIAL INFORMATION ABOUT CMOVE CHANGES!!!!!!
\ **************************************************************************
                TEST DI, # 1    \ for 100% compatibility, remove these lines
                JZ 0 $          \ for 100% compatibility, remove these lines
                JCXZ 0 $        \ for 100% compatibility, remove these lines
                MOVSB           \ for 100% compatibility, remove these lines
                DEC CX          \ for 100% compatibility, remove these lines
        0 $:    SHR CX, # 1     \ for 100% compatibility, remove these lines
                REPNZ MOVSW     \ for 100% compatibility, remove these lines
                RCL CX, # 1     \ for 100% compatibility, remove these lines
\ **************************************************************************
                REPNZ MOVSB
                MOV IP, BX      MOV ES, DX
                NEXT            END-CODE

CODE CMOVE>     ( from to count -- )
\ move "count" bytes from "from" to "to", highest address first
                MOV BX, IP      MOV AX, DS
                POP CX          DEC CX
                POP DI          POP IP
                ADD DI, CX      ADD IP, CX      INC CX
                MOV DX, ES      MOV ES, AX
                STD
                REPNZ           MOVSB
                CLD
                MOV IP, BX
                MOV ES, DX
                NEXT            END-CODE

CODE PLACE      ( from cnt to -- )
\ Move "cnt" characters from "from" to "to" + 1, with preceeding count byte
\ at "to".
                POP DI          POP CX
                MOV 0 [DI], CL
                INC DI
                CLD
                MOV BX, IP      POP IP
                MOV DX, ES
                MOV AX, DS      MOV ES, AX
                TEST DI, # 1
                JZ 0 $
                JCXZ 0 $
                MOVSB
                DEC CX
        0 $:    SHR CX, # 1
                REPNZ           MOVSW
                RCL CX, # 1
                REPNZ           MOVSB
                MOV IP, BX
                MOV ES, DX
                NEXT            END-CODE

CODE +PLACE     ( from cnt to -- )      \ append text to counted string
\ Append "cnt" characters from "from" to counted string "to", adjust
\ the count byte of "to" to include "cnt".
                POP DI          POP CX
                MOV BX, IP      POP IP
                MOV DX, ES
                SUB AX, AX
                MOV AL, 0 [DI]          \ pick up current length
                ADD 0 [DI], CL          \ adj current length plus cnt
                INC DI                  \ step to text start
                ADD DI, AX              \ adjust to current text end
                CLD
                MOV AX, DS      MOV ES, AX
                TEST DI, # 1
                JZ 0 $
                JCXZ 0 $
                MOVSB
                DEC CX
        0 $:    SHR CX, # 1
                REPNZ           MOVSW
                RCL CX, # 1
                REPNZ           MOVSB   \ append the text
                MOV IP, BX
                MOV ES, DX
                NEXT            END-CODE

DECIMAL

CODE SP@        ( -- n )
\ Push the address of the top element on the parameter stack (prior to push).
                MOV AX, SP      1PUSH           END-CODE
\ Can't use the following because it doesn't work on an 8088.
\               PUSH SP         NEXT            END-CODE

CODE SP!        ( n -- )
\ Set the parameter stack pointer to specified value.
                POP SP          NEXT            END-CODE

CODE RP@        ( -- addr )
\ Push the address of the top element of the return stack
\ onto the parameter stack.
                PUSH RP         NEXT            END-CODE

CODE RP!        ( n -- )  \ Set the return stack pointer to n .
                POP RP          NEXT            END-CODE

CODE DROP       ( n1 -- )
                ADD SP, # 2     NEXT            END-CODE

CODE DUP        ( n1 -- n1 n1 )  \ Duplicate the top element of the stack.
                MOV DI, SP      \ 2
                PUSH 0 [DI]     \ 21 = 23
                NEXT            END-CODE

CODE SWAP       ( n1 n2 -- n2 n1 )
\ Exchange the top two items on the stack.
                POP DX          POP AX
                2PUSH           END-CODE

CODE OVER       ( n1 n2 -- n1 n2 n1 )
\ Push a copy of the second stack item.
                MOV DI, SP
                PUSH 2 [DI]
                NEXT            END-CODE

CODE PLUCK      ( n1 n2 n3 --- n1 n2 n3 n1 )  
\ Copy the third stack item to top
                MOV DI, SP
                PUSH 4 [DI]
                NEXT            END-CODE

CODE TUCK       ( n1 n2 -- n2 n1 n2 )
\ Tuck the first stack element under the second.
                POP AX          POP DX
                PUSH AX         2PUSH           END-CODE

CODE NIP        ( n1 n2 -- n2 )  \ Delete the second stack item.
                POP AX          ADD SP, # 2
                1PUSH           END-CODE

CODE ROT        ( n1 n2 n3 --- n2 n3 n1 )  
\ Rotate top three stack values, bringing the third item to the top.
                POP DX          POP BX          POP AX
                PUSH BX         2PUSH           END-CODE

CODE -ROT       ( n1 n2 n3 --- n3 n1 n2 )  \ Inverse of ROT
                POP BX          POP AX          POP DX
                PUSH BX         2PUSH           END-CODE

CODE FLIP       ( n1 -- n2 )  \ Exchange the high and low halves of a word
                POP AX          XCHG AL, AH
                1PUSH           END-CODE

CODE SPLIT      ( n1 --- n2 n3 )        \ Splits n1 into two bytes, low, high
                POP BX
                SUB AX, AX
                MOV AL, BL
                PUSH AX
                MOV AL, BH
                1PUSH           END-CODE

                                        \ 07/03/89 RB
CODE JOIN       ( n1 n2 -- n3 )         \ Join bytes into one word, n2 = hi
                POP DX
                POP AX
                MOV AH, DL
                1PUSH           END-CODE

CODE ?DUP       ( n1 -- [n1] n1 )       \ duplicate n1 if <> 0
                MOV DI, SP              \  2
                MOV CX, 0 [DI]          \ 13
          CX<>0 IF                      \ 18/6
                        PUSH CX         \ 11
                THEN                    \ 32 without push
                NEXT    END-CODE        \ 33 with    push

                                        \ 07/03/89 RB
CODE ?DROP      ( n false -- false | n true -- n true )
                POP AX
                OR AX, AX
             0= IF      INC SP
                        INC SP
                THEN
                1PUSH           END-CODE

CODE R>         ( -- n )
\ Pop an item from the return stack and push onto parameter stack.
                PUSH 0 [RP]
                ADD RP, # 2
                NEXT            END-CODE

CODE R>DROP     ( --- )  \ Drop an item from the return stack
                ADD RP, # 2
                NEXT            END-CODE

CODE DUP>R      ( n1 --- n1 )  
\ Pushes a copy of the top item on parameter stack to the return stack.
                XCHG SP, RP     \  4
                PUSH 0 [RP]     \ 16 + 5
                XCHG SP, RP     \  4 = 29 cycles
                NEXT            END-CODE

CODE >R         ( n -- )  
\ Pop top of parameter stack and push value onto return stack.
                SUB RP, # 2     \  4
                POP 0 [RP]      \ 22 = 26 cycles
                NEXT            END-CODE

CODE 2R>        ( -- n1 n2 )  
\ Pop two items from return stack onto parameter stack
                PUSH 2 [RP]     \ 25
                PUSH 0 [RP]     \ 21
                ADD RP, # 4     \  4 = 50 cycles
                NEXT            END-CODE

CODE 2>R        ( n1 n2 -- )  
\ Pop two items from parameter stack, push onto return stack.
                SUB RP, # 4     \  4
                POP 0 [RP]      \ 22
                POP 2 [RP]      \ 26 = 52 cycles
                NEXT            END-CODE

CODE R@         ( -- n )  
\ Push a copy of top item on return stack onto parameter stack.
                PUSH 0 [RP]
                NEXT            END-CODE

CODE 2R@        ( -- n1 n2 )  
\ Push a copy of the top two items on the return stack onto the parameter stack.
                PUSH 2 [RP]
                PUSH 0 [RP]
                NEXT            END-CODE

CODE PICK       ( nm ... n2 n1 k -- nm ... n2 n1 nk )  
\ Push a copy of the n-th item on paramter stack.
                POP DI          SHL DI, # 1     ADD DI, SP
                PUSH 0 [DI]
                NEXT            END-CODE

CODE RPICK      ( k -- nk )
\ Pick a copy of the k-th item on the return stack and push onto the
\ parameter stack.
                POP DI          SHL DI, # 1
                PUSH 0 [RP+DI]
                NEXT            END-CODE

CODE AND        ( n1 n2 -- n3 )  
\ Perform bit-wise logical AND of top two items.
                POP BX          POP AX          AND AX, BX
                1PUSH           END-CODE

CODE OR         ( n1 n2 -- n3 )
\ Perform bit-wise logical OR of top two items on parameter stack.
                POP BX          POP AX          OR AX, BX
                1PUSH           END-CODE

CODE XOR        ( n1 n2 -- n3 )
\ Perform bit-wise logical Exclusive OR of top two stack items.
                POP BX          POP AX          XOR AX, BX
                1PUSH           END-CODE

CODE NOT        ( n -- n' )  \ Logically invert the bits of top stack item.
                POP AX          NOT AX
                1PUSH           END-CODE

-1 CONSTANT TRUE
 0 CONSTANT FALSE

CODE CSET       ( b addr -- )  
\ Logical OR of l.s. 8 bits of "b" with byte at "addr".
                POP BX          POP AX          OR 0 [BX], AL
                NEXT            END-CODE

CODE CRESET     ( b addr -- )
\ Clear bits in byte at addr corresponding to "1" bits in b .
                POP BX          POP AX
                NOT AX          AND 0 [BX], AL
                NEXT            END-CODE

CODE CTOGGLE    ( b addr -- )
\ Toggle bits in byte at addr corresponding to "1" bits in b .
                POP BX          POP AX          XOR 0 [BX], AL
                NEXT            END-CODE

CODE ON         ( addr -- )  \ Set word at addr to "true"
                POP BX          MOV 0 [BX], # TRUE WORD
                NEXT            END-CODE

CODE OFF        ( addr -- )  \ Clear all bits of word at addr.
                POP BX          MOV 0 [BX], # FALSE WORD
                NEXT            END-CODE

CODE -1!        ( addr -- )  \ Same as ON
                POP BX          MOV 0 [BX], # TRUE WORD
                NEXT            END-CODE

CODE 0!         ( addr -- )  \ Same as OFF
                POP BX          MOV 0 [BX], # FALSE WORD
                NEXT            END-CODE

CODE INCR       ( addr --- )  \ Increment word at addr.
                POP BX          INC 0 [BX] WORD
                NEXT            END-CODE

CODE DECR       ( addr --- )  \ Decrement word at addr.
                POP BX          DEC 0 [BX] WORD
                NEXT            END-CODE

CODE 0DECR      ( addr -- )     \ Decrement to zero only, not below
                POP BX
                DEC 0 [BX] WORD
             0< IF      MOV 0 [BX], # 0 WORD
                THEN
                NEXT            END-CODE

CODE +          ( n1 n2 -- sum )  \ Add top two elements
                POP BX          POP AX          ADD AX, BX
                1PUSH           END-CODE

CODE NEGATE     ( n -- n' )  \ Arithmetically negate top stack element.
                POP AX          NEG AX
                1PUSH           END-CODE

CODE -          ( n1 n2 -- n1-n2 )  \ Subtract top stack element from second
                POP BX          POP AX          SUB AX, BX
                1PUSH           END-CODE

CODE ABS        ( n1 -- n2 )   \  Return absolute value of top stack item 
                POP AX
                CWD
                XOR AX, DX
                SUB AX, DX
                1PUSH
                END-CODE

CODE D+!        ( d addr -- )  
\ Add double number "d" to double value at "addr"
                POP BX          POP AX          POP DX
                ADD 2 [BX], DX  ADC 0 [BX], AX
                NEXT            END-CODE

CODE +!         ( n addr -- )  \ Add "n" to word at "addr"
                POP BX          POP AX          ADD 0 [BX], AX
                NEXT            END-CODE

CODE C+!        ( n addr -- )  \ Add "n" to byte at "addr"
                POP BX          POP AX          ADD 0 [BX], AL
                NEXT            END-CODE


\ Since the 8086 has a seperate IO path, we define a Forth
\ interface to it.  Use P@ and P! to read or write directly to
\ the 8086 IO ports.

CODE PC@        ( port# -- n )  
\ Read 8-bit port at "port#" and push value on stack.
                POP DX          IN AL, DX       SUB AH, AH
                PUSH AX         NEXT            END-CODE

CODE P@         ( port# -- n )
\ Read 16-bit value at "port#" and push value on stack.
                POP DX          IN AX, DX       PUSH AX
                NEXT            END-CODE

CODE PC!        ( n port# -- )
\ Write 8 bit value "n" to "port#".
                POP DX          POP AX          OUT DX, AL
                NEXT            END-CODE

CODE P!         ( n port# -- )
\ Write 16 bit value "n" to "port#".
                POP DX          POP AX          OUT DX, AX
                NEXT            END-CODE

CODE PDOS       ( addr drive# --- f1 ) 
\ Read path of drive into addr, NULL terminated.
                pop dx          pop ax
                push si         mov si, ax
                mov ah, # $47   int $21
             u< if
                mov al, # 1
             else
                mov al, # 0
             then
                sub ah, ah      pop si
                1push           end-code

#TTHREADS CONSTANT #THREADS   \ Number of Threads used in dictionary.

CODE 2*         ( n -- 2*n )  \ Logical left shift n by 1 position.
                POP AX          SHL AX, # 1
                1PUSH           END-CODE

CODE 2/         ( n -- n/2 )  \ Arithmetic right shift of n by 1 position
                POP AX          SAR AX, # 1
                1PUSH           END-CODE

CODE U2/        ( u -- u/2 )  \ Logical right shift of n by 1 position
                POP AX          SHR AX, # 1
                1PUSH           END-CODE

CODE U16/       ( u -- u/16 ) \ Logical shift right by 4 bit positions. 
                POP AX
                SHR AX, # 1     SHR AX, # 1
                SHR AX, # 1     SHR AX, # 1
                1PUSH           END-CODE

CODE U8/        ( u -- u/8 )  \ Logical shift right by 3 bit positions.
                POP AX
                SHR AX, # 1
                SHR AX, # 1
                SHR AX, # 1
                1PUSH           END-CODE

CODE 8*         ( n -- 8*n )  \ Logical shift left by 3 positions.
                POP AX          SHL AX, # 1
                SHL AX, # 1     SHL AX, # 1
                1PUSH           END-CODE

CODE 1+         ( n1 --- n2 )  \  Add 1 to top stack element
                POP AX          INC AX
                1PUSH           END-CODE

CODE 2+         ( n1 --- n2 )  \  Add 2 to top stack element
                POP AX          ADD AX, # 2
                1PUSH           END-CODE

CODE 1-         ( n1 --- n2 )  \  Subtract 1 from top stack element
                POP AX          DEC AX
                1PUSH           END-CODE

CODE 2-         ( n1 --- n2 )  \  Subtract 2 from top stack element
                POP AX          SUB AX, # 2
                1PUSH           END-CODE

CODE UM*        ( n1 n2 -- d )
\  Form a 32 bit product from two 16 bit unsigned numbers
                POP AX          POP BX          MUL BX
                XCHG DX, AX     2PUSH           END-CODE

CODE *          ( n1 n2 -- n3 )  
\  Form a 16 bit product from two 16 bit numbers
                POP AX          POP BX          MUL BX
                1PUSH           END-CODE

: U*D           ( n1 n2 -- d )  
\  Form a 32 bit product from two 16 bit unsigned numbers
                UM*   ;

CODE UM/MOD     ( ud un -- URemainder UQuotient )
\ Unsigned double number divided by unsigned single results in unsigned
\ remainder and quotient, with quotient on top.
                POP BX          POP DX          POP AX
                CMP DX, BX
            U>=  ( divide by zero? )
            IF
                MOV AX, # -1    MOV DX, AX      2PUSH
            THEN
                DIV BX          2PUSH           END-CODE

CODE 0=         ( n -- f )  \  Return TRUE if n is zero.  Otherwise FALSE.
                POP AX          SUB AX, # 1     SBB AX, AX
                1PUSH           END-CODE

CODE 0<         ( n -- f )  
\  If n is negative, return TRUE.  Otherwise FALSE.
                POP AX          CWD             PUSH DX
                NEXT            END-CODE

CODE 0>         ( n -- f )
\  If n is greater than 0, return TRUE.  Otherwise FALSE.
                POP AX          NEG AX
           OV<> IF      CWD
                        PUSH DX
                        NEXT
                THEN
                SHL AX, # 1
                1PUSH           END-CODE

CODE 0<>        ( n -- f )
\  If n is not equal to 0, return TRUE.  Otherwise FALSE.
                POP AX          NEG AX          SBB AX, AX
                1PUSH           END-CODE

CODE =          ( n1 n2 -- f )
\  If n1 is equal to n2, return TRUE.  Otherwise FALSE.
                POP AX          POP CX          SUB AX, CX
                SUB AX, # 1     SBB AX, AX
                1PUSH           END-CODE

CODE <>         ( n1 n2 -- f )
\  If n1 is not equal to n2, return TRUE.  Otherwise FALSE.
                POP AX          POP CX          SUB AX, CX
                NEG AX          SBB AX, AX
                1PUSH           END-CODE

: ?NEGATE       ( n1 n2 -- n3 )  \  If n2 is negative, negate n1.
                0< IF    NEGATE   THEN   ;

CODE   U<       ( n1 n2 -- f )  
\  If unsigned n1 is less than unsigned n2, return TRUE, otherwise FALSE.
                POP CX          POP AX          SUB AX, CX
                SBB AX, AX
                1PUSH           END-CODE

CODE   U>       ( n1 n2 -- f )
\  If unsigned n1 is greater than unsigned n2, return TRUE, otherwise FALSE.
                POP AX          POP CX          SUB AX, CX
                SBB AX, AX
                1PUSH           END-CODE

CODE <          ( n1 n2 -- f )
\  If signed n1 is less than signed n2, return TRUE, otherwise return FALSE.
                POP AX          POP BX          CMP BX, AX
             >= IF
                        SUB AX, AX
                        1PUSH
                THEN
                MOV AX, # TRUE  1PUSH           END-CODE

CODE >          ( n1 n2 -- f )
\  If signed n1 is greater than signed n2, return TRUE, otherwise FALSE.
                POP AX          POP BX          CMP BX, AX
             <= IF
                        SUB AX, AX
                        1PUSH
                THEN
                MOV AX, # TRUE  1PUSH           END-CODE

CODE UMIN       ( n1 n2 -- n3 )
\ Return smaller of n1 or n2, treated as unsigned numbers.
                POP AX          POP BX          CMP BX, AX
            U<= IF
                        PUSH BX
                        NEXT
                THEN
                1PUSH           END-CODE

CODE MIN        ( n1 n2 -- n3 )
\ Return smaller of n1 or n2, treated as signed numbers.
                POP AX          POP BX          CMP BX, AX
             <= IF
                        PUSH BX
                        NEXT
                THEN
                1PUSH           END-CODE

CODE MAX        ( n1 n2 -- n3 )
\ Return larger of n1 or n2, treated as signed numbers.
                POP AX          POP BX
                CMP BX, AX
             <= IF
                        1PUSH
                THEN
                PUSH BX         NEXT            END-CODE

CODE 0MAX       ( n1 -- n3 )
\ Return larger of n1 or ZERO, treated as signed numbers.
                POP AX
                SUB BX, BX
                CMP BX, AX
             <= IF
                        1PUSH
                THEN
                PUSH BX
                NEXT            END-CODE

CODE UMAX       ( n1 n2 -- n3 )
\ Return larger of n1 or n2, treated as unsigned numbers.
                POP AX          POP BX          CMP BX, AX
            U<= IF
                        1PUSH
                THEN
                PUSH BX         NEXT            END-CODE

CODE WITHIN     ( n lo hi -- flag )
\  Returns TRUE if  lo <= n < hi .  Signed comparison
                POP DI          POP CX          POP DX
                XOR AX, AX
                CMP DX, DI
              < IF      CMP DX, CX
                     >= IF      DEC AX
                        THEN
                THEN
                1PUSH           END-CODE

CODE BETWEEN   ( n lo hi -- flag )
\  Returns TRUE if  lo <= n <= hi . Signed comparison
                XOR AX, AX      POP BX          POP CX
                POP DX
                CMP DX, BX
             <= IF      CMP DX, CX
                     >= IF      DEC AX
                        THEN
                THEN
                1PUSH           END-CODE

CODE UBETWEEN   ( n lo hi -- flag )
\  Returns TRUE if  lo u<= n u<= hi . UNsigned comparison
                POP BX          POP CX
                POP DX
                XOR AX, AX
                CMP DX, BX
            U<= IF      CMP DX, CX
                    U>= IF      DEC AX
                        THEN
                THEN
                1PUSH           END-CODE

CODE 2@         ( addr -- d )  \  Fetch a 32 bit value from addr
                POP BX
                PUSH 2 [BX]
                PUSH 0 [BX]
                NEXT            END-CODE

CODE 2!         ( d addr -- )  \ Store a 32 bit value into addr
                POP BX          POP 0 [BX]      POP 2 [BX]
                NEXT            END-CODE

CODE 2DROP      ( d -- )  \ Drop two 16 bit values from stack
                ADD SP, # 4
                NEXT            END-CODE

CODE 3DROP      ( n1 n2 n3 -- )  \ Drop 3 items from the stack.
                ADD SP, # 6
                NEXT            END-CODE

CODE 2DUP       ( d -- d d )  \  Duplicate two top items on stack.
                MOV DI, SP
                PUSH 2 [DI]
                PUSH 0 [DI]
                NEXT            END-CODE

CODE 3DUP       ( n1 n2 n3 -- n1 n2 n3 n1 n2 n3 )
\ Duplicate top 3 items on stack.
                MOV DI, SP
                PUSH 4 [DI]
                PUSH 2 [DI]
                PUSH 0 [DI]
                NEXT            END-CODE

CODE 2SWAP      ( d1 d2 -- d2 d1 )
\ Exchange top two pairs of numbers on stack.
                POP CX          POP BX
                POP AX          POP DX
                PUSH BX         PUSH CX
                2PUSH           END-CODE

CODE 2OVER      ( d2 d2 -- d1 d2 d1 )
\ Copy second pair of numbers over top pair of numbers on stack.
                MOV DI, SP      \  2
                PUSH 6 [DI]     \ 24
                PUSH 4 [DI]     \ 24 = 50
                NEXT            END-CODE

CODE D+         ( d1 d2 -- dsum )  \  Add top two double numbers on stack
                POP AX          POP DX
                POP BX          POP CX
                ADD DX, CX      ADC AX, BX
                2PUSH           END-CODE

CODE DNEGATE    ( d# -- d#' )  \  Negate double number on top of stack.
                POP AX
                POP DX
                NEG AX
                NEG DX
                SBB AX, # 0
                2PUSH
                END-CODE

CODE   S>D      ( n -- d )
\  Convert single signed number to signed double
                POP AX          CWD             XCHG DX, AX
                2PUSH           END-CODE

CODE DABS       ( d1 -- d2 )  
\  Replace the top double number with its absolute value.
                POP AX
                OR AX, AX
            0>= IF
                        1PUSH
                THEN
                POP DX
                NEG AX
                NEG DX
                SBB AX, # 0
                2PUSH
                END-CODE

CODE D2*        ( d -- d*2 )  \  32 bit left shift
                POP AX          POP DX
                SHL DX, # 1     RCL AX, # 1
                2PUSH           END-CODE

CODE D2/        ( d -- d/2 )  \ 32 bit arithmetic right shift
                POP AX          POP DX
                SAR AX, # 1     RCR DX, # 1
                2PUSH           END-CODE

CODE UD16/      ( d -- d/16 )   \ 32 bit UNSIGNED right shift 4 bits
                POP AX          POP DX
                SHR AX, # 1     RCR DX, # 1
                SHR AX, # 1     RCR DX, # 1
                SHR AX, # 1     RCR DX, # 1
                SHR AX, # 1     RCR DX, # 1
                2PUSH           END-CODE

: D-            ( d1 d2 -- d3 )
\ Subtract double number at top from second double number.
                DNEGATE D+   ;

: ?DNEGATE      ( d1 n -- d2 )  
\  If number at top is negative, negate the double number underneath.
                0< IF   DNEGATE   THEN   ;

: D0=           ( d -- f )  
\  If double number is 0.0 , return TRUE flag. Else return FALSE.
                OR 0= ;

: D=            ( d1 d2 -- f )  
\ If top two double numbers are equal, replace with TRUE flag; else FALSE.
                D-  D0=  ;

CODE DU<        ( ud1 ud2 -- Flag )
\ Unsigned compare double numbers.  If ud1 < ud2, return TRUE.  Else FALSE.
                pop dx          pop bx
                pop cx          pop ax
                sub ax, bx      sbb cx, dx      sbb ax, ax
                1push           end-code

: D<            ( d1 d2 -- f )
\ Signed compare two double numbers.  If d1 < d2, return TRUE.
                2 PICK OVER =
                IF      DU<
                ELSE  NIP ROT DROP <  THEN  ;

: D>            ( d1 d2 -- f ) 
\ Signed compare two double numbers.  If d1 > d2 , return TRUE.
                2SWAP D<   ;

: 4DUP          ( a b c d -- a b c d a b c d ) 
\ Duplicate top 4 single numbers (or two double numbers) on the stack.
                2OVER 2OVER   ;

: DMIN          ( d1 d2 -- d3 )  
\  Replace the top two double numbers with the smaller of the two (signed).
                4DUP D> IF  2SWAP  THEN 2DROP ;

: DMAX          ( d1 d2 -- d3 ) 
\  Replace the top two double numbers with the larger of the two (signed).
                4DUP D< IF  2SWAP  THEN  2DROP ;        \ 05/25/90 tjz

CODE *D         ( n1 n2 -- d# )
\ Obtain the 32 bit signed product of two 16 bit numbers.
                POP CX          POP AX          IMUL CX
                PUSH AX         PUSH DX
                NEXT            END-CODE

: M/MOD         ( d# n1 -- rem quot )
\ Divide a signed double by a signed single, leaving a remainder and 
\ quotient.
                ?DUP
                IF  dup>r  2DUP XOR >R  >R DABS R@ ABS  UM/MOD
                        SWAP R> ?NEGATE
                        SWAP R> 0<
                        IF  NEGATE OVER
                                IF  1- R@ ROT - SWAP  THEN
                        THEN    r>drop
                THEN  ;

: MU/MOD        ( ud# un1 -- rem d#quot )
\  Divide unsigned double by a single, leaving a remainder and quotient.
                >R  0  R@  UM/MOD  R>  SWAP  >R  UM/MOD  R>   ;

CODE /          ( num den --- quot )  \  Floored and signed division.
                POP BX          POP AX          CWD
                MOV CX, BX      XOR CX, DX
            0>= IF                              \ POSITIVE QUOTIENT CASE
                IDIV BX         1PUSH
            THEN
                IDIV BX         OR DX, DX
            0<> IF
                DEC AX
            THEN
                1PUSH           END-CODE

CODE /MOD       ( num den --- rem quot )
\ Divide two signed numbers and return the floored division and remainder.
                POP BX          POP AX          CWD
                MOV CX, BX      XOR CX, DX
            0>= IF
                IDIV BX         2PUSH
            THEN
                IDIV BX         OR DX, DX
            0<> IF
                ADD DX, BX      DEC AX
           THEN
                2PUSH           END-CODE

: MOD           ( n1 n2 -- rem )
\ Divide the second signed number on the stack by the top. 
\ Return the remainder (modulus).
                /MOD  DROP  ;

CODE */MOD      ( n1 n2 n3 --- rem quot )
\  Multiply n1 and n2.  Divide the result by n3. 
\  Return the remainder and quotient.
                POP BX          POP AX          POP CX
                IMUL CX         MOV CX, BX      XOR CX, DX
            0>= IF
                IDIV BX         2PUSH
            THEN
                IDIV BX         OR DX, DX
            0<> IF
                ADD DX, BX      DEC AX
            THEN
                2PUSH           END-CODE

: */            ( n1 n2 n3 -- n1*n2/n3 ) 
\  Multiply n1 by n2.  Divide the product by n3.  Return the quotient.
                */MOD  NIP  ;

: ROLL          ( n1 n2 .. nk k -- n2 n3 .. nk n1 )
\  Rotate k values on the stack, bringing the deepest to the top.
                >R R@ PICK   SP@ DUP 2+   R> 1+ 2* CMOVE>  DROP  ;

: 2ROT          ( a b c d e f - c d e f a b )   
\  Rotate the top three double numbers, bringing the deepest pair to top.
                5 ROLL  5 ROLL  ;

: PARAGRAPH     ( bytes -- paragraphs )
\ convert bytes to a whole number of paragraphs equal or greater than bytes.
                15 + U16/ ;

: DPARAGRAPH    ( dbl_bytes -- paragraphs )
\ convert dbl_bytes to a whole number of paragraphs equal or greater
\ than dbl_bytes.
                15. D+ UD16/ DROP ;

