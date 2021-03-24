\ UTILS.SEQ     Some basic utilities

3.6000 2CONSTANT FPCVER#        \ version number of F-PC, always 5 digits

: .FPCVER#      ( -- )          \ display version number in a 6 char field
                                \ with 1 leading, and 1 trailing space
                FPCVER# 100 UM/MOD NIP        \ discard last two digits
                0 <# BL HOLD # # '.' HOLD # BL HOLD #> TYPE ;

: .VERSION      ( -- )
                FPCVER# <# BL HOLD # # # # '.' HOLD # BL HOLD #> TYPE ;

: ?             ( adr -- )
                @ .   ;

: YCOUNT        ( a1 --- a2 n1 )
                DUP 1+ SWAP YC@ ;

: ?ENOUGH       ( n -- )
                DEPTH 1- > ABORT" Not enough Parameters" ;

TRUE  VALUE ?DOSIO

: SP>COL        ( n1 -- )
                #OUT @ - SPACES ;

: EEOL          ( col -- )
                COLS ?DOSIO + SP>COL ;

: .FREE         ( -- )
                ." Free Bytes:"
                 ."  CODE = "       SP@       HERE - (U.) TYPE
                ." , LIST = " #LISTSEGS XHERE DROP XSEG @ - - 16 *D 1 D.R
                ." , HEAD = " #HEADSEGS 16 * YHERE - (U.) TYPE ;

: DRIVE?        ( -- )   0 25 BDOS 'A' + EMIT ." : "  ;

                \ These are needed by later utilities

DEFER CCR       ' CR IS CCR             \ Carraige Carraige return?
DEFER .SRCCR    ' CR IS .SRCCR

VARIABLE DEFCFA                         \ Holds the CFA of the current word.
VARIABLE PFASAV         -1 PFASAV !     \ Current offset into definition.

2VARIABLE CTIME         GETTIME CTIME 2!
2VARIABLE CDATE         GETDATE CDATE 2!

: $.R   ( addr len n1 -- )   TUCK UMIN TUCK - -ROT TYPE SPACES ;
: $.L   ( addr len n1 -- )   TUCK UMIN TUCK - SPACES TYPE ;

: DOES?         ( IP -- IP' F )  \ IP IS ACTUALLY CFA, IP' IS PFA
                DUP >BODY SWAP @REL>ABS @REL>ABS
                ['] FORTH      @REL>ABS @REL>ABS = ;

' HEX @REL>ABS CONSTANT 'DOCOL

: >.ID          ( A1 --- )
                DUP 200 U< IF DROP EXIT THEN
                128 0
                DO      DUP @REL>ABS 'DOCOL =
                        IF  LEAVE ELSE 1- THEN
                LOOP    >NAME .ID ;

: U<=   ( u1 u2 -- f )   U> NOT   ;
: U>=   ( u1 u2 -- f )   U< NOT   ;
: <=    ( n1 n2 -- f )   > NOT    ;
: >=    ( n1 n2 -- f )   < NOT    ;
: 0>=   ( n1 n2 -- f )   0< NOT   ;
: 0<=   ( n1 n2 -- f )   0> NOT   ;

VARIABLE #TIMES   ( # times already performed )   1 #TIMES !

: TIMES   ( n -- )
   1 #TIMES +!  #TIMES @
   < IF  1 #TIMES !  ELSE  >IN OFF  THEN   ;

: MANY   ( -- )
   KEY? NOT IF   >IN OFF   THEN   ;

: AT            ( col row -- )  ( 0 0 is upper left )
                DOES>  >R 2DUP R> PERFORM  #LINE !  #OUT ! ; AT

' 2DROP IS AT


: DARK          ( -- )
                DOES>  PERFORM   #LINE OFF  #OUT OFF   ; DARK

' NOOP IS DARK

: ?DARK         ( -- )
                KEY? 0= IF DARK CR THEN ;

DEFER AT?
DEFER -LINE

: SAVECURSOR    ( -- )          \ save all of the current cursor stuff
                2R>
                @> ATTRIB >R            \ save attribute
                GET-CURSOR >R           \ cursor shape
                @> #OUT @> #LINE 2>R    \ and position
                2>R ;

: RESTCURSOR    ( -- )          \ restore all of the cursor stuff
                2R>
                2R> AT                  \ restore position
                R> SET-CURSOR           \ shape
                R> ATTRIB !             \ and attribute
                2>R ;

0 VALUE ?DOINGMAC       \ Are we doing a macro, moved her from macros
                        \ to make it available for testing by programs
                        \ that may want to know if we are in the middle
                        \ of a macro.

VARIABLE #PAGE

: PAGE   ( -- )
   DOES> PERFORM   1 #PAGE +!   #LINE OFF   #OUT OFF   ; PAGE

: FORM-FEED   ( -- )   CONTROL M EMIT   CONTROL L EMIT  ;

' FORM-FEED IS PAGE

: ?PAGE         ( --- )         \ PAGE IF LINE CNT NOT ZERO
                #LINE @
                IF      PAGE
                THEN    ;

: ALIAS         ( A1 | alias_NAME --- )
                HEADER LAST @ 1 TRAVERSE 1+ Y! ;

: \UNLESS       ( NAME --- )    \ comment out line unless name is defined
                DEFINED NIP 0=
                IF      [COMPILE] \
                THEN    ; IMMEDIATE

' \UNLESS ALIAS \U IMMEDIATE
' \UNLESS ALIAS \+ IMMEDIATE

: \|UNLESS      ( name -- )     \ comment out line if name is defined
                DEFINED NIP
                IF      [COMPILE] \
                THEN    ; IMMEDIATE

' \|UNLESS ALIAS \|U IMMEDIATE
' \|UNLESS ALIAS \-  IMMEDIATE

\ ***************************************************************************
\ defining word, creates words that control compilation of a program.

: DIRECTIVE     ( flag | name -- )
                CREATE , IMMEDIATE
                DOES> @ 0=
                IF      [COMPILE] \
                THEN    ;

TRUE  DIRECTIVE \FPC            \ true  = load line following \FPC
FALSE DIRECTIVE \TCOM           \ false = don't load line following \TCOM

' \FPC  ALIAS \F IMMEDIATE      \ create a couple of alias's for convenience
' \TCOM ALIAS \T IMMEDIATE

\ These words can be treated like VALUE's, set them TRUE to make them load
\ the lines following themselves, and make them FALSE to prevent them
\ from loading the line following.

\ ***************************************************************************

' !>       ALIAS =: IMMEDIATE   \ make =: the same as !>

VARIABLE NLEN
0 VALUE ?DEFATTRIB

: >NAME.ID      ( CFA --- )
                >NAME DUP YC@ 31 AND DUP ?LINE NLEN !
                ?DEFATTRIB
                IF      %.ID
                ELSE    .ID
                THEN    ;

DEFER (SEE)

: H.4           ( n1 -- )       \ display n1 in four digit hex
                $10 SAVE!> BASE
                0 <# # # # # #> TYPE
                RESTORE> BASE ;

: <GRAPHDUMMY>  ( --- )
                CR ." Enter a KEY " KEY TRUE ;

DEFER GRAPHCHAR         ' <GRAPHDUMMY> IS GRAPHCHAR

DEFER >ATTRIB1          ' NOOP IS >ATTRIB1
DEFER >ATTRIB2          ' NOOP IS >ATTRIB2
DEFER >ATTRIB3          ' NOOP IS >ATTRIB3
DEFER >ATTRIB4          ' NOOP IS >ATTRIB4
DEFER >ATTRIB5          ' NOOP IS >ATTRIB5
DEFER >ATTRIB6          ' NOOP IS >ATTRIB6
DEFER >ATTRIB7          ' NOOP IS >ATTRIB7
DEFER >ATTRIB8          ' NOOP IS >ATTRIB8

DEFER >NORM             ' NOOP IS >NORM
DEFER >REV              ' NOOP IS >REV
DEFER >NORMBG           ' NOOP IS >NORMBG

DEFER DOBUTTON          ' NOOP IS DOBUTTON
0 VALUE MOUSEFLG        \ IS THE MOUSE CURRENTLY TURNED ON?

DECIMAL

VARIABLE RESTBASE       10 RESTBASE !
VARIABLE RESTCAPS       RESTCAPS ON
VARIABLE RESTTABS       8 RESTTABS !
VARIABLE RESTLMRG       RESTLMRG OFF
VARIABLE RESTRMRG       70 RESTRMRG !
VARIABLE RESTSTAT       RESTSTAT OFF
VARIABLE STATV          STATV OFF

: SAVESTATE     ( --- )
                BASE    @ RESTBASE !
                CAPS    @ RESTCAPS !
                LMARGIN @ RESTLMRG !
                RMARGIN @ RESTRMRG !
                TABSIZE @ RESTTABS !
                STATV   @ RESTSTAT ! ;

: RESTORESTATE  ( --- )
                RESTSTAT @ STATV !
                RESTBASE @ BASE !
                RESTCAPS @ CAPS !
                RESTLMRG @ LMARGIN !
                RESTRMRG @ RMARGIN !
                RESTTABS @ TABSIZE ! ;

: DEFAULTSTATE  ( --- )
                RESTSTAT ON
                10 RESTBASE !
                RESTCAPS ON
                8 RESTTABS !
                RESTLMRG OFF
                COLS 10 - RESTRMRG !
                RESTORESTATE ;

: ?DOSTOP       ( F1 --- )
                IF      RESTORESTATE
                        TRUE ABORT" Stopped"
                THEN    ;

: ?KEYPAUSE     ( --- )         \ Pause if key pressed
                KEY?
                IF      KEY 27 = ?DOSTOP
                        KEY 27 = ?DOSTOP
                THEN    ;

: $>TIB         ( a1 --- )
                COUNT DUP #TIB ! TIB SWAP CMOVE >IN OFF  ;

: >PATHEND"     ( A1 --- A2 N1 )        \ RETURN A2 AND COUNT=N1 OF FILENAME
                COUNT
                BEGIN   2DUP '\' SCAN ?DUP
                WHILE   2SWAP 2DROP 1 /STRING
                REPEAT  DROP ;

CREATE DSBUF    6 ALLOT

: !USED         ( --- )         \ Save the current dictionary pointers
                HERE DSBUF !
                XHERE PARAGRAPH + DSBUF 2+ !
                YHERE DSBUF 4 + ! ;
     0 DSBUF !
XSEG @ DSBUF 2+ !
     0 DSBUF 4 + !

: .USED         ( --- )
                CR ."   CODE    LIST   HEAD   TOTAL  bytes used"
                CR HERE DSBUF @ - DUP 6 U.R 0
                XHERE PARAGRAPH + DSBUF 2+ @ - 16 *D 2DUP 8 UD.R D+
                YHERE DSBUF 4 + @ - DUP 7 U.R 0 D+ 8 UD.R CR ;

: USED          ( <command_line> --- )
                !USED INTERPRET .USED ;

12 CONSTANT FORMFEED

: %NEWPAGE      ( --- )
                FORMFEED PEMIT ;

DEFER NEWPAGE   ' %NEWPAGE IS NEWPAGE

VARIABLE EMMHEAD

: .MEM          ( -- )
                SAVE> BASE DECIMAL
                CR ." -------- DOS memory usage" cr
                0 PHEAD @
                BEGIN   ?DUP
                        ?KEYPAUSE
                WHILE   DUP 2+ @ 16 UM* 6 D.R ."  bytes "
                        DUP 2- @ ?DUP
                        IF      ." at segment " H.
                                DUP 2+ @ ROT + SWAP     \ accumulate summ
                        ELSE    ." Unallocated"
                        THEN    YDP @
                        IF      #out @ 40 <
                                if      29 SP>COL DUP 2- BODY> >NAME .ID
                                        39 #line @ at 40 sp>col
                                else    69 SP>COL DUP 2- BODY> >NAME .ID
                                        cr
                                then
                        ELSE    #out @ 40 <
                                if      39 #line @ at 40 sp>col
                                else    cr
                                then
                        THEN    @
                REPEAT  #out @ 0> if cr then
                ." --------" CR
                ( pars -- ) 16 UM* 8 D.R
                ."  bytes TOTAL used by F-PC, excluding Unallocated"
                CR ." --------" CR
                MAXBLOCK 16 UM* 2DUP 8 D.R ."  bytes DOS memory Available" CR
                #PARS @ 0 2 UM/MOD TUCK + 16 UM* ROT 16 UM* D+ D+ 8 D.R
                                \ the above garbage is done to assure
                                \ we get an unsigned result, as #PARS
                                \ could be negative to start with.
                ."  bytes DOS memory Total" CR
                EMM-PRESENT?
                IF      ." -------- Expanded Memory Version "
                        HEX     EMM-GET-VERSION 0 <# # '.' HOLD # #> TYPE
                        DECIMAL ."  at Paragraph " ?emm: h.
                        EMMHEAD @
                        BEGIN   ?DUP
                                ?KEYPAUSE
                        WHILE   CR DUP 2+ @ 16 *D 8 D.R ."  bytes "
                                DUP 2- @
                                IF      ." on handle " dup 4 + @ .
                                ELSE    ." Unallocated"
                                THEN    YDP @
                                IF      32 SP>COL DUP 2- BODY> >NAME .ID
                                THEN    @
                        REPEAT
                        CR EMM-AVAIL-PAGES 16384 *D 8 D.R
                        ."  bytes expanded memory  Available"
                        CR EMM-TOTAL-PAGES 16384 *D 8 D.R
                        ."  bytes expanded memory  Total"
                ELSE    ." ----- NO Expanded Memory detected"
                THEN    RESTORE> BASE ;


\ ***************************************************************************
\ ***************************************************************************
\ ***************************************************************************
\ Some utilities from Michael Schroeder, Oschersleben Germany, as passed
\ along from Jorg Staben, Hilden Germany  04/05/94 15:20 tjz

\ MYUTILS.SEQ  some personal util-words          11.09.93 ms

\ ***************************************************************************
\ ?FUNKEY can help you to decide if the last readed key was a function key
\ or a normal key with an extended ASCII code
\ c1  =  keyvalue from KEY returned
\ f1  =  TRUE if c1 was a function key
\ ***************************************************************************

: ?funkey       ( c1 --- f1 )
                bioskeyval @ and 0= ;

\ ***************************************************************************
\ !TBL+ and @TBL+ are useful to move trough a table while fetching or storing
\ data to that table
\ ***************************************************************************

: !tbl+         ( a1 n1 --- a2 )
\ store n1 to address a1 and return a2= a1+2
                over ! 2+ ;

: @tbl+         ( a1 --- a2 n1 )
\ n1 = value at address a1; a2= a1+2
                dup @ swap 2+ swap ;

\ ***************************************************************************
\ HOPEN.FPATH attempts to open the handle a1, which only contains a filename,
\  along the directorys in FPATH
\ ***************************************************************************
: HOPEN.FPATH   ( a1 --- f1 )   \ f1 = true if failed to open
                dup>r count $file_buf place
                r@ path1 prepend.apath
                hopen  dup
                if      0=
                        begin   $file_buf r@ $>handle
                                npath   0=
                                if      r@ swap prepend.apath
                                        hopen 0=
                                else    drop 0= true
                                        $file_buf r@ $>handle
                                then
                        until
                then    r>drop ;

\ ***************************************************************************
\ ***************************************************************************
\ ***************************************************************************

