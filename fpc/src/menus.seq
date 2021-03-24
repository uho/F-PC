\ SEDMENU.SEQ   Visual menu selection tool for SED      by Tom Zimmer

only forth also definitions hidden also

1 value default-mline   defer mline     ' default-mline   is mline
0 value default-mcolumn defer mcolumn   ' default-mcolumn is mcolumn

0    value mcol         \ current menu column
0    value mrow         \ Item to hilight in column
0    value menukey

defer doother   ' drop is doother       \ Throw away any key we don't want
                                        \ for now at least.

defer mbutton ' noop is mbutton         \ menu button function

                                        \ n1 = number of menu entries
                                        \ a1 = address to be filled by ENDMENU
                                        \ n2 = running total of menu length
: newmenu       ( name --- a1 n2 ) \ creates "name" the menu name
                create
                xhere paragraph + dup xdpseg ! xseg @ - , xdp off
                here 0 c, 0 ;    \ build start of menu string list

                                        \ a1 = address to be filled by ENDMENU
                                        \ n1 = running total of menu length
: newmenubar    ( name --- a1 n1 )      \ make a new menubar of "name"
                create here 0 c, 0 ;

: endmenu       ( a1 n1 --- )
                swap c! ;

: +,"           ( n1 | string --- n1+1 )
                1+ ,"  ;                \ lay in string

                                        \ a1 = address of count of strings
                                        \ n1 = current running total of lines
                                        \ string" = menu text line to display
                                        \ function = functio name for line
                                        \ n1+1 = resulting running total
                                        \ compile a new menu line
: menuline"     ( a1 n1 | string" function --- n1+1 )
                +,"                     \ lay in string
                ' xhere !L              \ lay function in LIST space
                2 xdp +! ;              \ bump LIST space

defer makefile  ' noop is makefile
defer editfile  ' noop is editfile
defer dolisting ' noop is dolisting
defer dofhelp   ' noop is dofhelp

I: mbye         ( --- )
                0 rows 1- at bye ;

I: ledit_restore ( --- )
\u <.stat>      statv @ if <.stat> then
                off> mcol
                0 rows 1- at
                editbuf off
                off> ecursor
                off> stripping_bl's
                0 =: ex rows 1- =: ey ;

I: editafile    ( --- )
                editfile cr ledit_restore ;

I: makeafile    ( --- )
                makefile cr ledit_restore ;

I: openfile     ( --- )
                #tib @ >in ! file
\u <.stat>      statv @ if <.stat> then
                ;

I: fhelp        ( --- )
                dofhelp ledit_restore ;

I: do-dos       ( --- )
                clearmem
                savescr                 \ save the screen
                dark
                cr >attrib1 ."  Type EXIT to return to F-PC. " >norm cr
                here dup off $sys dup 2 =
                if      ."  Couldn't find COMMAND.COM     Press a key"
                        key drop
                then    8 =
                if      ." Not enough memory to run DOS   Press a key"
                        key drop
                then
                restscr ;

\ Patch functions into the line editor

>keys1

' do-dos        is  dolf        \ Invoke DOS                    Ctrl-J
' fhelp         187 lkey!       \ Invoke HELP                   F1
' makeafile     177 lkey!       \ Make a new file               Alt-N
' openfile      152 lkey!       \ Open a file                   Alt-O
' editafile     146 lkey!       \ Edit a file                   Alt-E
' dolisting     153 lkey!       \ print current file            Alt-P

>keys2

headerless

newmenu dfile$
        menuline"  Help me learn F-PC     F1 " fhelp
        menuline" 컴컴컴컴컴컴컴컴컴컴컴컴컴 " noop
        menuline"  New  file           Alt-N " makeafile
        menuline"  Open file           Alt-O " openfile
        menuline"  Edit  current file  Alt-E " editafile
        menuline"  Print current file  Alt-P " dolisting
        menuline" 컴컴컴컴컴컴컴컴컴컴컴컴컴 " noop
        menuline"  Dos Shell      Ctrl-Enter " do-dos
        menuline" 컴컴컴컴컴컴컴컴컴컴컴컴컴 " noop
        menuline"  Quit & return to DOS      "  mbye
endmenu

newmenu dumy$
        menuline"  Help me learn F-PC     F1 " fhelp
endmenu

0 value defsave

headers

newmenubar default-bar
+," File  "
+,"  컴 Press ENTER and use - to walk Up & Down the menu 컴 "
endmenu
create default-list    dfile$ , dumy$ ,

default-bar   value menubar
default-list  value menulist

I: dofunc       ( col row --- )         \ perform function for menu item
                1- 0MAX 2* >r 2* menulist + @ @ +XSEG r> @L
                nosetcur off
                cursor-on
                execute
                cursor-off
                nosetcur on ;

I: .horizontal  ( a1 --- )              \ display a horizontal menu
                mcolumn mline at
                0 swap count 0
                do      i mcol =
                        if      nip @> #out swap >attrib4
                        else    >attrib1
                        then    space count 2dup type +
                loop    drop
                >attrib1 COLS @> #out - spaces >norm
                ( col --- ) ?DOSIO
                if      cursor-on 1+ mline at  else drop  then    ;

I: .vertical    ( a1 --- )              \ display a vertical menu
                >r menubar 1+ dup >r mcol 0
                ?do     count +
                loop    r> -    \ calculate the column of vertical menu
                mcolumn +
                mline 1+        \ row number of vertical menu
                r@ 1+ c@        \ width
                >r over r> + 1+ over r@ c@ + menubox
                0 0     \ default cursor location if not in any menu row.
                r> count 0
                do      tx 1+ ty ( 1+ ) i +
                        ?DOSIO
                        if      at
                        else    =: #line =: #out
                        then
                        i 1+ mrow =
                        if      >r 2drop
                                @> #out @> #line r>
                                >rev
                        then    count 2dup type + >norm
                loop    drop at ;

I: .menubar     ( --- )
                ?doingmac ?exit
                menubar .horizontal ;

I: .menu        ( --- )
                ?doingmac ?exit
                menulist mcol  2* + @ 2+ .vertical ;

: showmenus     ( --- )
                mrow 0>
                if      .menubar .menu
                else    recoverscr .menubar
                then    ;

                                \ find the first uppercase letter in string
I: ucscan       ( a1 --- c1 )   \ a1 is a counted string, c1 = char or NULL
                0 swap count bounds
                ?do     i c@ 'A' 'Z' between
                        i c@ '0' '9' between or
                        if      drop i c@ leave
                        then
                loop    ;

I: 1st-rowchar  ( --- c1 )              \ return first char of row message
                mcol  2* menulist + @ 2+ \ addr of menu list
                count mrow 1- min 0MAX 0
                ?do     count +         \ step to next item
                loop    1+ c@ ;

I: ?menukey     ( c1 f1 --- c1 f2 )     \ sets mcol  or mrow
                over =: menukey
                mrow 0=                         \ are we on the menubar
                if      menukey 13 =            \ did we press <enter>
                        if      1 =: mrow       \ pop down menu
                                drop true
                        else                    \ else search for menu name
                                0 menubar count 0
                                do      dup ucscan dup 0= or
                                        bl or menukey bl or =
                                        \ dup 1+ c@ bl or menukey bl or =
                                        if      over =: mcol
                                                1 =: mrow
                                                2swap 2drop 0 0
                                                2swap
                                                leave
                                        else    1. d+
                                                count +
                                        then
                                loop    2drop
                        then
                else                    \ search for name in current menu
                        mcol  2* menulist + @         \ addr of menu list
                        2+ 1 swap count 0
                        ?do     dup ucscan bl or menukey bl or =
                                menukey bl <> and
                                if      drop =: mrow
                                        13              \ 13 = return
                                        false           \ process command
                                        2swap
                                        leave
                                else    1. d+           \ bump count
                                        count +         \ step to next item
                                then
                        loop    2drop
                then    ;

I: ?domkey      ( c1 --- c1 | 0 )
                dup 199 =               \ HOME
                if      0=
                        mrow 0=                 \ if 0 then
                        if      off> mcol       \ home to left
                        else    off> mrow
                        then                                    then
                dup 207 =               \ END
                if      0=
                        mcol  2* menulist + @ 2+ c@ !> mrow     then
                dup 205 =               \ RIGHT
                over bl = or
                if      0=
                        recoverscr
                        mcol menubar c@ 1- =
                        if      0
                        else    mcol   1+ menubar c@ 1- min
                        then    =: mcol
                        mrow 1 min !> mrow                      then
                dup 203 =               \ LEFT
                if      0=
                        recoverscr
                        mcol 0=
                        if      menubar c@ 1-
                        else    mcol   1- 0MAX
                        then    =: mcol
                        mrow 1 min !> mrow                      then
                dup 200 =               \ UP
                if      0=
                        mrow 1- 0MAX !> mrow
                        begin   1st-rowchar 196 = \ skip over horizontal line
                                mrow 0> and
                        while   mrow 1- 0MAX !> mrow
                        repeat                                  then
                dup 208 =               \ DOWN
                if      0=
                        mrow 1+
                        mcol  2* menulist + @ 2+ c@ dup>r min !> mrow
                        begin   1st-rowchar 196 = \ skip over horizontal line
                                mrow r@ < and
                        while   mrow 1+ r@ min !> mrow
                        repeat  r> drop
                then    dup 13 = if 0= then ;

: menu          ( --- )
                savecursor              \ save cursor position
                ['] mbutton save!> dobutton
                cursor-off
                nosetcur on
                off> mrow
                savescr                 \ Save original screen
                save> mcol
                ON>  mcol .menubar      \ display menubar without hilite
                restore> mcol
                savescr                 \ save it again
                begin   showmenus
                        key dup 27 <>           \ while not ESC
                           over 13 <> and       \ and not carraige return
                        ?menukey                \ or menu key
                        if      ?domkey
                        then    ?dup
                until
                restscr restscr         \ Recover original screen
                restore> dobutton
                restcursor
                dup     13 =            \ is char a Carraige Return
                if      drop
                        mcol mrow dofunc  \ then do the function
                else    dup 27  =
                        if      drop    \ discard if ESC
                        else    doother \ else process the key
                        then
                then    nosetcur off ;

\ WARNING the two words following MUST BE USED together in a single
\ definition. They play with the RETURN stack, and can cause big
\ problems if not balanced.

: savemenu      ( --- )         \ save current menu setup
                2r>
                save> doother
                save> menubar
                save> menulist
                save> mline
                save> mcolumn
                2>r ;

: restmenu      ( --- )         \ restore to previous menu setup
                2r>
                restore> mcolumn
                restore> mline
                restore> menulist
                restore> menubar
                restore> doother
                2>r ;

: defmenu       ( --- )
                defsave =: mcol
                savemenu
                default-bar   =: menubar
                default-list  =: menulist
                ['] default-mline   is mline
                ['] default-mcolumn is mcolumn
                ['] drop            is doother
                menu
                restmenu
                mcol =: defsave ;

' defmenu is esc-in     \ make the menu pop up when user presses ESC.

only forth also definitions


