\ SEDIT2.SEQ    The second part of the main body of the sequential editor

editor definitions

0 value ?showcmd

: ecmdtgl       ( -- )
        savescr savecursor cursor-off
        ?showcmd 0= dup =: ?showcmd
        if      2 8
.box"  The names of edit functions will be shown in the upper left corner. "
                2 seconds
        else    24 8 .box"  Turning off show names "
                1 seconds
        then    restcursor restscr ;

: notavail      ( --- )
             window.left statusline at cursor-off
             ." \2 You MUST Load an expanded function set for that operation."
             edeeol beep 2 seconds cursor-on ;

defer sedesc    ' sesc     is sedesc
defer jstfy     ' notavail is jstfy
defer shelp     ' notavail is shelp
defer exportx   ' notavail is exportx
defer excutx    ' notavail is excutx
defer importx   ' notavail is importx
defer pmenux    ' notavail is pmenux
defer kerr      ' beep is kerr
defer sedset    ' notavail is sedset
defer sortlin   ' notavail is sortlin
defer drawlin   ' notavail is drawlin
defer lftjust   ' notavail is lftjust
defer appendx   ' notavail is appendx
defer alt-g     ' notavail is alt-g
defer adjwind   ' notavail is adjwind
defer alt-o     ' notavail is alt-o
defer insany    ' ^cc      is insany
defer zoomwind  ' notavail is zoomwind
defer splitwind ' notavail is splitwind
defer Ctrl-J    ' beep is Ctrl-J

                \ control key functiontable
: s^tbl         ( n1 --- )
                exec:
\ @     A       B       C       D       E       F       G
kerr    lwrd    jstfy   pdn     rchr    upln    rwrd    fdel
\ H     I       J       K       L       M       N       O
bdel    stab    Ctrl-J  kerr    lmset   nln     spltln  kerr
\ P     Q       R       S       T       U       V       W
kerr    kerr    pup     lchr    wdel    updt    itgl    sclup
\ X     Y       Z       ESC
dnln    ldel    scldn   sedesc  kerr    kerr    kerr    kerr  ;


                \ function key table
: sfuntbl       ( n1 --- )
                exec:
\ CBS Control Backspace
fdel
\ A-9   A-0     A -     A =     CPGUP   133     134     135
kerr    kerr    kerr    kerr    kerr    kerr    kerr    kerr
\ 136   137     138     139     140     141     142     BACKSPACE
kerr    kerr    kerr    kerr    kerr    kerr    kerr    sbtab
\ A-Q   A-W     A-E     A-R     A-T     A-Y     A-U     A-I
kerr    wr->fl  kerr    NOOP    tabset  lundel  wudel   kerr
\ A-O   A-P     154     155     156     157     A-A     A-S
Alt-O   pmenux  kerr    kerr    kerr    kerr    appendx sedset
\ A-D   A-F     A-G     A-H     A-J     A-K     A-L     167
kerr    kerr    alt-g   kerr    joinln  kerr    lftjust kerr
\ 168   169     170     171     A-Z     A-X     A-C     A-V
kerr    kerr    kerr    kerr    send    excutx  exportx importx
\ A-B   A-N     A-M     179     180     181     182     183
kerr    kerr    NOOP    kerr    kerr    kerr    kerr    kerr
\ 184   185     186     F1      F2      F3      F4      F5
kerr    kerr    kerr    shelp   tscrn   smrk    bscrn   sgetl
\ F6    F7      F8      F9      F10     197     198     199
sloon   sortlin srepn   drawlin sesc    kerr    kerr    kerr
\ 200   201     202     203     204     205     206     END
kerr    kerr    kerr    kerr    kerr    kerr    kerr    sendl
\ 208   209     210     Del     SF1     SF2     SF3     SF4
kerr    kerr    kerr    fdel    kerr    kerr    kerr    kerr
\ SF5   SF6     SF7     SF8     SF9     SF10    CF1     CF2
kerr    sloob   sortlin repall  kerr    kerr    kerr    kerr
\ CF3   CF4     CF5     CF6     CF7     CF8     CF9     CF10
kerr    kerr    kerr    kerr    kerr    kerr    kerr    kerr
\ AF1   AF2     AF3     AF4     AF5     AF6     AF7     AF8
kerr    tmscrn  kerr    bmscrn  kerr    slooa   kerr    srepa
\ AF9   AF10    242     CLEFT   CRIGHT  CEND    CPGDN   CHOME
kerr    squt    kerr    lwrd    rwrd    send    kerr    shom
\ A-1   A-2     A-3     A-4     A-5     A-6     A-7     A-8
kerr    kerr    kerr    kerr    kerr    kerr    kerr    ecmdtgl ;

\ ***************************************************************************
\ The following two words allow changing the functions installed in
\ two previous tables

: ctlset        ( n1 | function --- )
                defined
                if      swap                    \ get address of functon
                        0MAX 31 min 2*          \ clip n1 to valid CTRL char
                        2+                      \ step past EXEC:
                        ['] s^tbl >body @       \ relative segment of S^TBL
                        +XSEG swap !L   \ convert to absolute segment & store
                else    nip cr count type
                        ."  <- Not defined, and not installed "
                then    ;

: fnset         ( n1 | function --- )   \ same comments as ABOVE
                defined
                if      swap
                        127 max 255 min 127 - 2*
                        2+                      \ skip two bytes for EXEC:
                        ['] sfuntbl >body @     \ relative segment of SFUNTBL
                        +XSEG swap !L
                else    nip cr count type
                        ."  <- Not defined, and not installed "
                then    ;

: ?.s^tbl       ( c1 -- c1 )    \ conditionally display each keys function
                ?showcmd
                if      savecursor
                        savescr
                        0 0 at
                        dup 2*
                        2+                      \ step past EXEC:
                        ['] s^tbl >body @       \ relative segment of S^TBL
                        +XSEG swap @L           \ fetch function from table
                        >name .id               \ display the function name
                        4 tenths
                        restscr
                        restcursor
                then    ;

: ?.sfuntbl     ( c1 -- c1 )
                ?showcmd
                if      savecursor
                        savescr
                        0 0 at
                        dup 2*
                        2+                      \ skip two bytes for EXEC:
                        ['] sfuntbl >body @     \ relative segment of SFUNTBL
                        +XSEG swap @L           \ fetch function from table
                        >name .id               \ display the function name
                        4 tenths
                        restscr
                        restcursor
                then    ;

: ?controls     ( c1 --- c1 )   \ handle control characters
                keychar 32 <
                if      keychar ?.s^tbl s^tbl
                        off> keychar
                then    ;

: ?functions    ( c1 --- c2 )   \ handle function characters
                keychar 126 >       \ they have values >126
                if      keychar 127 - ?.sfuntbl sfuntbl
                        off> keychar
                then    ;

: ?schr         ( c1 --- )      \ insert character if not a func
                keychar ?dup if schr then    ;

: doachar       ( c1 --- )
                =: keychar
                ?controls ?functions ?schr ;

: find.line     ( --- )         \ Assumes we are starting on first line.
                loadline @ 1- 0MAX maxlines min to.line ;

: ?exp_type_set ( -- )
                ?exp_tabs
                if      ['] exsltypel is sltypel
                else    ['] typel     is sltypel
                then    ;

: deferset      ( --- )         \ save current deferred words, and reset them
                @> keyfilter    is normfilter  ['] skeyfilter  is keyfilter
                @> key          is normkey     ['] statkey     is key
                @> bgstuff      is normbgstuff ['] ?showstatus is bgstuff
                @> dobutton     is normbutton   @> sbutton     is dobutton
                ?exp_type_set   ;

: deferreset    ( --- )         \ restore the deferred words old function.
                @> normbutton   is dobutton
                @> normbgstuff  is bgstuff
                @> normkey      is key
                @> normfilter   is keyfilter ;

' deferreset is reset_defered   \ reset the defered words even if there
                                \ is a serious error condition

: <reedit>      ( --- )         \ reenter edit of file
                get-cursor >r
                bkable off              \ we are not breakable
                ?diskfull drop
                time-reset savestate
                decimal
                edscroll                \ enable sub screen scrolling
                off> updated
                etabsize tabsize !
                rmset?
                if      rmmax 70 max
                else    ermargin
                then    rmargin !
                edready 0= abort" No file to re-edit."
                ?showfull drop
                find.line
                scrline first.textline   max
                curline first.textline + min
                last.textline min =: screenline
                showscreen
                on> ?border
                doborder
                off> ?eddone
                begin   on> vstaton
                        showcur
                        key doachar
                        ?eddone
                until   restorestate
                bkable on               \ we are breakable again
                r> set-cursor ;

: reedit        ( --- )
                deferset <reedit> deferreset ;

forth definitions

