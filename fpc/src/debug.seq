\ DEBUG.SEQ     A high level debugger      Enhancements by Tom Zimmer

\ The debugger is designed to let the user single step the
\ execution of a high level definition.  To invoke the
\ debugger, type DEBUG XXX where XXX is the name of the
\ word you wish to trace.  When XXX executes, you will get
\ a single step trace showing you the word within XXX that
\ is about to execute, and the contents of the parameter
\ stack. This debugger works by patching the NEXT routine,
\ so it is highly machine and implementation dependent.

ONLY FORTH ALSO DEFINITIONS HIDDEN ALSO

: ?UPC          ( c1 -- c2 )
                dup 128 <
                if      upc
                then    ;

headerless

: SHOWSRC       ( --- ) \ Show the source for the current debugging word.
                0 save!> nosetcur
                savecursor
                8 SAVE!> TABSIZE
                0 0 AT
                ['] SRCEEOLCR IS CR
                ON> ?DEBUG
                DEFCFA  @ (SEE)
                OFF> ?DEBUG
                KEY? 0=
                IF      #LINE @ SPLIT-L# 1- MIN SPLIT-L# 1- SWAP
                        ?DO     CR EEOL
                        LOOP
                THEN
                ['] CRLF IS CR
                0 SPLIT-L# 1- AT >ATTRIB4
."  Cont, Done, Forth, Nest, Quit, Rstk, Skip, Unnest, Watch, X-srctgl, Z-slow, ?"
                EEOL >NORM
                RESTORE> TABSIZE
                restcursor
                restore> nosetcur ;

: SRCCR         ( --- ) \ Source CR for the debugger, subscreen scroll.
                0 SCROLL-L# AT -LINE 0 ROWS 1- AT ;

' SRCCR IS .SRCCR

DEFER .DEFSRC   ' SHOWSRC IS .DEFSRC    \ display definition source

DEFER DO.SRC    ' .DEFSRC IS DO.SRC

headers

: SRCON         ( --- ) \ Enable source printing durring debugging.
                ['] .DEFSRC IS DO.SRC
                ['] SRCCR   IS CCR ;

: SRCOFF        ( --- ) \ disable source printing durring debugging.
                ['] NOOP    IS DO.SRC
                ['] CRLF    IS CCR ;

headerless

ONLY FORTH ALSO DEFINITIONS BUG ALSO

VARIABLE DBSEG
VARIABLE DBOFF
VARIABLE CNT
VARIABLE 'DEBUG   ( Code field for high level trace )
DEFER DBG.S     ' .S IS DBG.S           \ default DBG.S to the systems .S
DEFER SKIP_TO   ' NOOP IS SKIP_TO       \ allow skipping to later point in
                                        \ definition.
create #pad-save 10 allot

: DEBUG.S       ( -- )                  \ display stack in specified base
                save> base
                save> hld
                pad 10 - #pad-save 10 cmove
                showbase ?dup
                if      26 min 2 max base !
                then    dbg.s
                #pad-save pad 10 - 10 cmove
                restore> hld
                restore> base ;

: DBG_RDEPTH    ( -- n1 )
                                        \ The "14" takes account of debug
                rp0 @ rp@ - 4 - 14 - 0 max 2/ ;

: .RP           ( -- )
                hld @
\                save> hld
                pad 10 - #pad-save 10 cmove
                ."  <" dbg_rdepth 0 max dup (.) type ." >" 8 sp>col
                14 over - 0max 5 * spaces
                14 min 7 + 7
                ?DO      I 3 + rpick space h.4
                LOOP
                #pad-save pad 10 - 10 cmove
\                restore> hld
                hld ! ;

: DO.SHOW.SOURCE ( -- )
                save> hld
                pad 10 - #pad-save 10 cmove
                DO.SRC
                #pad-save pad 10 - 10 cmove
                restore> hld ;

: DBGH.         ( -- )
                save> hld
                pad 10 - #pad-save 10 cmove
                H.
                #pad-save pad 10 - 10 cmove
                restore> hld ;

LABEL FNEXT   ( Fix the >NEXT code back to normal )
        MOV AX, # $AD26                 \ ES: LODSW
        MOV >NEXT AX
        MOV AX, # $E0FF                 \ JMP AX
        MOV >NEXT 2+ AX
        RET END-CODE

LABEL DNEXT   ( The Debugger version of a normal >NEXT )
    ES: LODSW JMP AX
        END-CODE

LABEL DEBNEXT
        MOV AX, ES
        CMP AX, DBSEG                           \ does SEG match?
        0= IF   MOV AX, IP
                CMP AX, DBOFF                   \ is offset greater
                >= IF   INC CNT
                        CMP CNT # 2             \ gone through twice?
                        0= IF   MOV CNT # 0
                                CALL FNEXT
                                PUSH IP
                                MOV AX, 'DEBUG
                                JMP AX
                        THEN
                THEN
        THEN    JMP DNEXT
        END-CODE

CODE PNEXT   ( -- )
        MOV AL, # $0E9
        MOV >NEXT AL
        MOV AX, # DEBNEXT  >NEXT 3 + -
        MOV >NEXT 1+ AX
        NEXT   C;

headers

FORTH DEFINITIONS ALSO HIDDEN ALSO

CODE UNBUG    ( -- )
        CALL FNEXT
        NEXT   C;

BUG DEFINITIONS

headerless

CREATE DSTK 100 ALLOT DSTK 100 ERASE

variable slowly
variable zippy
variable dcnt
variable dbcfa

\ ' >NAME.ID @REL>ABS CONSTANT 'DOCOL
' KEY   @REL>ABS          CONSTANT 'UDEFER
' BDOS  @REL>ABS          CONSTANT 'DEFER
' FORTH @REL>ABS @REL>ABS CONSTANT 'DODOES

0 value segabove                        \ segment of routine above current

: find_:        ( a1 n1 -- a2 n2 )   \ find any definition
                begin   $E9 ( jmp ) scan
                        over @rel>abs 'docol <> over and
                while   3 /string
                repeat  ;

: find_dodoes   ( a1 n1 -- a2 n2 )   \ find any definition
                begin   $E8 ( call ) scan
                        over @rel>abs @rel>abs 'dodoes <> over and
                while   3 /string
                repeat  ;

headers

: seg>cfa       ( seg -- cfa f1 )       \ find cfa given the physical segment
                xseg @ - >r
                $100 here $100 -
                begin   find_: over >body @ r@ <> over and
                while   5 /string
                repeat  dup 0=
                if      2drop
                        $100 here $100 -
                        begin   find_dodoes over @rel>abs
                                >body @ r@ <> over and
                        while   5 /string
                        repeat
                then    r>drop ;

headerless

: n>name.id     ( cfa --- )
                on>  ?defattrib >name.id
                off> ?defattrib ;

: next_word@    ( -- cfa )
                dbseg @ pfasav @ @L ;

: d.id          ( -- )                      \ debugger id dot
                ccr
                save> base hex
                save> hld
                pad 10 - #pad-save 10 cmove
                dbseg  @ 4 u.r
                pfasav @ 3 u.r
                #pad-save pad 10 - 10 cmove
                restore> hld
                restore> base
                dcnt @ 0max 16 mod spaces
                next_word@ dup @rel>abs
                case
                        'docol  of      ."  :  "        endof
                        'udefer of      ."  Ud "        endof
                        'defer  of      ."  d  "        endof
                                over
                                case
                                ['] execute of  ."  e  "        endof
                                ['] perform of  ."  p  "        endof
                                ['] exec:   of  ."  e: "        endof
                                                4 spaces
                                        drop
                                endcase
                        drop
                endcase
                n>name.id 16 nlen @ - spaces ;

: setdebug      ( cfa1 cfa2 -- )        \ cfa1 is for name displaying
                                        \ cfa2 is for debugging
                swap defcfa !
                dup dbcfa !
                >body @ +xseg dbseg !
                off> pfaline off> #empty
                slowly off 1 CNT ! DBOFF OFF ;

: >user@        ( cfa1 -- cfa2 )
                >body @ up @ + @ ;

: DSTK0 DSTK 100 ERASE DCNT OFF ;

: >DS   DCNT @ DSTK + !  2 DCNT +! ;

: DS>   DCNT @ 2 < 0= IF -2 DCNT +! THEN DCNT @ DSTK + @ ;

: nest1         ( cfa1 cfa2 -- )        \ save current debug and nest to
                ccr                     \ "cfa2". display "cfa1".
                over dup dbgh. n>name.id
                ."  nesting "
                dbcfa @ >ds
                defcfa @ >ds
                setdebug ;

: ?docol        ( cfa -- f1 )
                @rel>abs 'docol = ;

: ?nest         ( cfa -- )      \ try to nest the word "cfa"
                recursive       \ this is a recursive definition
                dup @rel>abs
                case
                'docol  of      dup nest1               endof
                'udefer of      >user@  ?nest           endof
                'defer  of      >body @ ?nest           endof
                                >r
                                case
                                ['] execute of  dup   ?nest             endof
                                ['] perform of  dup @ ?nest             endof
                                ['] exec:   of  dup 1+ 2*
                                                dbseg @ pfasav @ rot +
                                                @L    ?nest             endof
                                        \ *** DOES> test ***
                                        dup        @rel>abs @rel>abs
                                        ['] forth  @rel>abs @rel>abs =
                                        if      dup dup @rel>abs nest1
                                        else    ccr
                                                dup dbgh. dup n>name.id
                                                ."  Is not debugable "
                                        then
                                        drop
                                endcase
                                r>drop
                endcase ;

: unnest1       ( -- )
                off> pfaline
                off> #empty
                slowly @
                if      DO.SHOW.SOURCE
                then    off> slowly off> zippy
                dcnt @ 4 >=
                if      ds> ds> setdebug
                then    ;

: ?unnest1      ( -- )
                next_word@
                case
                ['] unnest of          unnest1          endof
                ['] exit   of          unnest1          endof
                ['] ?exit  of   dup if unnest1 then     endof
                        drop
                endcase ;


\ Type "?" while in the debugger to display the following line;

\       C-cont, D-done, F-forth, Q-quit, N-nest, U-unnest:

\ The commands are available while debugging, as follows;

\       C-cont          Continuous, scrolls through words as they
\                       are executed, stop by pressing <return>.
\       D-done          We are Done debugging, allow normal execution
\                       to continue.
\       F-forth         Allow entry of Forth commands, until a <return>
\                       is pressed on an empty command line.
\                       P.S. don't make any typing errors or you will
\                       fall out of the debugger.
\       Q-quit          Quit the debugger, and unpatch the debug word.
\                       Returns to Forth.
\       N-nest          Nest into the current definition the debugger
\                       is sitting on, if it is a ":" definition, else
\                       issue an error message but don't abort.
\       U-unnest        Unnest from the current word being debugged, the
\                       debugger will re-enter when the word finishes
\                       executing, and pops up one level to the word that
\                       called it. You cannot Unnest without Nesting.

defer extra-commands    ' noop is extra-commands
defer .dbg-help         ' noop is .dbg-help

: get-command   ( --- c1 )
                begin   ." ?> "
                        (key)   0 over '?' =
                        if      ccr
." Continue-to-end, Done-debugging, Forth-command, Hex-tgl, Nest-def, Quit,"
                                eeol ccr
." Return-stk, Skip-to, Unnest-def, Watch-set, X-srctgl, Z-slow-continue:"
                                eeol .dbg-help 0=
                        then    over ?upc 'F' =
                        if      2>r
                                ccr
        ." Press <Enter> on an empty command line to continue debugging."
                                begin   ccr debug.s ." ->"
                                        query #tib @
                                while   interpret
                                repeat  2r> 0=
                        then
                        '+' 2 pick =
                        if      split-l# 1+ max-split min
                                !> split-l# 0=          then
                        '-' 2 pick =
                        if      split-l# 1- 9 max
                                !> split-l# 0=          then
                        'H' 2 pick ?upc =
                        if      showbase 0=
                                if      $10 !> showbase
                                else    $00 !> showbase
                                then
                                2>r debug.s 2r> 0=      then
                        'R' 2 pick ?upc =
                        if      2>r debug.s 2r>
                                ccr .rp         0=      then
                        'O' 2 pick ?upc =
                        if      order           0=      then
                        extra-commands
                while  drop d.id DO.SHOW.SOURCE
                repeat ?upc ;

0 VALUE SAVESEG

: trace         ( ip - )
                attrib @ >r 7 attrib c!
                pfasav ! debug.s d.id
                slowly @ 0= zippy @ or if DO.SHOW.SOURCE then
                r> 2r> 2r> over =: segabove 2>r 2>r >r
                ?unnest1
                slowly @ 0= (key?) or
                if      slowly off zippy off
                        get-command
                        case
                        'C' of  slowly on                       endof
                        'Z' of  slowly on zippy on              endof
                        'N' of  next_word@ ?nest                endof
                        'X' of  @> DO.SHOW.SOURCE ['] noop =
                                if      srcon
                                else    srcoff
                                then                            endof
                        'D' of  off> pfaline off> #empty
                                r> attrib !
                                -1 pfasav ! exit                endof
                        'S' of  skip_to                         endof
                        'U' of  dcnt @ 4 >=
                                if      ds> ds> setdebug
                                else    segabove seg>cfa        \ -- cfa f1
                                        if      dup @rel>abs @rel>abs
                                                'dodoes =
                                                if      ccr
." Definition NAME may not be correct, this is one word of a class of words."
                                                        ccr
                                                        dup @rel>abs
                                                        setdebug
                                                else    dup setdebug
                                                then
                                        else    drop
                                                ccr ." Couldn't find CFA "
                                        then
                                then                            endof
                        'Q' of  -1 pfasav !
                                off> pfaline off> #empty
                                true abort" unbug"              endof
                        drop
                endcase
        else    3 spaces
        then
        pnext r> attrib ! ;

' TRACE 'DEBUG !

: %skip_to      ( -- )          \ set point to skip to
                save> pfasav
                0 split-l# at >attrib3
."  Use + and - to move the hilighted word to the point where you want to stop "
                eeol
                0 split-l# 1+ at >attrib3
."  Press Enter when done, or ESC to cancel skip " eeol >norm
                begin   DO.SHOW.SOURCE
                        key ?upc
                        case
                        '+' of 2 pfasav +!               false endof
                        '-' of pfasav @ 2- 0max pfasav ! false endof
( ESC )                 27  of                           true  endof
( Enter )               13  of pfasav @ 2- 0max DBOFF !  true  endof
                               drop false beep
                        endcase
                until
                restore> pfasav ;

' %skip_to is skip_to

headers

FORTH DEFINITIONS

: adebug        ( a1 --- )
                debugable       \ convert inline next to jmp next for debugger.
                dstk0                           \ clear debugger stack
                ?nest                           \ try to nest into definition
                dcnt @ 0= abort" Aborting.. "
                dstk0                           \ clear debugger stack again
                ." Debugger ready."
                pnext ;                         \ set debugger active

: debug         ' adebug ;
: dbg           >in @  debug  >in !  ;

ONLY FORTH ALSO DEFINITIONS


