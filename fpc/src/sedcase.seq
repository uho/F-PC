\ SEDCASE       Case conversion, paste date/time, & tab expansion
\
\ History:
\
\  01/26/92 21:48 tjz
\         Made a change to "paste_datetime" to remove the seconds and
\         hundreds. Suggested by J. McKnight.
\

editor definitions

: paste_datetime ( --- )
                ?browse ?exit
                true save!> imode
                bl schr getdate form-date count bounds
                ?do     i c@ schr       loop
                bl schr gettime form-time count 6 - bounds
                                                \ remove seconds & hundreds
                ?do     i c@ schr       loop
                bl schr
                restore> imode ;

: tabxp         ( --- )         \ tab expansion word
                ?browse ?exit
                true save!> ?exp_tabs
                ?exp_type_set scrshow
                modified
                shom
                begin   on> lchng               \ mark current line changed
                        <sdln>                  \ save line, to next line
                        curline 63 and 0=       \ show status once in a while
                        if      showstat
                                scrshow
                        then
                        ?lastline               \ till we are at file end
                        ?showfull or            \ or memory is full
                until
                ?full
                if      showstat
                        scrshow
                        beep                    \ alert user of full memory
                        5 tenths
                else    on> lchng               \ not full, do last line also
                        putline
                        getline
                then
                shom
                restore> ?exp_tabs
                ?exp_type_set scrshow ;

: tab_to_space  ( -- )          \ show tabs as spaces
                ?exp_tabs 0= !> ?exp_tabs
                putline getline
                ?exp_type_set scrshow ;

: l>lcase       ( --- )         \ convert the current line to lower case
                ?browse ?exit
                linebuf 1+ linelen bounds
                ?do     i c@ 'A' 'Z' between
                        if      i c@ bl or i c!
                        then
                loop    modified
                putline getline sdisplay ;

: l>ucase       ( --- )         \ convert the current line to lower case
                ?browse ?exit
                linebuf 1+ linelen bounds
                ?do     i c@ 'a' 'z' between
                        if      i c@ 95 and i c!
                        then
                loop    modified
                putline getline sdisplay ;

: w>lcase       ( --- )         \ convert the current word to lower case
                ?browse ?exit
                linebuf 1+ screenchar + linelen screenchar - 0MAX bounds
                ?do     i c@ 'A' 'Z' between
                        if      i c@ bl or i c!
                        then    i c@ bl = ?leave        \ leave at word end
                loop    modified
                putline getline sdisplay ;

: w>ucase       ( --- )         \ convert the current word to lower case
                ?browse ?exit
                linebuf 1+ screenchar + linelen screenchar - 0MAX bounds
                ?do     i c@ 'a' 'z' between
                        if      i c@ 95 and i c!
                        then    i c@ bl = ?leave        \ leave at word end
                loop    modified
                putline getline sdisplay ;

: c-alpha?      ( --- f1 )
                linebuf 1+ screenchar + c@
                dup  'A' 'Z' between            \ either A to Z
                swap 'a' 'z' between or ;       \   or   a to z

\ cursor MUST be sitting on a letter or NOTHING happens.

: wcasetgl      ( --- )         \ word case conversion toggle
                ?browse ?exit
                c-alpha?
                if      linebuf 1+ screenchar + c@ 'A' 'Z' between
                        if      w>lcase
                        else    w>ucase
                        then
                then    modified
                putline getline sdisplay ;

: ccasetgl      ( --- )         \ word case conversion toggle
                ?browse ?exit
                c-alpha?
                if      linebuf 1+ screenchar +
                        dup c@ dup 'A' 'Z' between
                        if bl or else 95 and then swap c!
                then    modified
                putline getline sdisplay ;

: ALT-OPTION    ( --- )         \ Alt-O options
                savescr
                ['] noop save!> dobutton
                ?doingmac 0=    \ If we are doing a macro, don't display
                                \ command menu box.
                if      screenline 1+ dup 11 >
                        if      13 -
                        then    20 swap 60 over 12 + box&fill
                        ."  Other commands.. Select an operation" bcr bcr
                        ."    A - enter Any Character"       bcr
                        ."    X - Expand all TABS to spaces" bcr
                        ."    S - Show   all TABS as spaces "
                        ?exp_tabs if ." \3 Yes " else ." \3 No " then
                                                             bcr
                        ."    L - convert line to Lowercase" bcr
                        ."    U - convert line to Uppercase" bcr
                        ."    W - Word      case toggle" bcr
                        ."    C - Character case toggle" bcr
                        ."    P - Paste the Time and Date"   bcr
                        ." \s10\r ESC \0 = cancel"
                        showcur
                then
                key bl or >r
                restscr
                'a' r@ = if insany          then
                'x' r@ = if tabxp           then
                's' r@ = if tab_to_space    then
                'p' r@ = if paste_datetime  then
                'l' r@ = if l>lcase         then
                'u' r@ = if l>ucase         then
                'c' r@ = if ccasetgl        then
                'w' r> = if wcasetgl        then
                restore> dobutton
                sdisplay showstat cursor-on ;

' ALT-OPTION IS ALT-O

forth definitions


