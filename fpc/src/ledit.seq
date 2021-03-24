\ LEDIT.SEQ     Line Editor Utility                     by Tom Zimmer

comment:

  Here is a relatively simple editor for editing one line strings.

  Support is provided for strings up to 126 characters in length, with
full word and character operations using keypad or WordStar keys as follows:

        Ctrl-A                  Left word
        Ctrl-S                  Left character
        Ctrl-D                  Right character
        Ctrl-F                  Right word
        Ctrl-G                  Forward delete
        Ctrl-T                  Word delete
        Ctrl-Y                  Line delete or clear
        Left arrow              Left character
        Ctrl-Left arrow         Left word
        Right arrow             Right character
        Ctrl-Right arrow        Right word
        Home                    Beginning of line
        End                     End of line
        ESC                     Discard changes and leave
        Return/Enter            Save changes and leave

  The parameters needed by LINEEDIT are as follows:

        lineeditor    ( x y a1 n1 --- )

                x = char pos on row,    zero = left edge
                y = row number,         zero = top line
                a1 = counted string
                n1 = edit limit length, maximum value = 80

  Here is an example of a command that would edit a line of text in
SAMPLEBUFFER, with a maximum length of 12 characters, at location
row 10 column 5 on the screen.

                5 10 samplebuffer 12 lineedit

  Two auto resetting flags can be used to control the behavior of the
line editor in special ways.

        The STRIPING_BL'S boolean "VALUE" determines whether the line
        editor will strip trailing blanks from an edited string at
        the completion of the edit. this VALUE defaults to TRUE, do
        strip trailing blanks.

                OFF> STRIPPING_BL'S     will prevent line edit from
                                        stripping spaces.

        The AUTOCLEAR boolean "VALUE" determines whether the line
        edit buffer will be automatically cleared if the first
        character you enter on starting an edit is a normal text
        char. This is used to ease the users life in the situation
        where you want to give them the option of re-using a string
        or easily entering a new one without having to delete the old
        string first. This VALUE defaults to FALSE, no autoclear.

                ON> AUTOCLEAR           will cause line edit to
                                        automatically clear the edit
                                        string if a letter if the
                                        first thing entered.

comment;

only forth also hidden definitions also

defer >edattrib ' >attrib3 is >edattrib \ trailing edit attribute is 3

true     value stripping_bl's           \ are we stripping trailing blanks?
false    value autoclear                \ automatically clear line if first
                                        \ type entered is a letter;
      variable insertmode               \ insert/overwrite mode flag

headerless

      variable saveflg                  \ are we saving the results

    0    value ?ldone                   \ is line edit done?
    0    value lchar                    \ recent line edit character
    0    value ex                       \ where we are editing X
    0    value ey                       \ where we are editing Y
    0    value ecursor                  \ edit cursor position
    0    value lenlimit                 \ line edit length limit

defer ledbutton ' noop     is ledbutton

132 constant maxedit
    variable editbuf maxedit 2+ allot           \ our edit buffer,
             editbuf off                        \ 132 characters max

: .ecursor      ( --- )                         \ show the cursor
                ex ecursor + COLS 1- min ey at ;

: .eline        ( --- )                         \ redisplay edit line
                ex ey at
                editbuf count type
                save> attrib >edattrib
                lenlimit editbuf c@ - 0MAX
                COLS 1- #out @ - 0MAX min spaces
                restore> attrib ;

: <doldel>     ( --- )                         \ Line delete
                0 editbuf c!
                off> ecursor ;

: <ichar>      ( c1 --- )
                autoclear               \ should we clear the line on the
                if      <doldel>        \ first character typed?
                        off> autoclear
                then
                insertmode @
                if      editbuf 1+ ecursor + dup 1+
                        maxedit ecursor - cmove>
                        editbuf c@ 1+ lenlimit min editbuf c!
                then
                editbuf 1+ ecursor + c!
                ecursor 1+ lenlimit min COLS 1- min =: ecursor
                ecursor editbuf c@ max editbuf c! ;

: ?char        ( --- )                 \ handle normal keys, insert them
                lchar   bl '~' between
                if      lchar <ichar>
                then    ;

: dohome        ( --- )                         \ beginning of line
                off> ecursor ;

: doend         ( --- )                         \ End of line
                editbuf c@ =: ecursor ;

: doright      ( --- )                         \ right a character
                ecursor 1+ editbuf c@ min COLS 1- min =: ecursor ;

: doleft       ( --- )                         \ left a character
                ecursor 1- 0MAX =: ecursor ;

: <edone>       ( --- )      \ flag edit is finished, save changes
                on> ?ldone
                saveflg on ;

: <equit>       ( false --- true )      \ flag edit is finished, discard chngs
                on> ?ldone
                saveflg off ;

defer doret     ' <edone> is doret
defer dotab     ' <edone> is dotab
defer edone     ' <edone> is edone
defer equit     ' <equit> is equit
defer dolf      ' noop is dolf
defer dopgup    ' noop is dopgup
defer dopgdn    ' noop is dopgdn
defer doup      ' noop is doup
defer dodown    ' noop is dodown
defer doldel    ' <doldel> is doldel

: dofdel       ( --- )                         \ Forward delete
                ecursor 1+ editbuf c@ max editbuf c!
                editbuf 1+ ecursor + dup 1+ swap maxedit ecursor - cmove
                -1 editbuf c+! ;

: >to=bl       ( --- )                         \ forward to a blank
                editbuf 1+ dup maxedit + swap ecursor +
                ?do     i c@ bl = ?leave
                        1 +!> ecursor
                loop    editbuf c@ ecursor min =: ecursor ;

: >to<>bl      ( --- )                         \ forward to a non blank
                editbuf 1+ dup maxedit + swap ecursor +
                ?do     i c@ bl <> ?leave
                        1 +!> ecursor
                loop    editbuf c@ ecursor min =: ecursor ;

: dorword      ( --- )                         \ Forward to next word
                >to=bl
                >to<>bl ;

: <to=bl+1     ( --- )                         \ back to char following BL
                ecursor 1- 0MAX =: ecursor
                editbuf 1+ dup ecursor + 1- editbuf 1+ max
                ?do     i c@ bl = ?leave
                        -1 +!> ecursor
            -1 +loop    ;

: <to<>bl      ( --- )                         \ Back to non blank
                ecursor 1- 0MAX =: ecursor
                editbuf 1+ dup ecursor + 1- editbuf 1+ max
                ?do     i c@ bl <> ?leave
                        -1 +!> ecursor
                loop    ;

: dolword      ( --- )                         \ back a word
                <to<>bl
                <to=bl+1 ;

: dobdel       ( --- )                         \ back delete
                ecursor editbuf c@ max editbuf c!
                ecursor         ( --- f1 )
                doleft
    ( --- f1 )  if      insertmode @            \ if we are in insertmode
                        if      dofdel          \ then delete the character
                        else    bl editbuf 1+ ecursor + c!
                                                \ else change char to blank
                        then
                else    beep
                then    ;

: dowdel       ( --- )                         \ word delete
                begin   ecursor editbuf c@ <
                        editbuf 1+ ecursor + c@ bl <> and
                while   dofdel
                repeat
                begin   ecursor editbuf c@ <
                        editbuf 1+ ecursor + c@ bl = and
                while   dofdel
                repeat  ;

: strip_bl's    ( --- )                         \ strip blanks from editbuf
                ecursor >r
                doend
                begin   doleft
                        editbuf 1+ ecursor + c@ bl =
                        ecursor 0<> and
                while   dofdel
                repeat  editbuf c@ r> min 0MAX =: ecursor
                editbuf @               \ get count and first char
                $2001 =                 \ count=1 & char=blank
                if      0 editbuf c!    \ then reset buffer to empty
                then    ;

: doins        ( --- )                         \ toggle insert mode
                insertmode @ 0= dup insertmode !
                if      big-cursor
                else    norm-cursor
                then    ;

: doany        ( --- )                         \ handle any character entry
                graphchar       ( -- <c1> f1 )
                if      <ichar>
                then    ;

: ?control     ( --- )                         \ handle control characters
                lchar   bl <
                if      off> autoclear          \ no auto clear now
                        lchar exec:
\                       0 null  1 a     2 b     3 c     4 d     5 e     6 f
                        noop    dolword noop    dopgdn  doright doup  dorword
\                       7 g     8 h     9 i     LF      11 k    12 l    Enter
                        dofdel  dobdel  dotab   dolf    noop    noop    doret
\                       14 n    15 o    16 p    17 q    18 r    19 s    20 t
                        noop    noop    noop    noop    dopgup  doleft  dowdel
\                       21 u    22 v    23 w    24 x    25 y    26 z    Esc
                        noop    doins   noop    dodown  doldel  noop    equit
\                       28 \    29 ]    30 ^    31 _
                        noop    noop    noop    noop
                then    ;

: funcarray     ( n1 | <name> --- )     \ create an X array initialized to
                                        \ NOOP functions
                >r create
                xhere paragraph + dup xdpseg ! xseg @ - , xdp off
                r> 0
                ?do     ['] noop x,
                loop    does> @ +XSEG swap 2* @L execute ;

128 funcarray keyfuncs1
128 funcarray keyfuncs2

' keyfuncs1 value keysfuncptr           \ default to table1

: >keys1        ( --- )
                ['] keyfuncs1 is keysfuncptr ;

: >keys2        ( --- )
                ['] keyfuncs2 is keysfuncptr ;

                                        \ for keys in the range 128 to 255
: lkey!         ( a1 n1 --- )           \ store key function CFA a1 into
                                        \ leuvalue n1 of the KEYFUNCS array
                128 - dup 0< over 127 > or
                abort" keys must be in the range 128 to 255"
                2* keysfuncptr >body @ +XSEG swap !L ;

: lkey@         ( n1 --- a1 )           \ fetch the function CFA a1 of
                                        \ keyvalue n1 from the KEYFUNCS array
                128 - dup 0< over 127 > or
                abort" keys must be in the range 128 to 255"
                2* keysfuncptr >body @ +XSEG swap @L ;

: ?func        ( --- )                         \ handle function keys
                lchar   127 >
                if      off> autoclear          \ no auto clear now
                        lchar 128 - 0MAX 127 min keysfuncptr execute
                then    ;

                                                \ c1 = keyboard character
                                                \ f1 = true for done editing
: dokey        ( c1 --- )                      \ process a key
                =: lchar
                ?char                           \ handle normal ascii
                ?func                           \ function characters
                ?control ;                      \ control chars

headers

                                                \ x = char pos on row
                                                \ y = line number
                                                \ a1 = counted string
                                                \ n1 = edit limit length
: <ledit>       ( x y a1 n1 --- )       \ Edit line currently in EDITBUF.
                ['] ledbutton save!> dobutton
                save> lenlimit
                get-cursor >r
                over c@ ecursor min =: ecursor
                maxedit min =: lenlimit         \ save max edit length
                dup >r                          \ save source address
                editbuf over c@ lenlimit min 1+ cmove
                editbuf c@ lenlimit min editbuf c!
                =: ey =: ex                     \ save origin
                doins   doins
                off> ?ldone
                begin   .eline
                        .ecursor key dokey
                        ?ldone
                until   saveflg @ dup           \ proper save exit
                if      stripping_bl's          \ do we want to strip blanks?
                        if      strip_bl's
                        then    on> stripping_bl's      \ force it next time
                        editbuf r@ over c@ lenlimit min 1+ cmove
                then    r>drop
                r> set-cursor           ( --- f1 )
                restore> lenlimit
                restore> dobutton
                off> autoclear ;                \ no automatic line clear

forth definitions
                                        \ x = char pos on row
                                        \ y = line number
                                        \ a1 = counted string
                                        \ n1 = edit limit length
                                        \ f1 = true for saved changes
                                        \ f1 = false for canceled with ESC
: lineeditor    ( x y a1 n1 --- f1 )    \ Edit line in a1
                ['] <equit> save!> equit
                off> ecursor
                insertmode off
                ['] noop save!> doLF
                save> keysfuncptr
                >keys2
                <ledit>
                restore> keysfuncptr
                restore> doLF
                restore> equit ;

\ ***************************************************************************
\ Initialize the key arrays for the proper function keys

>keys1

' doany   158 lkey!     \ Alt-A
' dohome  199 lkey!     \ Home
' doup    200 lkey!     \ Up arrow
' doPgUp  201 lkey!     \ PgDn
' doleft  203 lkey!     \ Left arrow
' doright 205 lkey!     \ Right arrow
' doend   207 lkey!     \ End
' dodown  208 lkey!     \ Down arrow
' doPgDn  209 lkey!     \ PgDn
' doins   210 lkey!     \ Ins
' dofdel  211 lkey!     \ Del
' dolword 243 lkey!     \ Ctrl Left arrow
' dorword 244 lkey!     \ Ctrl Right arrow

>keys2

' doany   158 lkey!     \ Alt-A
' dohome  199 lkey!     \ Home
' doup    200 lkey!     \ Up arrow
' doPgUp  201 lkey!     \ PgDn
' doleft  203 lkey!     \ Left arrow
' doright 205 lkey!     \ Right arrow
' doend   207 lkey!     \ End
' dodown  208 lkey!     \ Down arrow
' doPgDn  209 lkey!     \ PgDn
' doins   210 lkey!     \ Ins
' dofdel  211 lkey!     \ Del
' dolword 243 lkey!     \ Ctrl Left arrow
' dorword 244 lkey!     \ Ctrl Right arrow

only forth also definitions

\s

only forth also definitions hidden also

variable samplebuffer 128 allot

: sample        ( --- )
                " Zimmer, Harold" ">$
                samplebuffer over c@ 1+ cmove
                on> autoclear
                27 04 samplebuffer 24 lineeditor drop ;

