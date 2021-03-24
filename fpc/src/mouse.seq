\ MOUSE.SEQ     Mouse driver software, requires MOUSE.SYS       by Ray Isaac

comment:

  Mouse support for Forth.  Adding mouse support to a program is (if you
have looked at this file) not a trivial problem.  The technique chosen here
is to install a driver at the level of KEY? which monitors the movement of
the mouse and turns on its cursor if it is moved. A press of a key restores
the normal terminal cursor.

  In this system only the outside buttons of a three button mouse are used.
The left button is used to select or trigger an operation, while the right
button is always equivelant to the ESC key, which will usually cancel an
operation.

  This generic mouse driver is enhanced at each application level that wants
to use the mouse with an application specific button driver.  The application
level button drivers are installed into the defered word DOBUTTON.  The
button driver is expected to interpret the button presses, and perform the
appropriate function.  Several button drivers are already present in F-PC,
which can be used as examples for how to make button drivers.  See the
file MOUSEY.SEQ for further information.

  The mouse is automatically initialized at program boot time, if you want
to disable or re-enable the mouse after program boot time, you can use the
following words:

        HIDE.MOUSE      disables mouse operation
        SHOW.MOUSE      enables mouse operation if a mouse driver present

  This code was originally written by Ray Isaac at Calos Systems.

  This code has been extensively modified and extended by Tom Zimmer at
Maxtor.
**************  UPDATE *********************
I changed from the forth generated (cursor) mouse cursor to the mouse
interupts HARDWARE mouse cursor.  Mainly to provide a separate text
cursor and a separate mouse cursor while in the editor. MDC  26/08/90

*************** UPDATE by Tom Zimmer 03/30/91 13:25:13.45 **************
Integrated this file and MOUSEY2.SEQ into F-PC. Made a minor change to
prevent the mouse cursor from showing up on the screen until it is actually
moved.  Some people may have a mouse driver installed, but not a mouse. It
would be pretty irritating to have a mouse cursor in the middle of your
screen and not be able to get rid of it.  Thank you Mike Christopher for
this nice mouse driver enhancement.

comment;

\ prefix                  \ use prefix assembler syntax

decimal

only forth also hidden definitions also


0 value havemouse       \ was a good mouse driver present at boot?
0 value mousechar       \ character to return from mouse key press
0 value badmouse        \ non-zero if bad  driver present
0 value lastx           \ previous location of mouse X and Y
0 value lasty

headerless

0 value mousewasdown    \ was mouse button down last time we saw it
0 value last-cursor     \ cursor shape before we moved the mouse
0 value fixcur?         \ do we need to reset cursor position & size?

\ defer dobutton          ' noop is dobutton    \ moved to UTILS

headers

code show.ms    ( -- )          \ turn On the hardware mouse cursor
                mov ax, # 1     \ we are using hardware cursor
                int  51
                next
                end-code

code hide.ms    ( -- )          \ turn OFF the hardware mouse cursor
                mov ax, # 2     \ use this to hide mouse before screen updates
                int  51
                next
                end-code

headerless

code init.mouse ( --- )         \ initialize mouse if good driver present
                mov ax, # 0     \ mouse driver init.mouse function code
                int  51         \ call mouse driver
                cmp ax, # 0
             0= if      mov ' badmouse >body # true word
                        next
                then
                mov ' badmouse >body # false word
                mov ax, # 14            \ function code to disable light pen
                                        \ emulation.  mouse driver turned
                                        \ this on as default in
                                        \ function 1, done in init.mouse1
                int  51                 \ call mouse driver.
                mov ' mouseflg  >body # true word
                mov ' havemouse >body # true word

\                mov ax, # $0a           \ function code to set cursor type
\                mov bx, # $01           \ hardware mouse driver cursor
\                mov cx, # 0             \ start scan  (screen mask)
\                mov dx, # 0             \ end scan    (cursor mask)
\                int  51                 \ call mouse driver.

                next
                end-code

code getmous    ( --dx dy buttons.status )      \ get mouse information
                mov ax, # 03
                int  51
                push cx
                push dx
                and bx, # 3
                push bx
                next
                end-code

code setmous    ( x y -- )      \ set mouse position in pixels on screen
                pop dx                  \ get Y position in pixels
                pop cx                  \ get X position in pixels
                mov ax, # $04           \ function four, set mouse position
                int 51
                next    end-code

: nomouse       ( --- )                 \ mark us as not having a mouse
                off> mouseflg
                off> havemouse ;

code mouse.scale ( --- )                \ adjust mouse scaling for display
                mov cx, # 0
                mov dx, ' rows >body
                dec dx
                shl dx, # 1
                shl dx, # 1
                shl dx, # 1
                mov ax, # 08            \ set max Y
                int  51
                mov cx, # 0
                mov dx, ' cols >body
                dec dx
                shl dx, # 1
                shl dx, # 1
                shl dx, # 1
                mov ax, # 07            \ set max x
                int  51
                next    end-code

forth definitions

headers

: mousexy       ( --- x1/y1 )   \ x=0-79, y=0-24
                mouseflg 0= if 0 0 exit then
                getmous
                drop u8/ rows 1- min
                swap u8/ cols 1- min swap ;

: mousexy!      ( x1/y1 -- )    \ position mouse in characters on screen
                mouseflg 0= if !> lasty !> lastx exit then
                hide.ms
                rows 1- min dup !> lasty 8* swap
                cols 1- min dup !> lastx 8* swap setmous
                show.ms ;

: mousebutton   ( --- n1 )              \ n1=0,1,2,4 or a combination
                mouseflg 0= if false exit then
                getmous nip nip 3 and ;

: hide.mouse    ( --- )                 \ turn off the mouse cursor
                hide.ms                 \ mdc
                off> mouseflg ;

: show.mouse    ( --- )                 \ enable display of mouse cursor
                show.ms                 \ mdc
                havemouse =: mouseflg
                lastx lasty mousexy! ;

headerless

: ?menubar#     ( --- n1 )
                -1
                mcolumn
                menubar  count 0
                do      swap over c@ + 1+
                        mousexy drop ( x ) over <       \ lessthan next menu#
                        if      rot drop i -rot leave
                        then    swap count +            \ next bar
                loop    2drop
                dup 0< if drop menubar c@ 1- then
                ;

: track-menu    ( --- )         \ track menu with mouse

                mcol >r ?menubar# =: mcol
                r> mcol -
                if      hide.ms recoverscr show.ms
                then
                mousexy nip mline - 0max
                mcol  2* menulist + @ 2+ c@ min =: mrow
                hide.ms  showmenus  show.ms
                ;

headers

: track-mouse   ( --- )                 \ follow the mouse on screen
                mousexy lastx lasty d- or       \ has mouse moved?
                if  show.ms
                    mousexy =: lasty =: lastx
                    ['] dobutton >body @ ['] mbutton = if  \ a MENU is active
                        track-menu
                    then
                then
                ;

hidden definitions

: defbutton     ( --- )                 \ default button handler
                mousebutton
                case
                2 of    27 ( ESC )   =: mousechar   endof
                1 of    13 ( Enter ) =: mousechar   endof
                        drop
                endcase ;

: initmouse     ( --- )                 \ initialize the mose if present
                nomouse
                0 204 @L 0<>            \ interupt vector may be 51 ok
                                        \ if it is <>0
                if      init.mouse
                        badmouse ?exit
                        mouse.scale     \ adjust mouse scaling to screen
                        off> mousechar
                        off> mousewasdown
                        ['] defbutton is dobutton       \ default button
                        mousexy =: lasty =: lastx
                        0 0 mousexy!
                        hide.ms
                then                                    \ handler
                ;

initmouse                       \ initialize the mouse now so we can test it

headerless

: ?mouseinit    ( --- )         \ initialize mouse if present at boot time
                initmouse
                defers initstuff
                ;

' ?mouseinit is initstuff

forth definitions
: tracker ( - )  \ tracks the mouse on the screen OR a menu
       track-mouse
       ;

: mousekey?     ( --- f1 )      \ new mouseable version of KEY?
                defers key?
                dup 0=

         if      mouseflg 0= ?exit
                        mousewasdown
                        if
                                begin   mousebutton 0=
                                      tracker
                                until
                        then    off> mousewasdown
                        off> mousechar
                        tracker
                        mousebutton             \ if button pressed then
                        if      dobutton        \ handle it
                                on> mousewasdown
                        then
                then
                ;

' mousekey? is key?

: mousekey      ( -- CHAR )             \ allow mouse press to return key
                show.ms
                begin   pause
                        key?
                        mousechar or
                until   mousechar ?dup
                hide.ms
                if      off> mousechar
                        on> mousewasdown
                else    bioskey dup 127 and 0=
                        if      flip dup 3 =
                                if      drop 0
                                else    127 and 128 or
                                then
                        else    255 and
                        then
                then    keyfilter
                ;

' mousekey is key

only forth also definitions

headers

