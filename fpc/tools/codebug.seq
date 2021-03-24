\\ CODEBUG.SEQ          A CODE debugger for F-PC        by Tom Zimmer
       ( Modifications for floating point by Bob Smith )

  This file contains a simple debugger for F-PC to allow debugging
CODE routines. Don't expect TOO MUCH from this debugger, it is still
in quite primitive form. It is useful though.

  The debugger can be invoked directly by:

        TRACE <forth_name> <enter>

  Each subsequent enter key press causes the debugger to step one
instruction through <forth_name>. Limited operations are supported while
in the debugger, read the screen while debugging and press F1 for some help.

  To set a break point that will invoke the debugger when a particular
word is executed, use:

        BREAKAT <forth_name> <enter>

  The first time this word is executed, the code debugger will be started.
If your break point is not ever executed, you should execute UNBREAK
before leaving F-PC to remove the break point from memory.

Modifications by Bob Smith:
        The status register of the numeric co-processor (80x87) has
        been added.  I suggest that you also load FDIS86.SEQ to obtain
        the floating point disassembly capability (assuming that you
        have the hardware floating point unit (80x87 or equivalent).

 ***************************************************************************

  To make a debugger for CODE, we needed to make an interrupt handler for
INT 1, and then enable the single step status flag bit TF.

{

\ **************************************************************************
\ Load the disassembler, we need it in the debugger
\ **************************************************************************

DEFINED FLT_DEBUG NIP
#IF     cr .( Already loaded!) \S
#THEN
ALSO FORTH

\ If you want to use the floating point extentions to this debugger when
\ the hardware floating point package is not loaded, then you will need
\ to manually edit the following statment that makes FLT_DEBUG TRUE.

\- FVTOS        FALSE CONSTANT FLT_DEBUG        \ if no hardware floating
\+ FVTOS         TRUE CONSTANT FLT_DEBUG        \ auto include if there
                                                \ is hardware floating point

NEEDS DIS8086.SEQ

FLT_DEBUG #IF
        NEEDS FDIS86.SEQ
        NEEDS FASSEM.SEQ
        NEEDS FLTAUX.SEQ
        ( dis8086 ) symbolic off

    : WT-GETFPSW
        fpsw> ;

#ELSE
    : .FPSTACK ;
    : WT-GETFPSW 0 ;
    : FPCW> 0 ;
    : FPSW> 0 ;
    variable sigdigits
#THEN


WARNING OFF

\ **************************************************************************
\ variables to hold all needed registers
\ **************************************************************************

VARIABLE DEBUGIP
VARIABLE DEBUGCS
VARIABLE DEBUGFLAGS
VARIABLE DEBUGAX
VARIABLE DEBUGBX
VARIABLE DEBUGCX
VARIABLE DEBUGDX
VARIABLE DEBUGBP        VARIABLE FORTHBP
VARIABLE DEBUGSI        VARIABLE FORTHSI
VARIABLE DEBUGDI        VARIABLE FORTHDI
VARIABLE DEBUGES        VARIABLE FORTHES
VARIABLE DEBUGSS        VARIABLE FORTHSS
VARIABLE DEBUGSP        VARIABLE FORTHSP
VARIABLE DEBUGDS        VARIABLE FORTHDS
VARIABLE DEBUGHX
DEBUGHX OFF
10 ARRAY DEBUGSIG
\ **************************************************************************
\ words to allow setting the debugging registers
\ **************************************************************************

: REG!  ( a1 | <name> -- )  \ defining word to make register assignment words
        create , does> @ ! ;

DEBUGCS    REG! =CS
DEBUGIP    REG! =IP
DEBUGSS    REG! =SS
DEBUGSP    REG! =SP
DEBUGDS    REG! =DS
DEBUGES    REG! =ES
DEBUGAX    REG! =AX
DEBUGBX    REG! =BX
DEBUGCX    REG! =CX
DEBUGDX    REG! =DX
DEBUGSI    REG! =SI
DEBUGDI    REG! =DI
DEBUGBP    REG! =BP
DEBUGFLAGS REG! =FL

: REG@   ( a1 | <name> -- )      \ defining word to make register fetch words
        create , does> @ @ ;

DEBUGCS REG@ CS-@
DEBUGIP REG@ IP-@
DEBUGSS REG@ SS-@
DEBUGSP REG@ SP-@
DEBUGDS REG@ DS-@
DEBUGES REG@ ES-@
DEBUGAX REG@ AX-@
DEBUGBX REG@ BX-@
DEBUGCX REG@ CX-@
DEBUGDX REG@ DX-@
DEBUGSI REG@ SI-@
DEBUGDI REG@ DI-@
DEBUGBP REG@ BP-@

2VARIABLE BREAK_SAVE
          break_save off

\ **************************************************************************
\ Some handy debugging utilities
\ **************************************************************************

FLT_DEBUG
#IF
    : .FPSTACK ( -- )
        bcr (wait) debughx @
        IF      h.npustk
        ELSE    .npustk
        THEN ;

ROWS 4 - CONSTANT DBBOT

#ELSE

ROWS 6 - CONSTANT DBBOT

#THEN

2        CONSTANT DBTOP
0        VALUE    DBOFF
0        VALUE    DBTO
0        VALUE    DBSAVE
0        VALUE    KEYSAVE
0        VALUE    SPSAVE

: H.4BL         ( n1 -- )       \ display n1 in four digit hex
                $10 save!> base
                0 <# # # # # #> space type space
                restore> base ;

' NO-NAME >NAME CONSTANT [NO-NAME]

: SP>COL0   ( n1 -- )
        #out @ - 0max spaces ;

: DBEEOL   ( -- )
        58 sp>col0 ;

: ?.CFA   ( a1 -- )
        >name dup [no-name] <>
        IF      dup .id
        THEN    drop ;

: DEBUG_DEPTH   ( -- n1 )
        sp-@ sp0 @ swap - 2/  ;

: BP-DEPTH-DEBUG        ( -- n )        \ depth of return stack
        RP0 @  bp-@ -  2/
        10 - 0max ;
                \ compensate for the return stack effect of this definition
                \ seems to be o.k. now

: DEBUG.S       ( -- )
        savecursor cursor-off
        0 dbtop cols 1- dbtop
        flt_debug
        IF      3
        ELSE    2
        THEN
        + box
        debug_depth 0<
        IF      ."  \2 Data Stack INVALID !! "
                >attrib2 cols 2- sp>col0 >norm
        ELSE    debug_depth ?dup
                IF      dup ."  [" 1 .r ." ]" 0 swap 8 min 1-
                        DO      sp@ sp-@ swap - 2/ I + pick
                                7 u.r space
                        -1 +LOOP
                ELSE    ."  Stack Empty. "
                THEN    cols 1- sp>col0
        THEN
        flt_debug
        IF      .fpstack
        THEN
        restcursor ;

: .DBHEADER     ( -- )
        cls
        0 dbtop 2- at
        ." \3 Enter or Space \1 = single step instruction
        ." \2 ESC \1 = Quit debugging \2 F1 \1 = Help "
        cr
        ." \1 Use  to select line to (G)o to. "
        ." \1 Press (R) to change registers. " ;

: .DBFOOTER     ( -- )
        0 dbbot 1+ at
        ." \1 Type \`\3STEPS\1\` to restart debugger.  "
        >attrib1 cols sp>col0 >norm cr ;

: .BYTES        ( a1 n1 -- )
        0max bounds
        ?DO     I c@ 0 <# # # #> type space
        LOOP    ;

\ **************************************************************************
\ Display current instruction followed by data stack
\ **************************************************************************

FLT_DEBUG #IF
        dis8086 also
#THEN

0 value ipprev
0 value ipprev2

: .CINST         ( -- )          \ display current instruction
        save> base hex
        ip-@ ?.cfa 11 sp>col0
        cs-@ =seg
        ip-@ dup [ dis8086 ] cp ! =: ipprev
        >rev
        ip-@ h.4bl inst 44 sp>col0
        ip-@ cp @ over - 5 min .bytes dbeeol >norm
        restore> base ;

WARNING ON

: .NINST        ( n1 -- )
        save> base hex
        [ dis8086 ] cp @ ?.cfa 11 sp>col0
        1+ dboff =
        IF      cp @ =: dbto >attrib3
        THEN
        cp @ dup>r h.4bl inst 44 sp>col0
        r> cp @ over - 5 min .bytes dbeeol >norm
        restore> base ;

: .PINST        ( -- )
        ipprev 0=
        IF      dbeeol
        ELSE    save> base hex
                cs-@ =seg
                ipprev dup [ dis8086 ] cp ! =: ipprev2
                cp @ ?.cfa 11 sp>col0
                cp @ dup>r h.4bl inst 44 sp>col0
                r> cp @ over - 5 min .bytes dbeeol >norm
                restore> base
        THEN    ;

previous

\ **************************************************************************
\ Display the processor registers
\ **************************************************************************

: .BIN16  ( val -- )
        0 <# 16 0
        DO      # LOOP
        #> type ;

: .CW   ( -- )
        flt_debug
        IF      ."  \1CW" fpcw> h.4bl
        ELSE    9 spaces
        THEN ;

: .SW   ( -- )
        flt_debug
        IF      ."  \1SW" fpsw> h.4bl
        ELSE    9 spaces
        THEN ;

: .REGS  ( -- )
        savecursor cursor-off
        60 dbtop 4 + 79 dbtop 17 +
        flt_debug
        IF      2 +
        THEN
        box
        ."  \3 Tom's Debugger "                                 bcr
        ."  \1CS" cs-@ h.4bl ."  \1IP" ip-@ h.4bl               bcr
        ."  \1DS" ds-@ h.4bl ."  \1SI" si-@ h.4bl               bcr
        ."  \1ES" es-@ h.4bl ."  \1DI" di-@ h.4bl               bcr
        ."  \1SS" ss-@ h.4bl ."  \1SP" sp-@ h.4bl               bcr
        ."           \1BP" bp-@ h.4bl                           bcr
        ."  \1AX" ax-@ h.4bl 09 SPACES                          bcr
        ."  \1BX" bx-@ h.4bl ."  \1FL" debugflags @ h.4bl       bcr
        ."  \1CX" cx-@ h.4bl 09 SPACES                          bcr
        ."  \1DX" dx-@ h.4bl 09 SPACES                          bcr
        ."  ----ODITsz-a-p-c"                                   bcr
        2 save!> base
        space
        debugflags @ .bin16
        flt_debug
        IF      bcr ."  B3SSS210I-PUOZDI"                       bcr
                space wt-getfpsw .bin16
        THEN
        restore> base
        restcursor ;

\ **************************************************************************
\ This is the single step "receiver". It saves the debugging registers
\ and restores F-PC's registers then goes back to Forth.
\ **************************************************************************

LABEL INT1      ( -- )          \ preserve all registers
        push ds
        push ax
        mov ax, cs: forthds
        mov ds, ax
        pop debugax
        pop debugds
        pop debugip
        pop debugcs
        pop ax
        and ax, # $FEFF         \ clear TF flag bit
        mov debugflags ax
        mov debugbx bx
        mov debugcx cx
        mov debugdx dx
        mov debugbp bp  mov bp, forthbp
        mov debugsi si  mov si, forthsi
        mov debugdi di  mov di, forthdi
        mov debuges es  mov es, forthes
        mov debugss ss  mov ss, forthss
        mov debugsp sp  mov sp, forthsp
        cld
        next
        END-CODE

\ **************************************************************************
\ This is the break point "receiver". It saves the debugging registers
\ and restores F-PC's registers then goes to "STEPS".
\ **************************************************************************

DEFER DO_STEPS
2VARIABLE INT3SAVE

LABEL INT3      ( -- )          \ preserve all registers
        push ds
        push ax
        mov ax, cs: forthds
        mov ds, ax
        pop debugax
        pop debugds
        pop ax
        dec ax                  \ backup one byte to break point
        mov debugip ax
        pop debugcs
        pop ax
        and ax, # $FEFF         \ clear TF flag bit
        mov debugflags ax
        mov debugbx bx
        mov debugcx cx
        mov debugdx dx
        mov bx, break_save
        mov ax, break_save 2+
        mov 0 [bx], al          \ restore break point
        mov break_save # 0 word \ clear break point variable
        mov debugbp bp  mov bp, forthbp
        mov debugsi si  mov si, forthsi
        mov debugdi di  mov di, forthdi
        mov debuges es  mov es, forthes
        mov debugss ss  mov ss, forthss
        mov debugsp sp  \ mov sp, forthsp
        mov cx, cs
        mov ds, cx
        mov dx, cs: int3save            \ restore interrupt three
        mov ds, cs: int3save 2+
        mov ax, # $2503
        int $21
        mov ax, cs
        mov ds, ax
        mov ax, # ' do_steps            \ goto "STEPS"
        cld
        jmp ax
        END-CODE

\ **************************************************************************
\ Routines to save, set & restore the number one & three interrupt vectors.
\ **************************************************************************

2VARIABLE INT1SAVE               \ a place to save the interrupt one vector

CODE SAVE_INT#  ( n1 --- )      \ save the current contents of interrupt one
        pop bx
        push es
        mov ax, # $3500
        or al, bl
        int $21
        mov int1save bx
        mov int1save 2+ es       \ save old vector
        pop es
        next
        END-CODE

CODE SAVE_INT3  ( --- )      \ save the current contents of interrupt three
        push es
        mov ax, # $3503
        int $21
        mov int3save bx
        mov int3save 2+ es       \ save old vector
        pop es
        next
        END-CODE

CODE SET_INT#   ( n1 --- )      \ set interrupt one to our interrupt handler
        pop bx
        push es
        mov ax, cs
        mov ds, ax
        mov dx, # int1
        mov ax, # $2500
        or al, bl
        int $21
        pop es
        next
        END-CODE

CODE SET_INT3   ( --- )      \ set interrupt three to our interrupt handler
        push es
        mov ax, cs
        mov ds, ax
        mov dx, # int3
        mov ax, # $2503
        int $21
        pop es
        next
        END-CODE

CODE REST_INT#  ( n1 --- )      \ restore the contents of interrupt one
        pop bx
        mov cx, cs
        mov ds, cx
        mov dx, cs: int1save
        mov ds, cs: int1save 2+
        mov ax, # $2500
        or al, bl
        int $21
        mov ax, cs
        mov ds, ax
        next
        END-CODE

CODE REST_INT3  ( --- )      \ restore the contents of interrupt three
        mov cx, cs
        mov ds, cx
        mov dx, cs: int3save
        mov ds, cs: int3save 2+
        mov ax, # $2503
        int $21
        mov ax, cs
        mov ds, ax
        next
        END-CODE

\ **************************************************************************
\ initiate one single instruction step. Swaps registers, sets up the
\ hardware stack with processor status, code segment, and instruction
\ pointer then does an IRET to return to do a single step. The TF flag
\ is set in the status register to make the processor immediately perform
\ an INT1 after a single instruction has been executed. Execution then
\ returns to INT1 above, and consequently back to Forth.
\ **************************************************************************

CODE ONE_STEP   ( -- )          \ single step through one instruction as
                                \ already setup in the debugging recisters
        mov forthsp sp
        mov forthss ss
        mov forthbp bp
        mov forthsi si
        mov forthdi di
        mov forthds ds
        mov forthes es
        cmp debugsp # 0         \ give a default if needed.
     0= IF      mov debugsp sp
                mov debugss ss
                mov debugds ds
                mov debugcs cs
                pushf
                pop ax
                and ax, # $FEFF         \ clear TF flag bit
                mov debugflags ax
        THEN
        mov bx, debugbx
        mov cx, debugcx
        mov dx, debugdx
        mov bp, debugbp
        mov si, debugsi
        mov di, debugdi
        mov ss, debugss
        mov es, debuges
        mov sp, debugsp
        mov ax, debugflags
        or ax, # $100           \ set TF bit in flags
        push ax
        push debugcs
        push debugip
        mov ax, debugax
        mov ds, debugds
        iret
        END-CODE

CODE ONE_BREAK  ( -- )          \ go till the breakpoint we just installed
        mov forthsp sp
        mov forthss ss
        mov forthbp bp
        mov forthsi si
        mov forthdi di
        mov forthds ds
        mov forthes es
        mov bx, debugbx
        mov cx, debugcx
        mov dx, debugdx
        mov bp, debugbp
        mov si, debugsi
        mov di, debugdi
        mov ss, debugss
        mov es, debuges
        mov sp, debugsp
        mov ax, debugflags
        and ax, # $FEFF         \ CLEAR TF bit in flags
        push ax
        push debugcs
        push debugip
        mov ax, debugax
        mov ds, debugds
        iret
        END-CODE

CODE TRACE_DONE ( -- )
        mov bx, debugbx
        mov cx, debugcx
        mov dx, debugdx
        mov bp, debugbp
        mov si, debugsi
        mov di, debugdi
        mov ss, debugss
        mov es, debuges
        mov sp, debugsp
        mov ax, debugflags
        and ax, # $FEFF           \ CLEAR TF bit in flags
        push ax
        push debugcs
        push debugip
        mov ax, debugax
        mov ds, debugds
        iret
        END-CODE

\ ***************************************************************************
\ initialize the Forth registers, so they will be valid when the break point
\ occurs.
\ ***************************************************************************

: SET_FREGS  ( -- )                  \ give forth registers some defaults
        sp@     forthsp !
        ?cs:    forthss !
        ?cs:    forthds !
        rp@     forthbp !
        0       forthdi !
        0       forthsi !
        ['] quit >body @ xseg @ + forthes ! ;

\ **************************************************************************
\ Set the single step interrupt, perform a single instruction step, and
\ then restore the single step interrupt.
\ **************************************************************************

: SINGLE_STEP   ( -- )  \ perform one instruction step, and display
                        \ registers with next instruction to be traced.
        1 save_int#             \ save existing interrupt vector
        1 set_int#              \ set to out interrupt routine
        one_step                \ do a single step trace of one inst
        1 rest_int#             \ restore the interrupt vector
        ;

: BREAK_POINT   ( -- )                  \ break point to offset specified
        dbto c@ =: dbsave
        dboff
        IF      $CC dbto c!     \ only break if not zero
        THEN
        3 save_int#
        3 set_int#
        one_break
        3 rest_int#
        dbsave dbto c!          \ restore program byte
        ip-@ 1- =ip             \ backup program counter one byte
        off> dboff ;            \ reset break point offset

\ ***************************************************************************
\ Break point control words, allow setting, removing, and displaying
\ the current break point.
\ ***************************************************************************

: UNBREAK       ( -- )                  \ remove the break point
        break_save @ ?dup
        IF      break_save 2+ @ swap c!         \ restore break point
                break_save off                  \ clear break_save
                rest_int3                       \ restore vector
        THEN    defers byefunc ;

' UNBREAK IS BYEFUNC    \ make break point removal automatic

: ABREAKAT      ( a1 -- )               \ install a break point into address
        unbreak                 \ restore previous if needed
        set_fregs               \ give Forth registers a default
        save_int3               \ save interrupt three
        set_int3                \ set interrupt three
        dup break_save !
        dup c@ break_save 2+ !  \ save break point
        off> ipprev
        cr ." Break point set"
        $CC swap c! ;           \ set break point

: BREAKAT       ( | <name> -- )         \ install a break point
        ' abreakat ;

' BREAKAT ALIAS XX              \ xx is an alias for breakat

: .BREAK        ( -- )                  \ display the current break point
        break_save @ ?dup cr
        IF      ." Break point set in " >name .id
        ELSE    ." No break point set"
        THEN    ;

\ **************************************************************************
\ show the current registers, and a series of instructions as they will
\ be executed.
\ **************************************************************************

: SHOW_DEBUG    ( -- )
        savecursor cursor-off
        .regs
        debug.s
        0 dbtop 4 + 59 dbbot box
        ." \1 Name      Addr   Instruction              Data          "
        bcr .pinst bcr .cinst bcr
        dbbot dbtop 5 + - 3 - 0
        DO      i .ninst bcr
        LOOP    restcursor ;

: SET_REGISTER  ( -- )
        ipprev2 =: ipprev
        savecursor
        0 dbbot 1+ 2dup at cols 1- sp>col0 at
        sp@ >r sp-@ sp!
        ." \1 {in the form $23 =AX } command: "
        query interpret
        sp@ =sp r> sp!
        0 dbbot 1+ 2dup at cols 1- sp>col0 at
        restcursor ;

: UP_DBLINE     ( -- )
                ipprev2 =: ipprev
( up arrow )    dboff 1- 0max =: dboff ;

: DOWN_DBLINE   ( -- )
                ipprev2 =: ipprev
( down arrow )  incr> dboff ;

: SET-SIG   ( -- )
        0 dbbot 1+ 2dup at
        ." Significant digits = "
        debugsig 5 blank
        debugsig 1+ 3 expect
        2dup at 30 spaces at
        span @
        IF      span @ debugsig c!
                debugsig number?
                IF      drop dup
                        IF      1 max 17 min sigdigits !
                                false debughx !
                        ELSE    drop true debughx !
                        THEN
                ELSE    2drop
                THEN
        ELSE    false debughx !
        THEN ;

: SET-BRK   ( -- )
        0 dbbot 1+ 2dup at
        ." Break at: "
        debugsig 10 blank
        debugsig 1+ 6 expect
        2dup at 30 spaces at
        span @
        IF      span @ debugsig c!
                $10 save!> base
                debugsig count upper
                debugsig number?
                IF      drop !> dbto
                        2 !> dboff
                        break_point
                ELSE    2drop
                THEN
                restore> base
        THEN ;

\ **************************************************************************
\ Additional MINI help for the debugger.
\ **************************************************************************

: SHOW_HELP     ( -- )
        ipprev2 =: ipprev
        savecursor cursor-off savescr
        0 5 59 20 box&fill
        bcr ."   Debugger commands:" bcr
        bcr ." \S04\1 SPACE \0 = Do a single instruction"
        bcr ." \S04\1 ESC \0   = Done, terminate debugger"
        bcr ." \S04\1 D \0     = Done, continue execution from break point"
        bcr ." \S04\1 G \0     = Go till hilighted line"
        bcr ." \S04\1 R \0     = Set a Register"
        bcr ." \S04\1 S \0     = Significant digits of floating point stack"
        bcr ." \S04\1 B \0     = Go till new breakpoint address reached"
        bcr
        bcr ."   \1 Press ESC to continue, or SPACE for more help "
        key $1B <>
        IF
             0 5 59 19 box&fill
             bcr ."   Using R, \`<number> =AX\` will set AX to <number>."
             bcr ."   Registers that can be set are:"
             bcr ."     =CS =DS =ES =SS =IP =SI =DI =SP =BP =AX =BX =CX =DX"
             bcr
             bcr ."   TRACE gives registers CS, DS, SS, ES, IP, and AX"
             bcr ."   default values. Use TRACE once, then use 'R' to set"
             bcr ."   registers to your desired values."
             bcr
             bcr ."   Using S value of 0 will give raw hex of 8087 stack"
             bcr ."   Using S followed by Enter will restore decimal display"
             bcr
             bcr ." \S10\1 Press ANY key to continue " key drop
        THEN
        restscr restcursor ;

\ **************************************************************************
\ the main trace loop, walks through instructions until the ESC key is
\ pressed.
\ **************************************************************************

: DO_DBKEY      ( c1 -- f1 )
                CASE
( terminate )   $1B OF                          true    endof
( enter )       $0D OF  single_step             false   endof
( space )       $20 OF  single_step             false   endof
( up arrow )    $C8 OF  up_dbline               false   endof
( down arrow )  $D0 OF  down_dbline             false   endof
( help )        $BB OF  show_help               false   endof
                upc                 \ remaining tests are case insensitive
( Go )          'G' OF  break_point             false   endof
( register set) 'R' OF  set_register            false   endof
( debug done )  'D' OF  0 20 at trace_done      false   endof
( significance) 'S' OF  set-sig                 false   endof
                        ipprev2 =: ipprev
( breakpoint )  'B' OF  set-brk                 false   endof
( all others )          drop beep               false
                ENDCASE ;

: XCHG? ( -- flag )     \ TRUE if depth is negative                      \ akg92sep29
                        \ and bp-depth is to big
        debug_depth     0< >R
        bp-depth-debug  100 >
        R> AND ;

: STEPS         ( -- )
        .dbheader
        0 23 at
        rp@     =bp
        rp@ 80 - rp!            \ move return stack out of the way
                                \ 80 is upto 40 stack items
        sp@     =sp             \ set debugger to Forth stack
        sp@ 20 - sp!            \ 20 is upto 10 stack items
        BEGIN   show_debug
                key do_dbkey
        UNTIL
        XCHG?           \ did we use  XCHG  and stacks are  INvalid      \ akg92sep29
        IF      sp-@   2 AND                    \ EVEN number of items ?
                IF      \ return stack will be corrupted in some way     \ akg92sep29
                        WARM                    \ we better call ùwarmfunc
                ELSE    sp-@ rp!
                        debugbp @ sp!           \ we exchange stacks !!! \ akg92sep29
                        \ if we do some very nasty things  WARM  is better
                        \ but in 99% this is  fine  and  comfortable
                THEN
        ELSE    sp0 @  sp-@ -  2/  0>           \ is stack empty?
                IF      sp-@ sp!                \ if not, restore it
                ELSE    sp0 @  sp!              \ else clear stack
                THEN debugbp @  rp!
        THEN    .dbfooter ;

' STEPS   IS DO_STEPS     \ link into break point handler

\ **************************************************************************
\ setup for tracing a series of instructions, and call STEPS.
\ **************************************************************************

: TRACE         ( -<name>- )
        ' dup abreakat execute ;

PREVIOUS

cr cr
.( Invoke debugger directly by typing : ) cr cr
.(       TRACE <forth_name> <enter> ) cr cr
.(   or indirectly by typing: ) cr cr
.(       BREAKAT <forth_name> <enter> ) cr cr

\s
\ ***************************************************************************
\ some test words for the debugger
\ ***************************************************************************

code TST        ( -- )
        mov ax, # 23
        push ax
        next
        END-CODE

cr .( try:  TRACE TST <enter>  )

: ++ + ;

: TEST 2 3 ++ . ;

.( try:  BREAKAT ++ <enter>  )
.( then:  TEST <enter> )
cr

\ **************************************************************************
\ A utility to allow dropping into the BXDEBUG program while testing this
\ debugger
\ **************************************************************************


\ code INT3       ( -- )          \ a debugging tool
\       int 3
\       next
\       END-CODE

}

