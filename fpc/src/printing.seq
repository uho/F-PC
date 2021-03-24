\ PRINTING.SEQ  Export & Import for SED.           by 1987 Tom Zimmer

editor definitions

\ The following few lines allows you to remove the printer driver code
\ and still load this printing facility onto SED.  Afterall you may not
\ need special printer attributes like BOLD and UNDERLINE.

defined ptype nip 0=    \ if PTYPE not already defined, define it.
#if                     \ Along with some DUMMY words.
                : ptype         ( a1 n1 --- )
                                prnhndl hwrite #out +! ;
                : printer-init  ;
                : printer-reset ;
                : lineendoff ;
                variable compressvar
#then

: pcr           ( --- ) 13 pemit 10 pemit #out off ;

defer pbutton   ' noop is pbutton

0 value dolst
0 value file-date-val
0 value file-time-val

: pdate/time    ( --- )
                getdate form-date count ptype bl pemit
                gettime form-time count 6 - ptype ;
                                        \ get rid of seconds and hundredths

variable controlines    \ number of control line encountered.

: skipto        ( a1 --- a2 )   \ skips all but one leading bl
                1+
                begin   dup c@ bl =
                while   1+
                repeat  1- ;

: ?escprint     ( --- f1 )
                linebuf 1+ dup c@ '.' =
                swap 1+ c@ '#' = and dup
                if      linebuf 3 +     controlines incr
                        begin   skipto dup 1+ c@
                                '0' '9' between
                        while   0.0 rot convert nip swap
                                255 min pemit
                        repeat  drop
                then    ;

: .offline      ( --- )
                ."  *** Printer OFF LINE, or NOT connected. *** " ;

: pspaces       ( n1 -- )
                0max    80 /mod 0
                ?do     spcs   80 ptype
                loop    spcs swap ptype ;

: .noprinter    ( --- )
                dolst 0=
                if      17 6 63 8 box&fill .offline beep
                        cursor-off 2 seconds cursor-on
                        showcur emptykbd scrshow
                else    cr .offline cr
                then    ;

: printline     ( --- )
                ?escprint ?exit
                lmargin @ pspaces
                stripbl's
                ?browse         \ if we are in browse mode then
                if              \ Supress hypertext destinations printout
                        linebuf 1+ c@ hyperdest =
                        if      linebuf count 2dup bl scan nip - blank
                        then
                then
                linebuf count ptype
                pcr getline lineendoff ;

variable pagenumber     1 pagenumber !
variable firstpage      1 firstpage  !
variable lastpage      99 lastpage   !
variable copies         1 copies !      \ 05/28/90 21:27:21.00 TJZ
variable pgtoprint      1 pgtoprint !   \ 05/28/90 21:27:20.34 TJZ
variable lsttoprint    99 lsttoprint !  \ 05/28/90 21:27:19.74 TJZ
variable pitem                          \ 05/28/90 21:24:55.56 TJZ
variable pnumval                        \ 05/28/90 21:24:56.77 TJZ
variable ?head/foot    -1 ?head/foot !  \ 05/28/91 17:23:27.26 tjz
variable ?paginate     -1 ?paginate  !  \ 01/08/92 15:49:29.92 tjz

0 value ?listing

: .underline    ( --- )                 \ underline current line.
                13 pemit #out off
                lmargin @ pspaces       \ tab over to left margin
                80 lmargin @ - 0MAX 0
                ?do     '_' pemit
                loop    pcr pcr ;

comment:  GET DATE & TIME OF CURRENTLY-OPEN FILE, & CONVERT TO DOS FORMATS

        The file printing routine in F-PC puts into the footer the date on
        which the file was printed, which is fine as far as it goes. But in
        many cases you'd really like to know the revision date of the file
        itself. That is contained in the disk directory, and used to be
        shown by programmers on the top line of each block. But that
        practice in not now used, and you have no way to tell the "version"
        (last revision date) of a program printout.

        The following words get from DOS the date and time of the currently
        open file, in the special DOS file-date format, then converts them
        to the standard DOS date and time formats, for printing in .FOOTER.

        References: R. Jourdain, "Programmer's Problem Solver for the IBM
        PC", Brady, 1986. Sec 5.2.5 Get/set the time and date of a file (pg
        262). (Typo: in one place, says erroneously to put 01 into AL to
        get date. It is in fact 00 to get date.

        R. Duncan (ed), "The MS-DOS Encyclopedia", Microsoft Press, 1988.
        Sec. 5 "System 2Calls", Interrupt 21H, Function 57H, Get/Set
        Date/Time of File (pg 1388).

comment;

\ None of existing DOS calls pass the needed registers, so a new one is needed.

code get_file_date&time       ( handle# -- file-time file-date )
               pop bx                   \ handle# -> bx
               mov ah, # $057           \ Function 57 -> ah
               mov al, # 0              \ 0 -> al for "get"
               int $21                  \ gets the time & date
               push cx                  \ the time to the stack
               push dx                  \ the date over it
               next     end-code

: convert_file_date     ( file-date -- Y MD )  \ File-date format to DOS fmt.
               0 $0200  um/mod          \ high 7 bits are ( year - 80 )
               $050 +                   \ add the decimal 80
               swap                     \ get the remainder
               0 $020   um/mod          \ low 5 bits are day, next 4 are month
               $0100 * +  ;             \ form bcd number MD

: convert_file_time     ( file-time -- HM 00 )  \ File-time format to DOS fmt.
               0 $0800  um/mod          \ high 5 bits are hours
               $0100 *                  \ To upper nibble of DOS bcd format
               swap                     \ get the remainder
               $020  /                  \ low 5 bits are seconds (discarded),
                                        \ next 6 are minutes
               +              \ Add upper & lower nibbles to make bcd number
               00       ;     \ Not using seconds, so put in zero

: setfile_date&time     ( --- )
                ed1hndl hopen 0=
        if      ed1hndl   \ gets beginning of handle stack = currently open
                >hndle @    \ move to handle number & fetch it
                get_file_date&time =: file-date-val =: file-time-val
                ed1hndl hclose drop
        else    off> file-date-val off> file-time-val
        then    ;

: .file_date    ( --- )
                file-date-val
                convert_file_date  form-date count ptype bl pemit
                file-time-val
                convert_file_time  form-time count 6 - 0MAX ptype ;

: .footer       ( --- )
                ?paginate @ 0= ?exit
                pagenumber @ 0= ?exit
                ?head/foot @ 0= ?exit
                .underline
                lmargin @ pspaces       \ Move over to left margin
                " Page " ptype
                pagenumber @ 0 <# #s #> ptype "  of " ptype
                pagenumber incr
                lastpage @ 0 <# #s #> ptype
                2 pspaces
                " Printed " ptype  pdate/time
                ed1hndl c@                      \ Get length of file name
                22 lmargin @ - 0MAX >
                if pcr then                     \ CR if too long for line
                60 ed1hndl c@ - #out @ - 80 min pspaces
                ed1hndl count ptype
                "  of " ptype  .file_date ;     \ Print file date

: setpage       firstpage @ pagenumber ! ;

: linesleft     ( --- )         \ lines left to print on page
                curline controlines @ 1- 0MAX - 0MAX
                prtlines mod ;

: .header       ( --- ) \ print first line of the current file
                pcr                     \ at least one carraige return
                ?paginate @ 0= ?exit
                pcr
                ?head/foot @
                if      0 #lineseg 1 over 0 c@l >r ?cs: pad r@ cmovel
                        lmargin @ pspaces
                        pad r> ptype
                        .underline
                else    pcr pcr
                then    ;

: ?newpage      ( --- )
                ?paginate @ 0= ?exit
                linesleft 0=
                if      .footer newpage .header
                then    ;

: todocpage     ( --- )
                pgtoprint @ 1- 0MAX 199 min prtlines *
                to.line first.textline =: screenline
                dolst 0=
                if      scrshow
                then    ;


: ?lastppg      ( --- f1 )
                lsttoprint @ 199 min prtlines * 2-
                curline < ;

: setlastpg     ( --- )
                lastline prtlines /mod swap
                if      1+ then 1 max dup lsttoprint ! lastpage ! ;

: doprint       ( --- )
                ?printer.ready ?listing or 0=
                if  .noprinter exit then
                0 save!> ?listing
                printer-init
                setfile_date&time
                copies @ 0
        ?do     <shom>  dolst 0=
                if      scrshow
                then    .header
                todocpage setpage controlines off
                begin   curline 7 and 0= if showstat then
                        curline 0=
                        ?head/foot @ and
                        ?paginate  @ and
                        if      pcr
                        else    printline
                        then
                        ?lastline 0=
                        key?      0= and
                        ?lastppg  0= and
                while   dolst 0=
                        if      dnln
                        else    <sdln>
                        then    ?newpage
                repeat  prtlines linesleft - 1- 0MAX
                0 ?do      pcr loop
                .footer newpage    key? ?leave
        loop    printer-reset
                <shom>  dolst 0=
                if      scrshow emptykbd
                then
                restore> ?listing ;

defer escattrib ' >rev is escattrib

: torev         ['] >rev     is escattrib ;
: toblnk        ['] >revblnk is escattrib ;

create prtmenu
0       28 ,  10 ,  ," First Page to print"      pgtoprint , 1+
        28 ,  11 ,  ," Last Page to print"      lsttoprint , 1+
        28 ,  12 ,  ," Left margin indent"         lmargin , 1+
        28 ,  13 ,  ," Copies to print"             copies , 1+
        65 ,  10 ,  ," Start numbering pages at" firstpage , 1+
\ end of numeric items, continue with choice items
        65 ,  11 ,  ," Compressed printing"    compressvar , 1+
        65 ,  12 ,  ," Print header/footer"     ?head/foot , 1+
        65 ,  13 ,  ," Paginate printing"        ?paginate , 1+
value pitems

: showpdata     ( --- )
                >rev    prtmenu 5 0      \ first 5 items are numbers
                do      dup 2@ swap at
                        4 + count + dup @ @ space 4 .l 2+
                loop    dup 2@ swap at
                        4 + count + dup @ @
                        if      ."  ON  "
                        else    ."  OFF "
                        then    2+
                        dup 2@ swap at
                        4 + count + dup @ @
                        if      ."  YES "
                        else    ."  NO  "
                        then    2+
                        dup 2@ swap at
                        4 + count + dup @ @
                        if      ."  YES "
                        else    ."  NO  "
                        then    2+
                drop >norm ;

: showcmds      ( --- )
                11 16 at  escattrib
                ."  ESC \3 = cancel "    escattrib
                ."  P \3 = Print " escattrib
                ."  S \3 = Set print device or file "
                 9 18 at ." \1Currently printing to \0 "
                >attrib3 prnhndl count type
                >norm 72 #out @ - spaces ;

: showpform     ( --- )
                 6 4 73 19 box&fill
                27 5 at ." \r Printing Setup Menu "
                17 7 at ." \3Use Enter or Arrows to move between fields"
                24 8 at ." \3Use + or - to change values"
                prtmenu pitems 0
                do      dup 2@ 2 pick 4 + c@ - 1- swap at
                        4 + count 2dup type + 2+
                loop    drop 64 9 at ." 0=no #'s" showcmds ;

: >pitem        ( --- a1 )
                prtmenu pitem @ 0
                ?do     4 + count + 2+
                loop    ;

: sc            ( --- )
                torev showcmds ;

: showpcur      ( --- )
                >pitem 2@ 6 + swap at ;

: ptohome       pitem off pnumval off torev showpcur ;

: penter        ( c1 --- c1 ) dup 13 =  \ Enter key
                over 208 = or            \ down arrow
                if      pitem @ 1+ pitems mod pitem ! sc
                        showpcur pnumval off drop 0
                then    ;

: pincr         ( c1 --- c1 ) dup 43 =  \ plus "+" sign
                if      >pitem  4 + count + @
                         pitem  @ 4 >   \ greater than item 4 (fifth item)
                        if      dup @ 0= swap !
                        else    incr
                        then    showpdata sc showpcur drop 0
                then    ;

: pdecr         ( c1 --- c1 )
                dup 45 =        \ minus "-" sign
                if      >pitem  4 + count + @
                        dup @ 1- 0MAX swap !
                        showpdata sc showpcur drop 0
                then    ;

: prright       ( c1 --- c1 )
                dup  203 =      \ left arrow
                over 205 = or   \ right arrow
                if      pitem @ 4 + pitems mod pitem ! sc
                        showpcur pnumval off drop 0
                then    ;

: prup          ( c1 --- c1 )
                dup 200 =       \ up arrow
                if      pitem @ pitems 1- + pitems mod pitem ! sc
                        showpcur pnumval off drop 0
                then    ;

: pbkspace      ( c1 --- c1 )
                dup 8 =         \ back space
                if      pnumval off
                        >pitem  4 + count + @ off
                        showpdata sc showpcur drop 0
                then    ;

: pnum          ( c1 --- c1 )   \ number between 0 and 9
                dup '0' >= over '9' <= and
                if      dup '0' - pnumval @ 10 * + 199 min
                        dup pnumval ! >pitem 4 + count + @ !
                        showpdata sc showpcur drop 0
                then    ;

0 value pfileing

: pset          ( c1 --- c1 )
                dup bl or 's' =             \ s = set print file
                if      prnhndl pad over c@ 1+ cmove
                        on> autoclear
                        >attrib1
                        32 18 pad 40 lineeditor       ( --- f1 )
                        >norm
                        cursor-off
                        pad c@ 0<> and
                        if      on> pfileing
                                pad $pfile
                                if      32 18 at >rev
                                     ."  Could not to create requested file  "
                                        beep 1 seconds off> pfileing
                                then
                        else    pclose off> pfileing
                        then    showcmds drop 0
                        showpcur cursor-on
                then    ;

: pmenu         ( --- )         \ print menu
                ['] pbutton save!> dobutton
                savescr
                setlastpg
                showpform  showpdata ptohome
                begin   key dup 27 <>     over
( Alt-P )                      153 <> and over bl or
                               'p' <> and
                while   penter  pincr  pdecr  pnum  pbkspace
                        prright prup   pset
                        if toblnk showcmds torev beep showpcur
                        then
                repeat  restscr
                showscreen bl or 'p' =
                if      doprint
                then    pfileing        \ if we were printing to a file
                if      pclose          \ then close the print file
                then
                restore> dobutton ;

' pmenu is pmenux

: elisting       ( --- )
                [ editor ]
                setlastpg
                cr ." Printing..."
                savecursor
                on> dolst doprint off> dolst
                off> edready
                restcursor ;

forth definitions


