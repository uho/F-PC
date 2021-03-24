\ WINDOW.SEQ    Window code for F83                     by Graig A. Lindley

POSTFIX         \ Use the postfix assembler syntax.

comment:

IBM window program
by
Craig A. Lindley
Manitou Springs, Colorado
November 1985

Dr. Dobbs #117 July 1986

: ?comp state @ not abort" Compilation only" ;
: ?pairs   <> abort" Bad Case Statement" ;

: case   ?comp csp @ !csp 4 ; immediate

: of     4 ?pairs
         compile over compile = compile ?branch
         here 0 , compile drop 5 ; immediate

: endof  5 ?pairs compile branch here 0 ,
         swap >resolve 4 ; immediate
: endcase  4 ?pairs ( compile drop )
           begin sp@ csp @ <>
           while >resolve
           repeat csp ! ; immediate

comment;

\ write count # of chars with attrib at cursor position

code chra ( attrib,count--)
  cx pop ax pop ah bl mov
  bh bh xor 9 # ah mov        \ char in al, func. code in ah
  si push 16 int si pop       \ do video interrupt
  next end-code

\ write 1 char with attrib at cursor - update cursor position

code chra+ ( attrib--)
  ax pop ah bl mov bh bh xor
  1 # cx mov 9 # ah mov       \ char in al, func. code in ah
  si push 16 int              \ count-1 write char/attrib
  3 # ah mov 16 int dl inc 2 # ah mov 16 int
  si pop                      \ inc cursor position
  next end-code

\ read char and attrib at cursor position

code rdchra  ( --attrib )
  0 # bh mov 8 # ah mov        \ pg = 0  func. code = 8
  si push 16 int si pop        \ do video interrupt
  1push  end-code              \ attrib to stack

\ put char with attrib at x,y

: putch ( x,y,attrib--) >r at r> 1 chra ;

\ get char with attrib at x,y

: getch ( x,y--attrib) at rdchra ;

\ draw count # of chars starting at x,y

: drawrow ( x,y,attrib,count--) >r >r at r> r> chra ;

\ scroll specified window up n lines

code scrlup ( xul,yul,xlr,ylr,n,attrib--)
  bx pop bl bh mov di pop
  dx pop dl dh mov ax pop al dl mov  \ dx has lr x y
  cx pop cl ch mov ax pop al cl mov  \ cx has ul x y
  di ax mov si push bp push          \ save regs
  6 # ah mov 16 int          \ ax # of lines func code ah
  bp pop si pop              \ restore forth regs
  next end-code

\ DOS memory management support
\ tell DOS to allocate memory bytes

code calloc ( #bytes--seg,? )
  bx pop 4 # cl mov bx cl shr       \    -- maxp error code F
  bx inc 72 # ah mov 33 int         \ func. code 48h, int 21h
  u< if bx push ax push ax ax xor   \ if C then error
     else ax push -1 # ax mov
     then 1push end-code

\ tell DOS to free memory segment

code free ( seg--? )
  ax pop es push ax es mov          \ error code F
  73 # ah mov 33 int                \ func code 4Ah, int 21h
        es pop
  u< if ax push ax ax xor           \ if C then error
     else -1 # ax mov
     then 1push end-code

\ tell DOS to shrink or expand allocated memory segment

code setblock ( #bytes--? )
        bx pop es push
        cs ax mov ax es mov           \ maxp error code F
        4 # cl mov bx cl shr   \ bx has # of paragraphs
        bx inc 74 # ah mov 33 int     \ func code 4Ah, int 21h
        es pop
     u< if bx push ax push ax ax xor   \ if C then error
        else -1 # ax mov
        then 1push end-code

\ fetch word from extended memory

code e@ ( seg,addr--n)
        bx pop ax pop           \ seg in es, addr in bx
        es push ax es mov
        es: 0 [bx] ax mov       \ get the data on stack
        es pop
        1push end-code

\ store word in extended memory

code e!  ( n,seg,addr--)
        bx pop dx pop ax pop
        es push dx es mov
        ax es: 0 [bx] mov       \ store the data
        es pop
        next end-code

\ read current cursor location

code rdcur ( --x,y)
  si push 0 # bh mov
  3 # ah mov 16 int               \ func. code 3, int 10h
  si pop ah ah xor
  dl al mov ax push dh al mov
  1push end-code

\ Window Control Block (WCB) record layout            860704clz)

 0 constant ulx      2 constant uly       \ upper left corner
 4 constant width    6 constant height
 8 constant curx    10 constant cury      \ current cursor pos
12 constant oldx    14 constant oldy      \ old cursor pos
16 constant bufseg  18 constant oldwcbseg \ seg storage
20 constant attrib                        \ window attrib.

22 constant recordsize
15 constant border                        \ border attribute
HEX
B000 constant vseg                        \ start video memory
  \ B800 = color graphics adapter, B000 = monochrome monitor
variable wcbseg                           \ current WCB seg
DECIMAL

\ WCB extended memory access

\ store word n at addr in current WCB

: wcbseg! ( n,addr--) wcbseg @ swap e! ;

\ fetch word from addr in current WCB

: wcbseg@ ( addr--n) wcbseg @ swap e@ ;

: top ( --)
  ulx wcbseg@ uly wcbseg@ [ 201 border 256 * + ] literal putch
  ulx wcbseg@ 1+ uly wcbseg@ [ 205 border 256 * + ] literal
  width wcbseg@ drawrow
  ulx wcbseg@ width wcbseg@ + 1+ uly wcbseg@
  [ 187 border 256 * + ] literal putch ;

: bottom ( --)
  ulx wcbseg@ uly wcbseg@ height wcbseg@ + 1+
  [ 200 border 256 * + ] literal putch
  ulx wcbseg@ 1+ uly wcbseg@ height wcbseg@ + 1+
  [ 205 border 256 * + ] literal width wcbseg@ drawrow
  ulx wcbseg@ width wcbseg@ + 1+ uly wcbseg@ height wcbseg@ + 1+
  [ 188 border 256 * + ] literal putch ;

: sides ( --)
  uly wcbseg@ height wcbseg@ + 1+ uly wcbseg@ 1+
  do ulx wcbseg@ i [ 186 border 256 * + ] literal putch
     ulx wcbseg@ width wcbseg@ + 1+ i
     [ 186 border 256 * + ] literal putch
  loop ;

\ used by scn->buf and buf->scn

label saveh   nop nop         \ storage for height parameter
label savew   nop nop         \             width parameter
label saveptr nop nop         \             start pointer
label savesi  nop nop         \             forth's IP reg
label saveds  nop nop         \             current ds reg

\ move data from screen to memory buffer

HEX

code scn->buf ( x,y,width,height,seg--)
  cld dx pop 0 # di mov saveh #) pop savew #) pop ax pop
  A0 # bl mov bl mul bx pop bx shl bx ax add ax saveptr #) mov
  si savesi #) mov ds ax mov ax saveds #) mov vseg # ax mov
  es push dx es mov
  ax ds mov cs: saveptr #) si mov cs: saveh #) cx mov
  here cx push cs: savew #) cx mov rep movs
       cs: saveptr #) si mov A0 # si add si cs: saveptr #) mov
       cx pop
  loop
  cs: saveds #) ax mov ax ds mov
  savesi #) si mov
  es pop
  next end-code

DECIMAL

\ move data from memory buffer to screen

HEX
code buf->scn  ( seg,x,y,width,height--)
  cld saveh #) pop savew #) pop ax pop A0 # bl mov
  bl mul bx pop bx shl bx ax add ax saveptr #) mov
  si savesi #) mov ds ax mov ax saveds #) mov ax pop ax ds mov
  es push
  vseg # ax mov ax es mov 0 # si mov cs: saveptr #) di mov
  cs: saveh #) cx mov
  here cx push cs: savew #) cx mov rep movs
       cs: saveptr #) di mov A0 # di add di cs: saveptr #) mov
       cx pop
  loop
  cs: saveds #) ax mov ax ds mov savesi #) si mov
  es pop
  next end-code
DECIMAL

\ moves screen data to memory buffer
\ and then draws the actual window frame

: wndw ( --)
   ulx wcbseg@ uly wcbseg@
   width wcbseg@ 2+ height wcbseg@ 2+
   bufseg wcbseg@ scn->buf
   top sides bottom ;

: clearwindow ( --)
   ulx wcbseg@ 1+                   \ upper left x
   uly wcbseg@ 1+                   \ upper right y
   ulx wcbseg@ width wcbseg@ +      \ lower left x
   uly wcbseg@ height wcbseg@ +     \ lower right y
   0 attrib wcbseg@ scrlup          \ scroll entire window
   0 curx wcbseg! 0 cury wcbseg! ;  \ home window cursor

: window ( x,y,width,height,attrib--? )
   recordsize calloc                  \ reserve space for wcb
     if wcbseg @ >r wcbseg ! r>
        oldwcbseg wcbseg! attrib wcbseg!
        2dup 2+ swap 2+ * 2* calloc \ reserve space for scr buf
          if bufseg wcbseg!         \ save buffer seg
             height wcbseg! width wcbseg!    \ save parameters
             uly    wcbseg! ulx   wcbseg!    \ in new wcb
             rdcur oldy wcbseg! oldx wcbseg! \ get old cur pos
             wndw clearwindow true
          else ." Buffer allocation failure." cr
             wcbseg @ free drop drop 0
          then
     else ." WCB allocation failure." abort ( drop drop 0 )
     then ;

\ window parameter checking

: wfit ( ?--) cr abort" Window won't fit on crt." ;

: openwindow ( x,y,width,height,attrib--? )
   depth 5 >=
     if >r 4dup rot + 2+ 24 <=
       if + 2+ 79 <=
         if r> window
         else cr ." ULX and/or WIDTH incorrect." wfit
         then
       else cr ." ULY and/or HEIGHT incorrect." wfit
       then
     else cr ." Incorrect # of parameters specified." quit
     then ;

\ close the current window
\ free wcb and buffer memory then unlink window

: closewindow ( --) wcbseg @ 0 <>        \ if window exists
   if bufseg wcbseg@                     \ get buffer seg addr
      ulx wcbseg@ uly wcbseg@
      width wcbseg@ 2+ height wcbseg@ 2+
      buf->scn                     \ move data back to screen
      oldx wcbseg@ oldy wcbseg@ at
      bufseg wcbseg@ free drop     \ free buffer seg memory
      wcbseg @ free drop           \ free wcb seg memory
      oldwcbseg wcbseg@ wcbseg !   \ unlink this window
    else cr ." No open windows." cr
    then ;

\ position cursor in window
\ if paras out of range do the best and still stay in window

: wat ( x,y--)
    swap dup abs width wcbseg@ 1- >    \ x not within window
      if drop width wcbseg@ 1-         \ set x to max in window
      then curx wcbseg!                \ save new cursor x pos
    dup abs height wcbseg@ 1- >        \ y not within window
      if drop height wcbseg@ 1-        \ set y to max in window
      then cury wcbseg!                \ save new cursor y pos
    curx wcbseg@ ulx wcbseg@ + 1+      \ actual cursor x
    cury wcbseg@ uly wcbseg@ + 1+      \ actual cursor y
    at ;

\ read window cursor position

: rdwcur ( --x,y) curx wcbseg@ cury wcbseg@ ;

\ read attribute of character at cursor in window

: rdwcha ( x,y--attrib) wat rdchra ;

\ scroll window up for blank line at bottom

: scrollwindow ( --)
   ulx wcbseg@ 1+ uly wcbseg@ 1+   \ upper left corner to scroll
   ulx wcbseg@  width wcbseg@ +    \ lower right x coordinate
   uly wcbseg@ height wcbseg@ +    \ lower right y coordinate
   1 attrib wcbseg@ scrlup ;       \ up one line

                \ cr in current window
: crout         ( --) rdwcur nip 0 swap wat ;

                \ line feed in current window
: lfout         ( --) rdwcur 1+
    dup height wcbseg@ 1- >     \ cursor out of window
     if 1- scrollwindow
     then wat ;

                \ execute backspace in current window
: bsout         ( --) rdwcur over
                if swap 1- swap wat else 2drop then ;

: bell          ( --) 7 (emit) ;

: wemit         ( char--) dup 32 <
    if case                          \ handle controls
         7 of bell   endof
         8 of bsout  endof
        10 of lfout  endof
        13 of crout  endof drop
       endcase
    else                             \ display character
      attrib wcbseg@ 256 * +         \ char now char/attrib
      rdwcur rot chra+               \ output char & adv. cursor
      drop dup width wcbseg@ 1- =    \ at end of line
        if drop lfout crout          \ if do lfcr
        else 1+ curx wcbseg!         \ store new x coordinate
    then then ;

: wcr ( --) 13 wemit 10 wemit ;

: wtype ( addr,n--) 0 ?do count wemit loop drop ;


comment:

\ use DOS memory manager to give forth a full 64k segment

: initialize ( --)
    cr ." Memory management "
    -1 setblock                        \ request FFFF bytes
      if ." initialized." 0 wcbseg !   \ initialize link var
      else ." error." abort
      then cr ;

comment;

  7 constant normal          15 constant highint
112 constant reverse        128 constant blink

: enterwindow ( x,y,width,height,attrib--) openwindow
    if ['] wemit is emit ['] wcr is cr 0 0 wat then ;

: exitwindow ( --) closewindow wcbseg @ 0=
    if ['] (emit) is emit ['] crlf is cr then ;

: smash ( --) exitwindow ;

: window1 ( --) 0 0 20 10 reverse openwindow ;

: window2 ( --) 2 1 70 8 normal openwindow ;

: window3 ( --) 7 6 69 10 reverse openwindow ;

: window4 ( --) 10 9 59 4 highint openwindow ;

: msg1 ( --) " Attitudes are contagious. " wtype ;

: msg2 ( --) " Is yours worth catching? " wtype ;

: msg3 ( --) "  ** Window 4 ** " wtype ;

: msg1out ( --) 0 0 wat 20 0 do msg1 loop ;

: msg2out ( --) 0 0 wat 20 0 do msg2 loop ;

: msg3out ( --) 0 0 wat 80 0 do msg3 loop ;

: fillscreen ( --) 0 0            \ fill with rev video A's
    [ ascii A reverse 256 * + ]   \ calculate char/attrib code
    literal 2048 drawrow ;

: wait ( --) 2 tenths ;

: demo ( --) fillscreen window1
    if 0 0 wat msg1 wait wcr wait 7 wemit wcr wait
      " Really ?" wtype wait 8 wemit 8 wemit wait
      10 5 wat wait window2
        if msg2out wait window3
          if ( 0 10 wat 24 wtriad wait ) window4
            if msg3out wait closewindow wait closewindow
              wait clearwindow msg2out wait closewindow
              ( 0 wlist wait wait wait ) closewindow
    then then then then dark ;


  