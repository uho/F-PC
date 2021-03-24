\ COLOR.SEQ     Color board support for F-PC            by Tom Zimmer

\ For Color boards, the following values may be used for background
\ or forground.

$00 CONSTANT BLACK       \ black
$01 CONSTANT BLUE        \ blue
$02 CONSTANT GREEN       \ green
$03 CONSTANT CYAN        \ cyan
$04 CONSTANT RED         \ red
$05 CONSTANT MAGENTA     \ magenta
$06 CONSTANT BROWN       \ brown
$07 CONSTANT LTGRAY      \ light gray

$08 CONSTANT DKGRAY      \ dark gray   All following, blink in Background.
$09 CONSTANT LTBLUE      \ light blue
$0A CONSTANT LTGREEN     \ light green
$0B CONSTANT LTCYAN      \ light cyan
$0C CONSTANT LTRED       \ light red
$0D CONSTANT LTMAGENTA   \ light magenta
$0E CONSTANT YELLOW      \ yellow
$0F CONSTANT WHITE       \ white

16 ARRAY COLORS         \ an array of colors for F-PC to use

: BG!   ( n1 n2 -- )    \ set color n1 into attribute position n2 background
        1- 0MAX 2* COLORS + C! ;

: BG@   ( n1 -- n2 )    \ get the color n2 of attribute position n1 background
        1- 0MAX 2* COLORS + C@ ;

: FG!   ( n1 n2 -- )    \ set color n1 into attribute position n2 forground
        1- 0MAX 2* COLORS + 1+ C! ;

: FG@   ( n1 -- n2 )    \ get the color n2 of attribute position n1 forground
        1- 0MAX 2* COLORS + 1+ C@ ;

BLUE    1 BG!   LTGREEN 1 FG!   \ set the default attribute forground and
RED     2 BG!   WHITE   2 FG!   \ background colors.
GREEN   3 BG!   WHITE   3 FG!
RED     4 BG!   WHITE   4 FG!
BLUE    5 BG!   GREEN   5 FG!
RED     6 BG!   WHITE   6 FG!
GREEN   7 BG!   WHITE   7 FG!
RED     8 BG!   WHITE   8 FG!


: >FG           ( n1 --- )      \ set the actual forground color to n1 NOW!
                attrib c@ $F0 and + attrib c! ;

: >BG           ( n1 --- )      \ set the actual background color to n1 NOW!
                $10 * attrib c@ $0F and + attrib c! ;

: COLORSET      ( -- )          \ set the current screen colors to be the
                                \ default normal color combination
                attrib c@ =: normval ;

: >1BGFG        ( --- ) 1 BG@ >BG 1 FG@ >FG ;
: >2BGFG        ( --- ) 2 BG@ >BG 2 FG@ >FG ;
: >3BGFG        ( --- ) 3 BG@ >BG 3 FG@ >FG ;
: >4BGFG        ( --- ) 4 BG@ >BG 4 FG@ >FG ;
: >5BGFG        ( --- ) 5 BG@ >BG 5 FG@ >FG ;
: >6BGFG        ( --- ) 6 BG@ >BG 6 FG@ >FG ;
: >7BGFG        ( --- ) 7 BG@ >BG 7 FG@ >FG ;
: >8BGFG        ( --- ) 8 BG@ >BG 8 FG@ >FG ;

: >COLOR        ( --- )         \ Select hilighting for color monitor.
                ['] >1BGFG IS >ATTRIB1
                ['] >2BGFG IS >ATTRIB2
                ['] >3BGFG IS >ATTRIB3
                ['] >4BGFG IS >ATTRIB4
                ['] >5BGFG IS >ATTRIB5
                ['] >6BGFG IS >ATTRIB6
                ['] >7BGFG IS >ATTRIB7
                ['] >8BGFG IS >ATTRIB8
                >NORM ;

' >COLOR IS INITCOLOR   \ Enable attributes according to video board.

VMODE.SET               \ RESET VIDEO MODE TO CURRENT VIDEO BOARD.

\ Liquid Crystal Display, or Least Common Denominator.   Your choice.

: >LCD          ( --- )
                ['] >REV  DUP IS >ATTRIB1 DUP IS >ATTRIB5
                          DUP IS >ATTRIB2 DUP IS >ATTRIB6
                          DUP IS >ATTRIB3     IS >ATTRIB7
                ['] >NORM DUP IS >ATTRIB4     IS >ATTRIB8
                >NORM ;

DECIMAL


