\ ANSI.SEQ  Provide ANSI CORE and CORE EXT by U.Hoffmann

/*

   $Logfile:   E:/SRC/FPC/VCS/ANSI.SEV  $
   $Revision:   1.2  $
   $Date:   02 Jan 1994 20:41:02  $
   $Author: U.Hoffmann$

 ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄuh 29Mai93
 ³ This file contains the definition of an ANS-Forth compatibilty  ³
 ³ package for F-PC. It covers most of the CORE and CORE EXT word  ³
 ³ set. For restrictions see below.                                ³
 ³                                                                 ³
 ³ To support strict ANS-Forth code, separate vocabularies are     ³
 ³ defined. By this the F-PC vocabulary search mechanism can be    ³
 ³ used to restrict the visibility of specific word sets.          ³
 ³                                                                 ³
 ³ This Code is public domain software.                            ³
 ³                                                                 ³
 ³ Remarks and comments are welcome.                               ³
 ³                                                                 ³
 ³               Ulrich Hoffmann                                   ³
 ³               Graf-Spee-Strasse 38                              ³
 ³               24105 Kiel                                        ³
 ³               Germany                                           ³
 ³               +49 431 80 12 14                                  ³
 ³               +49 431 80 12 33 (FAX)                            ³
 ³               uho@informatik.uni-kiel.de                        ³
 ³                                                                 ³
 ³ Bad news:                                                       ³
 ³ The F-PC outer interpreter does not comply to the ANS-Forth     ³
 ³ standard. Even worse, its structure prohibits to make it        ³
 ³ compliant with only simple additions.                           ³
 ³ Since this structure is used in several other parts of the F-PC ³
 ³ package (Metacompiler, Assembler, ...) for extensions, we do    ³
 ³ not try to change the outer interpreter and open up Pandora's   ³
 ³ box. Instead of this we will draw the attention of this         ³
 ³ package's user on the mismatch. So please be warned that the    ³
 ³ F-PC outer interpreter does not behave ANS-FORTH compliant.     ³
 ³                                                                 ³
 ³ In detail:                                                      ³
 ³ ANS-Forth requires the word ] not to have any effect on the     ³
 ³ input stream. The word : must read exactly one token from the   ³
 ³ input stream.                                                   ³
 ³ This is not the case in F-PC, F83, polyFORTH ... where ] itself ³
 ³ is the outer compiler and is called by : .                      ³
 ³ FIG-Forth sets STATE to reflect the state of the outer          ³
 ³ interpreter. A single loop dispatches to the interpreter or     ³
 ³ compiler. volksFORTH sets STATE as well as an execution vector  ³
 ³ to do this dispatch and so ] and : behave the ANS-Forth way and ³
 ³ are efficiently implemented.                                    ³
 ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

Please note also that this package does not cover word lists.
F-PC vocabularies are only used to seperate word sets.
To find the right words the search order is changed accordingly.

To keep the same search order even while colon definitions are handled,
ANSI CORE : already avoids the Forth-83's requirement to change
the context vocabulary to be the same as the current vocabulary.
This approach is hopefully open to a later addition of word lists.

*/

Only Forth also Hidden also definitions   warning off

: COMP:  \ Warn if word is used in compile state
  Create , immediate
  Does> @
    state @
    IF  ." warning: outer interpreter not ANS compliant!" cr
        X,  EXIT THEN
    EXECUTE ;

: token ( -- xt )
   \ Construct all necessary data for a new ananymous colon def
   \ but do not start to compile the body itself.
   \ Return the newly created execution token.
   \ Token is a factor of :NONAME and :
   here
   !CSP   233 c, ( jmp )  >nest here 2+ - ,
   XHERE PARAGRAPH +  DUP XDPSEG !  XSEG @ - ,  XDP OFF ;

: colon ( -- ) \ : without current @ context !
   HEADER  token  drop  !CSP HIDE (]) ;

Only Forth also Hidden also Forth also definitions

Vocabulary ANSI  ANSI definitions

Vocabulary CORE  ANSI CORE definitions

' !             Alias !                 ( x a-addr -- )
' #             Alias #                 ( ud1 -- ud2 )
' #>            Alias #>                ( xd -- c-addr u)
' #S            Alias #S                ( ud1 -- ud2 )
' '             Alias '                 ( "name" -- xt )
' (             Alias ( immediate       ( "ccc<paren>" -- )
' *             Alias *                 ( n1|u1 n2|u2 -- n3|u3 )
' */            Alias */                ( n1 n2 n3 -- n4 )
' */MOD         Alias */MOD             ( n1 n2 n3 -- n4 n5 )
' +             Alias +                 ( n1|u1 n2|u2 -- n3|u3 )
' +!            Alias +!                ( n|u a-addr -- )
' +LOOP         Alias +LOOP immediate   ( C: do-sys -- ) ( n -- ) ( R: loop-sys11 -- | loop-sys2 )
' ,             Alias ,                 ( x -- )
' -             Alias -                 ( n1|u1 n2|u2 -- n3|u3 )
' .             Alias .                 ( n -- )
' ."            Alias ." immediate      ( "ccc<quote>" -- )
' /             Alias /                 ( n1 n2 -- n3 )
' /MOD          Alias /MOD              ( n1 n2 -- n3 n4 )
' 0<            Alias 0<                ( n -- flag )
' 0=            Alias 0=                ( x -- flag )
' 1+            Alias 1+                ( n1|u1 -- n2|u2 )
' 1-            Alias 1-                ( n1|u1 -- n2|u2 )
' 2!            Alias 2!                ( x1 x2 a-addr -- )
' 2*            Alias 2*                ( x1 -- x2 )
' 2/            Alias 2/                ( x1 -- x2 )
' 2@            Alias 2@                ( a-addr -- x1 x2 )
' 2DROP         Alias 2DROP             ( x1 x2 -- )
' 2DUP          Alias 2DUP              ( x1 x2 -- x1 x2 x1 x2 )
' 2OVER         Alias 2OVER             ( x1 x2 x3 x4 -- x1 x2 x3 x4 x1 x2 )
' 2SWAP         Alias 2SWAP             ( x1 x2 x3 x4 -- x3 x4 x1 x2 )
' colon         Comp: :                 ( "name" -- colon-sys )
                                        ( Initiation: i*x -- i*x ) ( R: -- nest-sys )
                                        ( "name" execution: i*x -- j*x )
' ;             Alias ; immediate       ( C: colon-sys -- )
                                        ( -- ) ( R: nest-sys -- )
' <             Alias <                 ( n1 n2 -- flag )
' <#            Alias <#                ( -- )
' =             Alias =                 ( x1 x2 -- flag )
' >             Alias >                 ( n1 n2 -- flag )
' >BODY         Alias >BODY             ( xt -- a-addr )
' >IN           Alias >IN               ( -- a-addr )

: >number ( ud1 c-addr1 u1 -- ud2 c-addr2 u2 )
    BEGIN dup
    WHILE ( ud c-addr u )
      >r dup >r c@  ( ud char )
      base @ digit 0= IF  drop  r> r> ( ud c-addr u ) EXIT THEN  ( ud digit )
      swap  ( hi ) base @ um*  drop    rot ( lo ) base @ um* d+
      double?  IF   dpl incr  THEN
      r> r>  1 /string
    REPEAT ;

' >R            Alias >R                ( x -- ) ( R: -- x )
' ?DUP          Alias ?DUP              ( x -- 0|x x )
' @             Alias @                 ( a-addr -- x )
' ABS           Alias ABS               ( n -- +n )
' ABORT         Alias ABORT             ( i*x -- ) ( R: j*x -- )
' ABORT"        Alias ABORT" immediate  ( C: "ccc<quote>" -- ) ( i*x flag -- i*x | ) ( R: j*x -- j*x | )
: ACCEPT ( c-addr +n1 -- +n2 )  EXPECT  SPAN @ ;
' NOOP          Alias ALIGN immediate   ( -- )
' NOOP          Alias ALIGNED immediate ( -- )
' ALLOT         Alias ALLOT             ( n -- )
' AND           Alias AND               ( x1 x2 -- x3 )
' BASE          Alias BASE              ( -- a-addr )
' BEGIN         Alias BEGIN immediate   ( C: -- dest ) ( -- )
' BL            Alias BL                ( -- char )
' C!            Alias C!                ( char c-addr -- )
' C,            Alias C,                ( char -- )
' C@            Alias C@                ( c-addr -- char )
' 2+            Alias CELL+             ( a-addr1 -- a-addr2 )
' 2*            Alias CELLS             ( n1 -- n2 )
: CHAR ( "name" -- char )  BL WORD 1+ C@ ;
' 1+            Alias CHAR+             ( c-addr1 -- c-addr2 )
' NOOP          Alias CHARS immediate   ( n1 -- n2 )
' CONSTANT      Alias CONSTANT          ( x "name" -- ) ( -- x )
' COUNT         Alias COUNT             ( c-addr1 -- c-addr2 u )
' CR            Alias CR                ( -- )
' CREATE        Alias CREATE            ( "name" -- ) ( -- a-addr )
' DECIMAL       Alias DECIMAL           ( -- )
' DEPTH         Alias DEPTH             ( -- +n )
' DO            Alias DO immediate      ( C: -- do-sys ) ( n1|u1 n2|u2 -- ) ( R: loop-sys )
' DOES>         Alias DOES> immediate   ( C: colon-sys1 -- colon-sy2 )
                                        ( -- ) ( R: nest-sys1 -- )
                                        ( Initiation: i*x -- i*x a-addr ) ( R: -- nest-sys )
                                        ( "name" execution: i*x -- j*x )
' DROP          Alias DROP              ( x -- )
' DUP           Alias DUP               ( x -- x x )
' ELSE          Alias ELSE immediate    ( C: orig1 -- orig2 )
' EMIT          Alias EMIT              ( x -- )
: ENVIRONMENT? ( x-addr u -- false | i*x true )   2DROP FALSE ;

Only Forth also definitions  needs eval

Hidden also ANSI CORE definitions

' EVAL Alias EVALUATE      ( i*x c-addr u -- j*x )
' EXECUTE       Alias EXECUTE           ( i*x xt -- j*x )
' EXIT          Alias EXIT              ( -- ) ( R: nest-sys -- )
' FILL          Alias FILL              ( c-addr u char -- )
' FIND          Alias FIND              ( c-addr -- c-addr 0 | xt 1 | xt -1 )
' M/MOD         Alias FM/MOD            ( d1 n1 -- n2 n3 )
' HERE          Alias HERE              ( -- addr )
' HOLD          Alias HOLD              ( char -- )
' I             Alias I                 ( -- n|u ) ( R: loop-sys -- loop-sys )
' IF            Alias IF immediate      ( C: -- orig ) ( x -- )
' IMMEDIATE     Alias IMMEDIATE         ( -- )
' NOT           Alias INVERT            ( x1 -- x2 )
' J             Alias J                 ( -- n|u ) ( R: loop-sys -- loop-sys )
' KEY           Alias KEY               ( -- char )
' LEAVE         Alias LEAVE immediate   ( -- ) ( R: loop-sys -- )
' LITERAL       Alias LITERAL immediate ( C: x -- ) ( -- x )
' LOOP          Alias LOOP immediate    ( C: do-sys -- ) ( -- ) ( R: loop-sys1 -- | loop-sys2 )
\ : LSHIFT ( x1 u -- x2 )  16 umin 0 ?DO 2* LOOP ;
CODE LSHIFT ( x1 u -- x2 ) POP CX   POP AX   SHL AX, CL   1PUSH end-code  \  courtesy akg
' *D            Alias M*                ( n1 n2 -- d )
' MAX           Alias MAX               ( n1 n2 -- n3 )
' MIN           Alias MIN               ( n1 n2 -- n3 )
' MOD           Alias MOD               ( n1 n2 -- n3 )
' MOVE          Alias MOVE              ( addr1 addr2 u -- )
' NEGATE        Alias NEGATE            ( n1 -- n2 )
' OR            Alias OR                ( x1 x2 -- x3 )
' OVER          Alias OVER              ( x1 x2 -- x1 x2 x1 )

: POSTPONE ( C: "name" -- ) ( -- )
    state @ 0= Abort" compile only"
    defined dup 0= ?missing 0< IF ( non imm )  compile compile THEN
    X, ; immediate

' QUIT          Alias QUIT              ( -- ) ( R: i*x -- )
' R>            Alias R>                ( -- x ) ( R: x -- )
' R@            Alias R@                ( -- x ) ( R: x -- x )
' RECURSE       Alias RECURSE immediate ( C: -- )
' REPEAT        Alias REPEAT immediate  ( C: orig dest -- ) ( -- )
' ROT           Alias ROT               ( x1 x2 x3 -- x2 x3 x1 )
\ : RSHIFT ( x1 u -- x2 )  16 umin 0 ?DO u2/ LOOP ;
CODE RSHIFT ( x1 u -- x2 )  POP CX   POP AX   SHR AX, CL  1PUSH end-code \ courtesy akg
' "             Alias S" immediate      ( C: "ccc<quote>" -- ) ( -- c-addr u )
' S>D           Alias S>D               ( n -- d )
' SIGN          Alias SIGN              ( n -- )

: SM/REM ( d1 n2 -- n2 n3 )
    over >r  2dup xor 0< >r
    abs >r  dabs r>   um/mod ( rem quot )
    r> ?negate swap  r> ?negate swap ;

' SOURCE        Alias SOURCE            ( -- c-addr u )
' SPACE         Alias SPACE             ( -- )
' SPACES        Alias SPACES            ( n -- )
' STATE         Alias STATE             ( -- a-addr )
' SWAP          Alias SWAP              ( x1 x2 -- x2 x1 )
' THEN          Alias THEN immediate    ( C: orig -- ) ( -- )
' TYPE          Alias TYPE              ( c-addr u -- )
' U.            Alias U.                ( u -- )
' U<            Alias U<                ( u1 u2 -- flag )
' UM*           Alias UM*               ( u1 u2 -- ud )
' UM/MOD        Alias UM/MOD            ( ud u1 -- u2 u3 )
Code UNLOOP ( -- ) ( R: loop-sys -- )  ADD RP, # 6  NEXT   end-code
' UNTIL         Alias UNTIL immediate   ( C: dest -- ) ( x -- )
' VARIABLE      Alias VARIABLE          ( "name" -- ) ( "name" Execution: -- a-addr )
' WHILE         Alias WHILE immediate   ( C: dest -- orig dest ) ( x -- )
' WORD          Alias WORD              ( char "<chars>ccc<char>" -- c-addr )
' XOR           Alias XOR               ( x1 x2 -- x3 )
' [             Alias [ immediate       ( -- )
' [']           Alias ['] immediate     ( -- xt )
: [CHAR] ( C: "name" -- ) ( -- char )  CHAR [compile] Literal ; immediate
' ]             Comp: ]                 ( -- )

Vocabulary EXT  EXT definitions

' #TIB          Alias #TIB              ( -- a-addr )
' .(            Alias .( immediate      ( "ccc<paren>" -- )
' .R            Alias .R                ( n1 n2 -- )
' 0<>           Alias 0<>               ( x -- flag )
' 0>            Alias 0>                ( n -- flag )
' 2>R           Alias 2>R               ( x1 x2 -- ) ( R: -- x1 x2 )
' 2R>           Alias 2R>               ( -- x1 x2 ) ( R: x1 x2 -- )
' 2R@           Alias 2R@               ( -- x1 x2 ) ( R: x1 x2 -- x1 x2 )

: :NONAME ( -- colon-sys ) ( -- xt )
   token  (]) ;

' <>            Alias <>                ( x1 x2 -- flag )
' ?DO           Alias ?DO immediate     ( C: -- do-sys ) ( n1|u1 n2|u2 -- ) ( R: -- | loop-sys )
' AGAIN         Alias AGAIN immediate   ( C: dest -- ) ( -- )
: C" ( "ccc<quote>" -- ) ( -- c-addr )  [compile] "  compile ">$ ; immediate
' CASE          Alias CASE immediate    ( C: -- case-sys ) ( -- )
' X,            Alias COMPILE,          ( xt -- )
' CONVERT       Alias CONVERT           ( ud1 c-addr1 -- ud2 c-addr2 )
' ENDCASE       Alias ENDCASE immediate ( C: case-sys -- ) ( x -- )
' ENDOF         Alias ENDOF immediate   ( C: case-sys1 of-sys -- case-sys2 ) ( -- )
' ERASE         Alias ERASE             ( addr u -- )
' EXPECT        Alias EXPECT            ( c-addr +n -- )
' FALSE         Alias FALSE             ( -- false )
' HEX           Alias HEX               ( -- )

: MARKER ( "name" -- )
    Create
    context  #vocs 0 DO  dup @ , 2+ LOOP drop  current @ ,
  Does> ( -- )
    dup  context  #vocs 0 DO  over @ over !  2+ swap 2+ swap LOOP drop
       @ current !
    body> dup >view (frget) ;

' NIP           Alias NIP               ( x1 x2 -- x2 )
' OF            Alias OF immediate      ( C: -- of-sys ) ( x1 x2 -- | x1 )
' PAD           Alias PAD               ( -- c-addr )
' PARSE         Alias PARSE             ( char "ccc<char>" -- c-addr u )
' PICK          Alias PICK              ( xu ... x1 x0 u -- u ... x1 x0 xu )
' QUERY         Alias QUERY             ( -- )
( REFILL is not defined )

: RESTORE-INPUT ( x1 ... xn n -- flag )
    dup 7 <> IF  0 ?DO drop LOOP  true EXIT THEN
    drop
    !> run  !> loadline  !> #tib  !> >in  !> 'tib  !> loading  !> iblen
    false ;

' ROLL          Alias ROLL              ( xu xu-1 ... x0 u -- xu-1 ... x0 xu )

: SAVE-INPUT ( -- x1 ... xn n )
    @> iblen  @> loading  @> 'tib  @> >in  @> #tib  @> loadline  @> run  7 ;

( SOURCE-ID is not defined )
' SPAN          Alias SPAN              ( -- a-addr )
' TIB           Alias TIB               ( -- c-addr )
' =:            Alias TO immediate      ( x "name" -- )  ( C: "name" -- ) ( x -- )
' TRUE          Alias TRUE              ( -- true )
' TUCK          Alias TUCK              ( x1 x2 -- x2 x1 x2 )
' U.R           Alias U.R               ( u n -- )
' U>            Alias U>                ( u1 u2 -- flag )
: UNUSED ( -- u )   sp@ here - ;
' VALUE         Alias VALUE
: WITHIN ( n1|u2 n2|u2 n3|u3 -- flag )   over - >r - r> u< ;
' [COMPILE]     Alias [COMPILE] immediate ( "name" -- )
' \             Alias \ immediate       ( "ccc<eol>" -- )

Only Forth also Root definitions

' ANSI Alias ANSI

Only Forth also definitions  warning on

cr .( ANS-Forth compatibilty package loaded )
cr .( Type: ONLY ANSI CORE  to select the ansi word set.)
cr .( To get back and leave, type FORTH BYE )

\S

Examples:

 strict ANS-Forth definitions:
 ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
  Only ANSI CORE EXT  also ANSI CORE   also definitions

 use also core word set:
 ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
  Only Forth also definitions ANSI CORE also

  Notice that ANSI CORE-:'s will not change the search order,
  so it stays the same even over colon definitions.

ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

Revision history:
$Log:   E:/SRC/FPC/VCS/ANSI.SEV  $
   
      Rev 1.2   02 Jan 1994 20:41:02
   Warning if outer interpreter conflict can arise.
   
      Rev 1.0   29 May 1993 20:39:54
   Initial revision.


