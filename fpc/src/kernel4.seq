\ KERNEL4.SEQ   Last part of the kernel file, finishes up the compile.

\ Link this file into the FILELIST chain.

FILES DEFINITIONS

VARIABLE KERNEL4.SEQ

FORTH DEFINITIONS   META IN-META

VARIABLE #USER
\ A variable that holds the count of how many user variables are allocated.

VOCABULARY USER   USER DEFINITIONS
\ Vocabulary that holds task versions of defining words.

: ALLOT         ( n -- )
\ Allocate space in the user area for a multi-tasking word.
                #USER +!   ;

' CREATE        \ avoid recursion: leave address for ,-X in CREATE

: CREATE        ( -- )
\ Define a word that returns the address of the next available user
\ memory location.
                [ ,-X ]         \ compile addr of CREATE
                #USER @ ,
                ;USES  DOUSER-VARIABLE ,-X

: VARIABLE      ( -- )
\ Define a task type variable. Similar to the old FIG version word USER.
                CREATE   2 ALLOT   ;

: DEFER         ( -- )
\ Defines an execution vector that is local to a task.
                VARIABLE   ;USES   DOUSER-DEFER  ,-X

FORTH DEFINITIONS   META IN-META

: >IS           ( cfa -- data-address )
\ smart word converts from CFA to data field. Knows about user variables.
                DUP 1+ @ OVER >BODY +
                DUP [  [ASSEMBLER] DOUSER-VARIABLE META ] LITERAL = SWAP
                DUP [  [ASSEMBLER] DOUSER-DEFER    META ] LITERAL = SWAP
                DROP   OR IF   >BODY @ UP @ +   ELSE    >BODY   THEN   ;

: (IS)          ( cfa --- )
\ This word is compiled by IS. Sets the following DEFERred word to the
\ address on the parameter stack.
                2R@ @L >IS !   R> 2+ >R   ;

: IS            ( cfa --- )
\ Depending on STATE, either sets the following DEFERred word immediatly
\ or compiles (IS) to set it later.
                STATE @
                IF      COMPILE (IS)
                ELSE    ' >IS !
                THEN    ;       IMMEDIATE

: SELECT        ( N1 --- )
\ Select drive n1 as the current disk drive. 0=A, 1=B etc.
                14 bdos drop
                seqhandle >hndle @ -2 =
                if      -1 seqhandle >hndle !
                then    ;

: A:            ( --- )         0 SELECT ;
: B:            ( --- )         1 SELECT ;
: C:            ( --- )         2 SELECT ;
: D:            ( --- )         3 SELECT ;
: E:            ( --- )         4 SELECT ;
: F:            ( --- )         5 SELECT ;
\ Select drive A:, B:, C:, D:, E: or F: as the default drive.

: QUIT          ( -- )
\ The main loop in Forth. Gets input from the terminal and Interprets it.
\ Responds with OK if healthy and repeats the process.
                SP0 @ 'TIB !    [COMPILE] [
                BEGIN   BEGIN RP0 @ RP! STATUS QUERY  RUN
                              STATE @ NOT UNTIL ."  ok" AGAIN  ;

DEFER BOOT
\ A defered word that performs initialization before executing QUIT.

DEFER INITSTUFF   ' SEQINIT IS INITSTUFF
\ A defered word chain that performs various initialization operations
\ at Forth initial cold start time.

DEFER SEGSET      ' SETYSEG IS SEGSET
\ A DEFERed word that contain the current function used to set up the
\ segment registers at cold start time. Typically contains SETYSEG.

: WARMSTRT      ( --- )
\ The default function to be performed on a WARM start.
                FORTH
                TRUE ABORT" Warm Start" ;

DEFER WARMFUNC  ' WARMSTRT IS WARMFUNC
\ A DEFERed word that is invoked when a warm start occurs.
\ This function is also called whenever the CONTROL BREAK key is pressed.

TRUE VALUE 1STCOLD
\ A flag to tell if COLD has been called yet.

: WARM          ( -- )
\ The WARM entry point for Forth, just calles the DEFERed word WARMFUNC.
\ A WARM start is invoked whenever the CONTROL BREAK key is pressed.
                [ LABEL WARMBODY ]
                WARMFUNC ;

: COLD          ( -- )
\ The high level cold start code. For ordinary forth, BOOT should
\ initialize and pass control to QUIT.
                [ LABEL COLDBODY ]
                1STCOLD                 \ Only do this stuff once each load
                IF      SEGSET
                        VMODE.SET
                        SETCRITICAL
                        BKFLAG OFF      \ clear the SHIFT-CONTROL-BREAK flag
                        BKABLE ON       \ enable the CONTROL-BREAK able flag
                        INITSTUFF
                THEN    %OFF> 1STCOLD
                BOOT QUIT   ;

: START         ( -- )
\ Minimal default initialization. This word is stuffed into BOOT
\ when compiling the KERNEL.COM file.
                SP0 @ 'TIB !
                >IN OFF
                SPAN OFF
                #TIB OFF
                LOADING OFF
                DEFAULT INTERPRET ;

VARIABLE BIOSBKSAVE     0 ,-T
\ A double variable that holds the BIOS Control Break vector so it
\ can be restored on exit.

VARIABLE DIV0SAVE       0 ,-T
\ A double variable that holds the divide by zero interupt vector
\ so it can be restored on exit from Forth.

VARIABLE CTRLBKSAVE
\ A variable that holds the state of the low memory Control Break
\ flag so it can be restored on exit from Forth.

CODE RESTORE_VECTORS    ( --- )
\ Restores the saved vectors to their saved values.  This word is called
\ just prior to returning to DOS.
                MOV AX, CS              MOV DS, AX
                MOV DX, CS: BIOSBKSAVE
                MOV DS, CS: BIOSBKSAVE 2+
                MOV AX, # $251B
                INT $21
                MOV AX, CS              MOV DS, AX
                MOV DX, CS: DIV0SAVE
                MOV DS, CS: DIV0SAVE 2+
                MOV AX, # $2500
                INT $21
                MOV AX, CS              MOV DS, AX
                MOV DX, CTRLBKSAVE
                MOV AX, # $3301         \ Control BREAK flag status
                INT $21
                NEXT                    END-CODE

: DIV0STRT      ( --- )
\ The default function to perform when a DIVIDE by 0 trap occurs.
                TRUE ABORT" Divide OVERFLOW error" ;

DEFER DIV0FUNC  ' DIV0STRT IS DIV0FUNC
\ F-PC traps divide by 0 errors, and calls this defered word when
\ such an error is detected.

DEFER BYEFUNC   ' NOOP IS BYEFUNC
\ A defered word which normally contains NOOP, provided so you
\ can specify a function to be performed before leaving back to DOS.

: BYE           ( -- )
\ Returns control to DOS. Performs the defered word BYEFUNC before
\ actually leaving.
                RESTORE_VECTORS
                BYEFUNC
                ."  Leaving.." 0 0 BDOS  ;

: DIVIDE0       ( STATUS_reg, CS, IP, AX, BX, CX, DX, SI, BP --- )
\ The actual entry point from the divide by 0 trap, this word just
\ calls the deferd word DIV0FUNC.
                [ LABEL DIV0BODY ]
                DIV0FUNC BYE ;

LABEL DIV0BK
\ Handle the Divide by 0 interupt processing. You cannot reliably return
\ from a divide by zero interupt.
                STI
                PUSH AX
                PUSH BX
                PUSH CX
                PUSH DX
                PUSH SI
                PUSH DI
                PUSH BP
                MOV AX, # DIV0BODY 5 -
                JMP AX
                END-CODE

TRUE VALUE RESTNEXT
\ A flag to determine if we want the next code restored.

LABEL SETBRK
\ A subroutine not accessible directly from Forth that sets the
\ various interupt vectors used by Forth.
                PUSH ES
                MOV AX, CS
                MOV DS, AX
                CMP ' RESTNEXT >BODY # 0 WORD   \ If RESTNEXT is NOT = 0
            0<> IF      MOV AX, # $AD26      \ Value to restore in >NEXT
                        MOV >NEXT AX         \ Restore it
                        MOV AX, # $E0FF      \ Value to restore in >NEXT + 2
                        MOV >NEXT 2+ AX      \ Restore it
                THEN
                MOV DX, # BIOSBK
                MOV AX, # $251B         \ BIOS Break
                INT $21
                MOV DX, # DOSBK
                MOV AX, # $2523         \ DOS Break
                INT $21
                MOV DX, # 0
                MOV AX, # $3301         \ DISABLE DOS Break
                INT $21
                MOV DX, # DIV0BK
                MOV AX, # $2500         \ BIOS Break
                INT $21
                POP ES
                RET             END-CODE

LABEL SAVEVECTORS ( --- )
\ A subroutine not accessible directly from Forth that saves
\ the Divide by 0 & Cntrl Brk interrupt vectors.
                PUSH ES
                MOV AX, # $351B          \ Get the interupt vector for
                INT $21                  \ BIOS control break vector
                MOV BIOSBKSAVE BX
                MOV BIOSBKSAVE 2+ ES    \ Save old vector
                MOV AX, # $3500          \ Get the interupt vector for
                INT $21                  \ DIVIDE by 0
                MOV DIV0SAVE BX
                MOV DIV0SAVE 2+ ES      \ Save old vector
                POP ES
                MOV AX, # $3300         \ Control BREAK flag status
                INT $21
                SUB DH, DH
                MOV CTRLBKSAVE DX       \ Save it away for later restoral
                RET             END-CODE

CODE SET_VECTORS ( --- )
\ Set the CONTROL BREAK and DIVIDE by 0 traps to point to the
\ Forth provided functions, so we can handle them smoothly.
                CALL SETBRK
                NEXT            END-CODE

CODE @REL>ABS   ( a1 --- a2 )
\ Convert JMP address in a1+1 to an absolute memory address
                POP BX
                ADD BX, 1 [BX]
                ADD BX, # 3
                PUSH BX
                NEXT            END-CODE

[FORTH] ASSEMBLER

LABEL WORIG
\ An inaccessible routine. You get here from the WARM entry at offset
\ ORIGIN + 4, and get sent to the WARM colon definition.
HERE ORIGIN 6 + - ORIGIN 4 + !-T  ( WARM ENTRY )
        MOV AX, # WARMBODY 5 -
        JMP AX
        END-CODE

LABEL CORIG
\ An inaccessible routine. You get here from the COLD entry at offset
\ ORIGIN + 0, and get sent to the COLD colon definition.
\ This routine expands out the compressed Forth system to its various
\ segments.
HERE ORIGIN 3 + - ORIGIN 1+ !-T  ( COLD ENTRY )
        MOV AX, CS                      \ move CS to AX
        MOV DS, AX
        MOV SS, AX
        MOV BX, YSTART                  \ Read YSTART
        OR BX, BX 0<>                   \ If not reset, then move stuff
     IF
        ADD AX, ' #CODESEGS >BODY       \ Add CODE segments and
        ADD AX, ' #OVSEGS   >BODY       \ add OVERLAY segments and
        ADD AX, ' #LISTSEGS >BODY       \ LIST segments to get to head space.
        MOV ES, AX                      \ move head seg to ES
        MOV CX, YDP
        MOV DI, # 0                     \ Clear DI
        MOV SI, YSTART                  \ MOV YSTART to AX
        OR CX, CX 0<>                   \ if YDP was not zero (0)
        IF      CLD
                REPZ
                MOVSB                   \ move HEADS to head space
                CLD
        THEN
        MOV YSEG ES                     \ set YSEG to ES
     THEN
        MOV BX, XMOVED                  \ Has LIST been moved?
        OR BX, BX 0=                    \ If not reset, then move stuff
     IF
        MOV AX, DS                      \ move DS to AX
        ADD AX, ' #CODESEGS >BODY       \ Add CODE segments and
        ADD AX, ' #OVSEGS   >BODY       \ add OVERLAY segments to get LIST
        MOV ES, AX                      \ move head seg to ES
        MOV CX, XSEGLEN
        SHL CX, # 1                     \ MULTIPLY BY 16 DECIMAL
        SHL CX, # 1
        SHL CX, # 1
        SHL CX, # 1
        MOV DI, # 0                     \ Clear DI
        MOV SI, DPSTART                 \ MOV source offset to SI
        OR CX, CX 0<>                   \ if DPSTART was not zero (0)
        IF      CLD             \ Forward move, NOT backwards this time.
                REPZ
                MOVSB                   \ move LISTS to LIST space
                CLD
        THEN
        MOV XSEG ES                     \ set XSEG to ES
     THEN

\ The following few instructions patch two ADD instructions in KERNEL1, so
\ we can do an ADD IMMEDIATE rather than an ADD MEMORY in NEST and DODOES.
        MOV ES, XSEG                    \ Initialize ES in case we haven't??
        MOV NESTPATCH 1+ ES             \ Patch NEST   ADD instruction
        MOV DOESPATCH 1+ ES             \ Patch DODOES ADD instruction

        CALL SAVEVECTORS                \ Save existing vectors
        MOV ' RESTNEXT >BODY # TRUE WORD \ We want NEXT restored
        CALL SETBRK                     \ Install Break vectors &
                                        \ restore NEXT
        MOV AX, ' #CODESEGS >BODY
        SUB AX, # 1                     \ One less than max
        SHL AX, # 1
        SHL AX, # 1
        SHL AX, # 1
        SHL AX, # 1
        SUB AX, ' #OVBYTES  >BODY       \ reduce by OVERLAY bytes
        MOV ' LIMIT 3 + AX              \ LIMIT
        SUB AX, # 10
        MOV ' FIRST 3 + AX              \ FIRST = LIMIT - 10h
        SUB AX, # 10
        MOV RP, AX                      \ RP = FIRST - 10h
        MOV BX, # RP0
        ADD BX, UP
        MOV 0 [BX], RP                  \ RP0 = RP
        SUB AX, # 250
        MOV 'TIB AX                     \ TIB = RP - 250 DECIMAL
        MOV BX, # SP0
        ADD BX, UP
        MOV 0 [BX], AX                  \ SP0 = TIB
        MOV SP, AX                      \ SP = TIB
        MOV ' 1STCOLD >BODY # TRUE WORD \ Make COLD to its initialization
        MOV AX, COLDBODY 2-
        ADD AX, XSEG
        MOV ES, AX
        MOV IP, # 0
        NEXT
        END-CODE
  IN-META

\ Here we initialize the USER table with its default values.
HERE UP !-T     ( SET UP USER AREA )
       0 ,      ( TOS )
       0 ,      ( ENTRY )
       0 ,      ( LINK )
       0 ,      ( ES0 )
INIT-R0 256 - , ( SP0 )
 INIT-R0 ,      ( RP0 )
       0 ,      ( DP )          ( Must be patched later )
       0 ,      ( OFFSET )
      10 ,      ( BASE )
       0 ,      ( HLD )
   FALSE ,      ( PRINTING )
' (EMIT) ,      ( EMIT )
' (KEY?) ,      ( KEY? )
' (KEY)  ,      ( KEY  )
' (TYPE) ,      ( TYPE )
' (TYPEL) ,     ( TYPEL )

0 , 0 , 0 , 0 , 0 ,             \ room for 10 additional USER variables
0 , 0 , 0 , 0 , 0 ,

: DEPTH         ( -- n )
\ Returns the number of items on the parameter stack.
                SP@ SP0 @ SWAP - 2/   ;

VARIABLE MAX.S
\ A variable that holds the maximum depth to be displayed of the
\ data stack with .S following.

: .S            ( -- )
\ Displays the contents of the parameter stack non destructively.
\ Very useful when debugging.
                DEPTH 0< ABORT" Stack UNDERFLOW !! "
                DEPTH ?DUP MAX.S @ 1 < IF 4 MAX.S ! THEN
                IF      DUP ."  [" 1 .R ." ]" 0 SWAP 1- MAX.S @ 1- MIN
                        DO I PICK 7 U.R SPACE -1 +LOOP
                ELSE    ."  Stack Empty. "  THEN ;

: %.ID          ( nfa -- )
\ Display the variable length name whose name field address is on the
\ stack. If it is shorter than its count, it is padded with underscores.
\ Only valid Ascii is typed.
                DUP 1+ DUP YC@ ROT YC@ 31 AND 0
               ?DO      DUP 127 AND FEMIT 128 AND
                        IF   ASCII _ 128 OR   ELSE  1+ DUP YC@  THEN
                LOOP    2DROP SPACE ;

DEFER .ID       ' %.ID IS .ID   \ defer to allow for COLORIZER
\ A defered word. Display the variable length name whose name field
\ address is on the stack. If it is shorter than its count, it is
\ padded with underscores. Only valid Ascii is typed.

: DUMP          ( addr len -- )
\ A primitive little dump routine to help you debug after you have
\ changed the system source and nothing works any more.
              0 DO   CR DUP 6 .R SPACE  16 0 DO   DUP C@ 3 .R 1+   LOOP
            16 +LOOP   DROP   ;

: RECURSE       ( -- )
\ Makes the definition this word is used in call itself at the
\ point where it is used. ie. "RECUSION"
                LAST @ NAME> X,  ;  IMMEDIATE

: H.            ( N1 --- )
\ Display the unsigned number in hex, with trailing blank. Does not
\ change the number base.
                BASE @ >R HEX U. R> BASE ! ;

VARIABLE LMARGIN    0 LMARGIN !-T
\ The left margin setting used by ?LINE, ?CR.

VARIABLE RMARGIN   70 RMARGIN !-T
\ Controls the right margin, used by ?LINE, ?CR.

VARIABLE TABSIZE    8 TABSIZE !-T
\ Controls the TAB increment for TAB. Default is 8.

: ?LINE         ( n -- )
\ Break the line at the cursor if there are less than n1 characters
\ till RMARGIN is encountered.
                #OUT @ +  RMARGIN @ > IF CR LMARGIN @ SPACES THEN ;

: ?CR           ( -- )
\ Break the line at the cursor, if we have reached the right margin
\ as specified by RMARGIN.
                0 ?LINE  ;

: TAB           ( --- )
\ Print spaces to get to the next TAB increment as specified by
\ TABSIZE.
                #OUT @ TABSIZE @ MOD TABSIZE @ SWAP - SPACES ;

: \             ( --- )
\ Comment till the end of this line.
                #TIB @ >IN ! ; IMMEDIATE

CODE SET-CURSOR ( n1 --- )
\ Set the cursor shape to value n1
                POP CX
                MOV AH, # 1
                PUSH SI         PUSH BP
                INT $10
                POP BP          POP SI
                NEXT C;

: GET-CURSOR    ( --- n1 )      \ return n1 the shape of the cursor.
                0 $460 @L ;

\ : SS            ( | <name> )
\                 ' >BODY @ +XSEG 0 ;

\ : NN            ( SEG OFF --- SEG OFF+2 )
\                 2DUP @L DUP H. >NAME .ID 2+ ;

\ The :RESOLVES word resolves forward references in LIST space, while
\ the  RESOLVES word resolves forward reverences in CODE space.
\ It does not matter whether the word you are resolving is a CODE word
\ or a COLON definitions, what matters is where it is being resolved which
\ is typically in LIST space not CODE space.  All this to say you should
\ normally use :RESOLVES rather than RESOLVES to resolve forward reverences.

\ Resolve some forward references.

' (.")                            :RESOLVES <(.")>
' (")                             :RESOLVES <(")>
' (;CODE)                         :RESOLVES <(;CODE)>
' (;USES)                         :RESOLVES <(;USES)>
' (IS)                            :RESOLVES <(IS)>
' (ABORT")                        :RESOLVES <(ABORT")>
 [ASSEMBLER] >NEXT    META         RESOLVES <VARIABLE>
 [ASSEMBLER] DOUSER-DEFER META     RESOLVES <USER-DEFER>
 [ASSEMBLER] DOUSER-VARIABLE META  RESOLVES <USER-VARIABLE>

' DEFINITIONS                     :RESOLVES DEFINITIONS
' [                               :RESOLVES [
' ?MISSING                        :RESOLVES ?MISSING
' QUIT                            :RESOLVES QUIT
' .ID                             :RESOLVES .ID
' @REL>ABS                        :RESOLVES @REL>ABS
' >IS                             :RESOLVES >IS
' >BODY                           :RESOLVES >BODY
' 0POINTERS                       :RESOLVES 0POINTERS

\ Fill in some deferred words.
' CRLF          IS CR
' CR            IS STATUS
' START         IS BOOT
' (PRINT)       IS PEMIT
' (CONSOLE)     IS CONSOLE

\ Set CONTEXT and CURRENT to FORTH.
' FORTH >BODY-T CURRENT !-T
' FORTH >BODY-T CONTEXT !-T

HERE-T  DP UP @-T + !-T               \ INIT USER DP
#USER-T @ #USER !-T                   \ INIT USER VAR COUNT
TRUE  CAPS !-T                        \ SET TO IGNORE CASE
TRUE WARNING !-T                      \ SET TO ISSUE WARNINGS
31 WIDTH !-T                          \ 31 CHARACTER NAMES
VOC-LINK-T @ VOC-LINK !-T             \ INIT VOC-LINK
PHEAD-T @ PHEAD !-T                   \ INIT PHEAD

\ Now display the statistics for this compile.

CR
CR .( Unresolved references: )          CR   .UNRESOLVED ?NEWPAGE
CR .(     Statistics: )
CR .( Last  Host Address:        )      [FORTH] HERE U.
CR .( First Target Code Address: )      META 256 THERE U.
CR .( Last  Target Code Address: )      META HERE-T THERE U.
                                        META 256 THERE          \ start addr
                                        SVXSEG     DPSTART !-T
                                        HERE-X DROP 1+
                                        0 XS: DROP - XSEGLEN !-T
CR .( CODE space used:           )      HERE-T U.
CR .( LIST space used:           )      HERE-X SWAP 0 XS: DROP - 16 * + U.
CR .( HEAD space used:           )      HERE-Y U.
                                        HERE-X DROP 1+ 0 XS: DROP -
                                        DUP 16 * ALLOT-T DROP
                                        0 XDP !-T
                                      SVYSEG DUP
                                        YSTART !-T
                                        0 XMOVED !-T
                                      HERE-Y +
                                        HERE-Y YDP !-T
                                        THERE ONLY FORTH ALSO META ALSO
                                      CODEBYTES OVER -
CR .( Free Target Program room:  )      U.
                                        SP@ HERE -
CR .( Free Symbol Table bytes:   )      U.

.COMPSTAT

( A1 N1 --- )   ZSAVE KERNEL.COM        \ Save the KERNEL.COM file.

ONLY FORTH ALSO DEFINITIONS

CR .( Now type EXTEND <enter> at the DOS prompt.)
CR

