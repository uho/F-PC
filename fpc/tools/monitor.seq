\ MONITOR.SEQ   Interactive Forth Monitor               by Zafar Essak

comment:
  Zafar Essak, Box 46263 Stn.G, Vancouver, B.C.,Canada V6R 4G6

     Forth offers the advantage that it is interactive.  The
user may examine the effects of a series of Forth words by
direct execution.  This series of words may be added to the
dictionary as new definitions or entered onto the disk for
future use.

     To fully utilize the interactive features of the language,
implementations of Forth need to provide a monitor state with
editing functions.  Such a monitor would respond to the enter
keystroke by transfering the contents of the current line to
the terminal input buffer (TIB) and calling INTERPRET.
Following interpretation, QUIT would return the user to the
terminal monitor to resume "editing" instructions.
comment;

\ Zafar Essak, Box 46263 Stn.G, Vancouver, B.C.,Canada V6R 4G6

comment:

References:
    Craig A. Lindley, Forth Windows for the IBM PC,
                               Dr.Dobbs 1986 JUL
    Leo Brodie, Quick Text Formatter, FD:IV/4,p.28, 1982 NOV
    Henry Laxen & Michael Perry, F83, 1984 APR
    Ray Duncan, Advanced MS DOS
    IBM-PC XT Technical Reference Manual, 1983 APR  p.2-18

comment;

POSTFIX         \ Use the postfix assembler syntax

CODE SETMODE    ( n--)  AX POP   16 INT   NEXT   END-CODE

: clearscreen   ( --)  2 SETMODE ;

\ CODE pos      ( x,y--)
\     AX POP  DX POP  AL DH MOV  BH BH XOR  2 # AH MOV
\     16 INT   NEXT   END-CODE
\ : pos ( x,y--) 2DUP #LINE ! #OUT ! pos ;    \ for F83

: pos ( x,y--) at ;

        \ Craig A. Lindley, Dr.Dobbs 1986 JUL, p.46

CODE location   ( --x,y)               \ current cursor location
    SI PUSH 0 # BH MOV 3 # AH MOV    \ int 10h func. 3
    16 INT SI POP AH AH XOR
    DL AL MOV AX PUSH DH AL MOV
    1PUSH END-CODE

        \ scroll specified window n lines

CODE scrollup   ( xul,yul,xlr,ylr,n,attrib--)
    BX POP BL BH MOV DI POP
    DX POP DL DH MOV AX POP AL DL MOV   \ dx has lr x y
    CX POP CL CH MOV AX POP AL CL MOV   \ cx has ul x y
    DI AX MOV SI PUSH BP PUSH           \ save regs
    6 # AH MOV 16 INT             \ ax # of lines func code ah
    BP POP SI POP NEXT END-CODE   \ restore forth regs

CODE scrolldown ( xul,yul,xlr,ylr,n,attrib--)
    BX POP BL BH MOV DI POP
    DX POP DL DH MOV AX POP AL DL MOV   \ dx has lr x y
    CX POP CL CH MOV AX POP AL CL MOV   \ cx has ul x y
    DI AX MOV SI PUSH BP PUSH           \ save regs
    7 # AH MOV 16 INT             \ ax # of lines func code ah
    BP POP SI POP NEXT END-CODE   \ restore forth regs

CODE rdchar     ( --charattrib )   \ read char at current cursor
    0 # BH MOV 8 # AH MOV       \ pg = 0  func. code = 8
    SI PUSH 16 INT SI POP       \ do video interrupt
    1PUSH  END-CODE             \ charattrib to stack

: getchar       ( x,y--charattrib) pos rdchar ;

: attribute     ( --n) rdchar 256 / ;

CODE charemit   ( charattrib--)
    AX POP AH BL MOV BH BH XOR    \ char in al, attrib in bl
    1 # CX MOV 9 # AH MOV         \ count=1, func. code in ah
    SI PUSH 16 INT                \ write char/attrib
    3 # AH MOV 16 INT             \ increment cursor
    DL INC 2 # AH MOV 16 INT      \     position
    SI POP NEXT END-CODE

CODE chars      ( charattrib,n--)    \ write n chars
    CX POP AX POP AH BL MOV     \ count in cx, attrib in bl
    BH BH XOR 9 # AH MOV        \ char in al, func. code in ah
    SI PUSH 16 INT SI POP       \ do video interrupt
    NEXT END-CODE

: drawrow       ( x,y,charatt,n--)  \ draw n chars starting at x,y
                >R >R pos R> R> chars ;

: putchar       ( x,y,charatt--) >R POS R> 1 chars ;

: acceptline    ( addr,n--) location       \ a,n,x,y
    2 PICK 0
        DO I OVER pos rdchar 255 AND    \ a,n,x,y,ascii
            4 PICK I + C! LOOP          \ a,n,x,y
    >R DROP -TRAILING 1+ DUP SPAN !
     R> pos DROP ;

80 CONSTANT crtwidth

: right         ( --n) crtwidth 1- ;

VARIABLE saddr

: saveaddr      ( --addr) saddr @ ;

: saveline      ( --) location saveaddr crtwidth acceptline pos ;

: forward       ( --) location SWAP DUP right <
                IF 1+ ELSE DROP 0 THEN SWAP pos ;

: backward      ( --) location SWAP ?DUP
                IF 1- ELSE right THEN SWAP pos ;

: down          ( n--) location DUP 24 <
                IF 1+ ELSE DROP 0 THEN pos ;

: goup          ( n--) location ?DUP
                IF 1- ELSE 24 THEN pos ;

: home          ( --) 0 0 pos ;

: end           ( --) right 24 pos ;

VARIABLE TABS   8 TABS !

: dotab         ( --) TABS @ location DROP over MOD - 0 DO forward LOOP ;

: lowerscreen   ( --xul,yul,xlr,ylr,1,attrib)
                location >R DROP 0 R> 79 24 1 attribute ;

: deleteline    ( --) lowerscreen scrollup ;

: spreadlines   ( --) lowerscreen scrolldown ;

: backspace     ( --) bs EMIT BL EMIT bs EMIT -4 #out +! ;

: gobble        ( --) location crtwidth 2 PICK - SPACES pos ;

: slough        ( --) gobble location 2DUP 1+ pos 24 OVER
    DO deleteline LOOP pos ;

VARIABLE keypressed

: keyemit       ( --) keypressed @ EMIT ;

: insertblank   ( --) saveline location >R >R
    saveaddr R@ + DUP 1+ crtwidth R@ - CMOVE>
    BL saveaddr R@ + C!
    R> R> 0 OVER pos saveaddr crtwidth TYPE pos ;

: deletechar    ( --) saveline location >R >R
    saveaddr R@ + DUP 1+ SWAP crtwidth R@ - 1- CMOVE
    BL saveaddr crtwidth + C!
    R> R> 0 OVER pos saveaddr crtwidth TYPE pos ;

\ arranging the characters in the line buffer and then typing
\ the entire line to the screen is extra work but appears to be
\ necessary for F83.

    \ Leo Brodie, FD:IV/4,p.28

: ',            ( --) '  , ;

create lineactions ( --addr)    \   action
      8 , ( bs )                ',  backspace
      9 ,                       ',  dotab
    199 , ( home )              ',  home
    207 , ( end )               ',  end
    203 ,                       ',  backward
    205 ,                       ',  forward
    200 ,                       ',  goup
    208 ,                       ',  down
    210 , ( ins )               ',  insertblank
    211 , ( del )               ',  deletechar
    245 , ( ctrl-end )          ',  gobble
    246 , ( ctrl-PgDn)          ',  slough
    247 , ( ctrl-home )         ',  clearscreen
    159 , ( Alt-s)              ',  spreadlines
    160 , ( Alt-d)              ',  deleteline
      0 , ( others )            ',  keyemit

HERE CONSTANT endlineactions

endlineactions 4 - CONSTANT nomatch

: keyaction     ( ascii--) DUP keypressed !
    nomatch SWAP
    endlineactions lineactions
        DO DUP I @ =
            IF 2DROP I 0 LEAVE
            THEN 4 +LOOP
        DROP 2+ @ EXECUTE ;

: keys          ( --ascii) key ?DUP 0= IF KEY 128 + THEN ;

: ACCEPT        ( addr,n--) OVER saddr !
    BEGIN keys DUP 13 = NOT
    WHILE keyaction
    REPEAT DROP acceptline ;

: IQUERY         ( --) TIB crtwidth ACCEPT SPAN @ #TIB ! 0 >IN ! ;

: ok            ( --) quit ;

\ Refill QUERY in F83 quit with new quit.

' IQUERY ' QUIT >BODY @ XSEG @ + 22 !L


 CR CR .( Forth monitor now interactive ! )

    \ Concept courtesy of Bill Muench, Santa Cruz

\ : U.R ( n1,n2--) 0 SWAP D.R ;

: ENOUGH? ( --?) KEY? IF KEY DROP KEY 13 = ELSE 0 THEN ;

: 8dump ( addr--) DUP 8 + SWAP DO I C@ 4 .R LOOP ;

: emitit ( n--) DUP 14 254 BETWEEN NOT
    IF DUP >R R@ 7 = R@ 8 = R@ 10 = R@ 13 = R> 255 = OR OR OR OR
        IF DROP BL THEN THEN EMIT ;

: 8cdump ( addr--) DUP 8 + SWAP DO I C@ emitit LOOP ;

: | ( addr,n1..n8--) 0 7            ( decrementing +loop )
    DO I 1+ PICK I + C! -1 +LOOP
    LOCATION >R DROP 45 R> POS 8cdump QUIT ;

: DUMP ( addr,n--) CR CR OVER + SWAP
    DO 4 SPACES I 5 U.R SPACE I 8dump SPACE ASCII | EMIT SPACE
        I 8cdump CR ENOUGH? IF LEAVE THEN 8 +LOOP ;

warning off     \ Dont want warnings about redefinitions.

