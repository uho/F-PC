\ SELECT.SEQ            Menu selection utility          by Tom Zimmer

\ 05/10/91      Enhanced to include Mouse support. T. Zimmer

only forth also definitions hidden also

\ If you try to load this file more than once, then it MUST be unlinked
\ from the defered word chain to avoid a crash caused by ANEW's auto
\ forget feature.

\+ dowbgstuff   cr .( Loading UNLINK utility.)
\+ dowbgstuff   needs unlink.seq                \ unlink utility
\+ dowbgstuff   cr .( UNLINKing DOWBGSTUFF from background processing.)
\+ dowbgstuff   ' bgstuff unlink dowbgstuff     \ remove window stuff from bg

anew selector

0 value winlist
16 array #buf

: %#input       ( x y d1 bgclr fgclr -- d1 f1 ) \ f1 = true for success
                ' noop save!> >edattrib
                savecursor cursor-on >fg >bg
                2over 2>r
                #buf 10 blank
                <# #s #> #buf place
                on> autoclear
                #buf 6 lineeditor >r
                $2020 #buf count + !
                #buf count upper
                #buf %number r> and
                2r> at #buf count 5 min 5 over - spaces type space
                restcursor
                restore> >edattrib ;

: #input        ( x y d1 -- d1 f1 )     \ f1 = true for success
                ltgray black %#input ;

: ?upc          ( c1 -- c2 )            \ uppercase if not function key
                dup 128 <
                if      upc
                then    ;

0 value curwin          \ current window
0 value winline#        \ current window line being compiled
0 value wbgstuff?       \ is window bgstuff active?

: winitem       ( n1 | name -- )        \ window subfield defining word
                create over c, +
                does> c@ curwin + ;

0
2 winitem wlink         \ previous window pointer link
1 winitem worgx         \ window x origin
1 winitem worgy         \ window y origin
1 winitem wrows         \ window rows
1 winitem wcols         \ window columns
1 winitem wx            \ current window x position
1 winitem wy            \ current window y position
1 winitem wbase         \ window base
1 winitem wexit         \ non zero flag if we want to leave window
1 winitem wbg           \ window background color
1 winitem wfg           \ window forground color
1 winitem wexitkey      \ window exit key       (defaults to ESC)
2 winitem wexitfunc     \ window exit function  (defaults to NOOP)
2 winitem wescfunc      \ window ESC key function IF NOT EXIT!
2 winitem wshow         \ window additional display information
2 winitem wintitle      \ window title line
2 winitem winbottom     \ bottom of window message
2 winitem wptr          \ window save pointer
2 winitem wokey         \ function to do when other keys are pressed
2 winitem f1key         \ function to do when F1 pressed
2 winitem wbgstuff      \ window background operation
1 winitem wshown        \ has window been shown?
1 winitem whilite       \ hilited line number
0 winitem winlines      \ start of text for window  ** MUST BE LAST ITEM **
value winrsize

: window        ( c r | name -- )       \ define window c=columns r=rows
                create here !> curwin           \ set as current window
                       winrsize allot
                curwin winrsize erase
                winlist wlink !                 \ link window into list
                curwin !> winlist
                1 max wrows c!                  \ default window size
                1 max wcols c!
                $0A wbase c!                    \ default base DECIMAL
                ltgray wfg c!                   \ default colors
                black wbg c!
                27 wexitkey c!                  \ default exit is ESC
                ['] noop wexitfunc !            \ default exit func NOOP
                ['] noop wescfunc !             \ default ESC func NOOP
                ['] noop wshow !                \ default extra show info
                ['] drop wokey !                \ default for other keys
                ['] noop f1key !                \ default F1 help
                ['] noop wbgstuff !             \ default background function
                here wrows c@ 2* dup allot erase
                off> winline#                   \ reset current line cnt
                does>   !> curwin ;

\ a gackground function can be specified for each window by putting the
\ function into WBGSTUFF.  It will be executed repeatedly whenever the
\ user has selected the window containing the WBGSTUFF fucntion, and
\ the VALUE WBGSTUFF? is ON.

: dowbgstuff    ( -- f )                \ perform some background stuff
                defers bgstuff
                wbgstuff?
                if      wbgstuff perform
                then    ;

' dowbgstuff is bgstuff

: dohilite      ( -- )                  \ perform function for hilited line
                curwin >r
                whilite c@ 2* winlines + @ 1+
                count + @ execute
                r> !> curwin ;

\ WINEXIT can be used inside of any function performed by a window line
\ to force an exit from the window when the function terminates.  This
\ prevents the user from having to press the ESC key to exit the window
\ if it will be more convenient.

: winexit       ( -- )                  \ force exit of current window
                1 wexit c! ;

: winline"      ( c1 | txt" func -- )   \ install text as line of window
                winline#
                dup wrows c@ >= abort" TOO MANY lines for this window!"
                here swap 2* winlines + !       \ install pointer to text
                c,                              \ activation key
                ," ' ,                          \ compile in text to here
                incr> winline# ;                \ bump to next line

: wintitle"     ( | txt" -- )           \ title of window
                here wintitle !
                ," ;

: winbottom"    ( | txt" -- )           \ bottom line message
                here winbottom !
                ," ;

: winorg!       ( x y -- )              \ set origin of current window
                dup worgy c! 1+ wy c!
                dup worgx c! 1+ wx c! ;

: winbg!        ( n1 -- )               \ set window background color
                wbg c! ;

: winfg!        ( n1 -- )               \ set window forground color
                wfg c! ;

: winat         ( x y -- )              \ move to relative pos in window
                -1 max worgy c@ 1+ + wy c!
                 0max  worgx c@ 1+ + wx c!
                wx c@ wy c@ 2dup at !> bline 1- !> tx ;

: wincr         ( -- )                  \ move to beginning of next win line
                worgx c@ 1+ dup wx c!
                wy c@ 1+ dup wy c! at ;

: .wintitle     ( -- )                  \ show window title bar message
                wintitle @ 0= ?exit
                ?cs: wintitle @ count
                wcols c@ over - 0max 2/ worgx c@ + 1+ worgy c@ at \typeL ;

: .winbottom    ( -- )                  \ show bottom of window message
                winbottom @ 0= ?exit
                worgx c@ 2+ worgy c@ wrows c@ + 1+ at
                ?cs: winbottom @ count \typeL ;

: .info         ( -- )
                worgx c@ wcols c@ + 1+ worgy c@
                2dup               at ." \6"
                2dup swap 6 - swap at ."  Home "
                wrows c@ + 1+
                2dup               at ." \6"
                     swap 5 - swap at ."  End " ;

: winshow       ( -- )          \ show the window border and edge information
                savescr
                wbg c@ >bg wfg c@ >fg
                worgx c@ worgy c@ 2dup
                2dup wcols c@ 1+ wrows c@ 1+ d+ box&fill
                mouseflg                                \ if we have mouse
                if      swap 1+ swap at >rev ." []"    \ show close button
                else    2drop
                then
                .wintitle
                .winbottom
                .info
                1 wshown c! ;

: whilite+      ( n1 -- )       \ increment hilited by signed n1
                begin   whilite c@ over + wrows c@ mod whilite c!
                        winlines whilite c@ 2* + @
                        c@
                until   drop ;

: towhome       ( -- )                  \ move to first leagal line of window
                0 whilite c!
                winlines @ c@ 0=
                if      1 whilite+
                then    ;

: towend        ( -- )                  \ move to last leagal line of window
                wrows c@ 1- whilite c!
                winlines wrows c@ 1- 2* + @ c@ 0=
                if      -1 whilite+
                then    ;

: %wintype      ( a1 n1 n2 -- )         \ display special for line n2
                save> attrib cyan >bg black >fg space
                >r
                over 1+ c@ '#' =                \ number edit?
                if      winlines r@ 2* + @ 1+
                        count + @
                        wbase c@ save!> base
                        >body @ @ 5 u.r 2 spaces
                        restore> base
                        8 /string
                else    tuck '±' skip 2dup 2>r nip - >r
                        winlines 3 rpick 2* + @ 1+
                        count + @
                        >body @ count r> 2dup swap - 0max >r
                        min type ( \type ) r> spaces 2r>
                then    r>drop
                restore> attrib \type ;

: wintype       ( a1 n1 n2 -- )         \ display window text for line n2
                >r                                      \ save line
                2dup '±' scan 2dup 2>r nip - \type      \ show leading text
                2r> dup
                if      r@ %wintype
                else    2drop
                then    r>drop ;

-1 value prevhi         \ hilighted line on previous window show
 0 value curhi          \ hilighted line during current window show

: windshow      ( -- )          \ show window contents
                winlines whilite c@ 2* + @ c@ 0=
                if      1 whilite+
                then    0 0 winat
                wbg c@ >bg wfg c@ >fg
                attrib @ winlines wrows c@ 0
                ?do     over attrib ! space
                        prevhi i =              \ if previous hilight line
                        whilite c@ i = or       \ or current hilight line
                        prevhi -1 = or          \ or showing all lines
                        if      whilite c@ i =          \ current hilight line
                                if      attrib c@ $10 /mod swap $10 * +
                                        attrib c!
                                        i !> curhi
                                then
                                2 spaces dup i 2* + @ 1+
                                count i over >r wintype
                                wcols c@ r> - 5 - 0max spaces
                        then    bcr
                loop    2drop wshow perform
                curhi !> prevhi ;

\ : windshow      ( -- )          \ show window contents
\                 winlines whilite c@ 2* + @ c@ 0=
\                 if      1 whilite+
\                 then    0 0 winat
\                 wbg c@ >bg wfg c@ >fg
\                 attrib @ winlines wrows c@ 0
\                 ?do     over attrib ! space
\                         whilite c@ i =
\                         if attrib c@ $10 /mod swap $10 * + attrib c! then
\                         2 spaces dup i 2* + @ 1+
\                         count i over >r wintype
\                         wcols c@ r> - 5 - 0max spaces
\                         bcr
\                 loop    2drop wshow perform ;

: winhide       ( -- )          \ hide the entire window (restore the screen)
                wshown c@ 0= ?exit
                restscr
                0 wshown c! ;

: dowinkey      ( c1 -- )       \ check & perform c1 function
                ?upc winlines           \ for window mechanism to handle key
                wrows c@ 0
                ?do     2dup i 2* + @ c@ ?upc =
                                if      i whilite c!
                                        windshow 50 ms
                                        dohilite
                                        -1 !> prevhi
                                        swap 0= swap    \ change key to NULL
                                        leave
                                then
                loop    drop
                wokey perform ;         \ do other key operation
                                        \ wokey is passed a NULL if key was
                                        \ processed by window mechanism

: doeditvar     ( a1 | name -- )        \ edit a window variable
                create , does>
                save> base wbase c@ base !
                @ dup @ >r                              \ s=a1  r=n1
                winlines whilite c@ 2* + @ 1+
                count                                   \ s=a1, n1
                    tuck '±' scan nip -                 \ s=offset
                wx c@ 4 + + wy c@ whilite c@ +          \ s=x, y
                r> 0 cyan black ( fldcolor ) %#input
                if      drop swap !
                else    2drop drop
                then    restore> base ;

: doedit$       ( a1 | name -- )        \ edit a window string variable
                create , does>
                ' noop save!> >edattrib
                savecursor cursor-on
                cyan >bg black >fg ( fldcolor )
                @ >r                                    \ r=a1
                winlines whilite c@ 2* + @ 1+
                count                                   \ s=a1, n1
                    tuck '±' scan 2dup 2>r nip -        \ s=offset
                wx c@ 4 + + wy c@ whilite c@ +          \ s=x, y
                2r> tuck '±' skip          nip -        \ s=x, y, length
                r> swap lineeditor drop
                restcursor
                restore> >edattrib ;

: dofunc$      ( a1 a2 | name -- )      \ a1=string, a2=function
                create swap , , does> length swap perform ;

cyan value grayfg
blue value graybg

: graywind      ( -- )                  \ gray prev window
                ?vmode 7 = ?exit
                savecursor
                wfg c@ wbg c@
                grayfg wfg c!
                graybg wbg c!
                -1 !> prevhi
                windshow
                wbg c! wfg c!
                restcursor ;


: ?in_menu?     ( x y -- x y | 0 0 )    \ in menu area
                2dup
                worgy c@ 1+ wrows c@ over + 1- between >r
                worgx c@ 1+ wcols c@ over + 2- between r> and
                if      dup worgy c@ 1+ -          \ row in window
                        dup whilite c!
                        winlines swap 2* + @ c@ 0= \ if invalid line
                        if      1 whilite+         \ go to next line
                        else    hide.ms            \ hide mouse
                                windshow           \ display window
                                show.ms            \ show mouse
                                13 ( enter ) =: mousechar
                        then                       \ else press enter
                        2drop 0 0
                then    ;

: ?in_close?    ( x y -- x y | 0 0 )    \ in close button
                2dup worgy c@ = swap worgx c@ 1+ dup 2+ between and
\                2dup worgx c@ 1+ worgy c@ d=    \ in close?
                if      27 ( ESC )   =: mousechar
                        2drop 0 0
                then    ;

: ?in_home?     ( x y -- x y | 0 0 )    \ in home button
                worgy c@ over = >r
                over worgx c@ 1+ wcols c@ + dup 6 - swap 1- between r> and
                if      towhome windshow
                        2drop 0 0
                then    ;

: ?in_end?      ( x y -- x y | 0 0 )    \ in end button
                worgy c@ 1+ wrows c@ + over = >r
                over worgx c@ 1+ wcols c@ + dup 5 - swap 1- between r> and
                if      towend windshow
                        2drop 0 0
                then    ;

: ?in_up?       ( x y -- x y | 0 0 )    \ in up button
                dup worgy c@ dup wrows c@ 2/ + between >r
                over worgx c@ 1+ wcols c@ + = r> and
                if      -1 whilite+ windshow
                        2drop 0 0
                then    ;


: ?in_down?     ( x y -- x y | 0 0 )    \ in down button
                dup worgy c@ 1+ wrows c@ + dup wrows c@ 2/ - swap between >r
                over worgx c@ 1+ wcols c@ + = r> and
                if      1 whilite+ windshow
                        2drop 0 0
                then    ;

: wbutton       ( --- )         \ mouse driver for the menu system
                mousebutton                     \ get the mouse buttons
                case
                2 of    27 ( ESC )   =: mousechar               endof
                1 of    mousexy
                        ?in_menu?
                        ?in_close?
                        ?in_home?
                        ?in_end?
                        ?in_up?
                        ?in_down?
                        or if beep then                         endof
                        drop                    \ cleanup case stack
                endcase ;

: comment->PRN  ( -- )          \ allow entry and printing of a comment
                savescr savecursor
                $0D pemit $0A pemit
                $0D pemit $0A pemit
                0 21 79 23 box&fill at?
                begin   2dup at
                        pad off
                        off> stripping_bl's
                        1 22 pad 78 lineeditor dup
                        if      $0D pemit $0A pemit
                                ?cs: pad count prntypel
                        then    0=
                until   2drop
                $0D pemit $0A pemit 12 pemit
                restcursor restscr ;

: dowindow      ( a1 | name -- )        \ the genaric window driver
                create curwin , does> @ !> curwin
                ['] wbutton save!> dobutton             \ set mouse driver
                savecursor                              \ save screen cursor
                0 wexit c!                              \ mark as no auto exit
                winshow                                 \ show window border
                -1 !> prevhi
                begin   windshow                        \ and window contents
                        wexit c@ 0=                     \ if not exiting,
                        if      key dup ?upc            \ get a key
                                wexitkey c@ =           \ is key exit key?
                                if      1 wexit c!              \ mark exit
                                        wexitfunc perform       \ do exit func
                                then
                        else    0
                        then    wexit c@ 0=             \ if not exiting
                while   case                            \ then process key
        ( Shift F10 )   221 of  12 pemit        endof   \ formfeed printer
        ( Shift F9 )    220 of  comment->PRN    endof   \ comment to printer
        ( home )        199 of  towhome                 endof
        ( up   )        200 of  -1 whilite+             endof
        ( down )        208 of   1 whilite+             endof
        ( end  )        207 of  towend                  endof
                        $0D of  dohilite -1 !> prevhi   endof
                         bl of   1 whilite+             endof
                         27 of  wescfunc perform        endof
        ( F1 )          187 of  f1key    perform        endof
                                dowinkey                \ a window line key
                        endcase
                repeat  drop                            \ repeat till exit
                winhide                                 \ hide the window
                restcursor                              \ restore the cursor
                restore> dobutton ;                     \ & prev mouse driver

only forth also definitions     \ restore vocabulary search sequence

loadline @ 5 +
cr .( Type ") . .( LOAD" to load the SELECT MENU demo program )

\S      ****************  Stop Here  ******************

\ ***** Now define the windows for our demonstation *****

0 value w3cnt

: win3beep      ( -- )
                w3cnt 0=
                if      beep
                        5000 !> w3cnt
                then    decr> w3cnt ;

        40 03 window mywin3     \ define a new window of size 40x03
        mywin3                  \ select the window just defined
        28 05 winorg!           \ x y origin of current window
        ltgray winfg!           \ forground  color of current window
        blue   winbg!           \ background color of current window
        ' win3beep wbgstuff !   \ setup a background task for this window
        wintitle" \6 BEEPs once in a while"
        'H' winline" Hello There!"         beep
        'T' winline" This is a test"       noop
        'D' winline" do no window"         noop
        winbottom" \6 ESC=Exit window "

mywin3 dowindow %dowin3

: dowin3        ( -- )
                true save!> wbgstuff?
                %dowin3
                restore> wbgstuff? ;

        40 03 window mywin2     \ define a new window of size 40x03
        mywin2                  \ select the window just defined
        24 04 winorg!           \ x y origin of current window
        ltgray winfg!           \ forground  color of current window
        blue   winbg!           \ background color of current window
         wintitle" \6 My Window 2 "
        'H' winline" Hello There! 2"       beep
        'T' winline" This is a test 2"     noop
        'D' winline" dowindow3"            dowin3
        winbottom" \6 ESC=Exit window "

mywin2 dowindow dowin2

variable mahalovar                      \ define the variable
         mahalovar doeditvar mahalo     \ define a word to edit the variable

create mahalostring ," a test" 32 allot
       mahalostring doedit$ mahalo$

       mahalostring ' drop dofunc$ mahalo$2

        40 06 window mywin      \ define a new window of size 40x03
        mywin                   \ select the window just defined
        20 03 winorg!           \ x y origin of current window
        ltgray winfg!           \ forground  color of current window
        blue   winbg!           \ background color of current window
         wintitle" \6 My Window "
         0  winline"  "                               noop
        'H' winline"  Hello There!1  ±#±±±±±"          mahalo
        'D' winline"  do window 2"                     dowin2
        'T' winline"  This is        ±±±±±±±±±±±±±±±±" mahalo$
        'T' winline"  test2  ±±±±±±±±±±±±±±"           mahalo$2
         0  winline"  "                               noop
        winbottom" \6 ESC=Exit window "

mywin dowindow SDEMO            \ <<-- This is the demo entry word.

cr .( Type SDEMO to see a demonstration of the menu selection utility!)


