\ SEDCOPY.SEQ           Editor Cut Copy & Paste         by Tom Zimmer

: insertaline   ( a1 --- )
                ?showfull ?exit
                linebuf over c@ 1+ 2- cmove
                linebuf c@ dup =: linelen =: screenchar
                ch/l linebuf c!
                modified
                nodisp-nln ;

create cut/copy_file ," TEMP.SEQ"

: imp/exp.init  ( --- )
                cut/copy_file ed2hndl $>handle ;

: getinpfile    ( --- f1 )
                imp/exp.init
                ?shiftkey dup
                if      drop
                        ['] femit  is emit
                        ['] noop save!> doLF
                        getfile 0=  showstat            \ get a filename
                        restore> doLF
                        ['] (emit) is emit
                        dup 0=
                        if      swap ed2hndl $>handle
                        then
                then    ;

0 value export$

64 array expbuf                         \ export buffer
cut/copy_file count expbuf place        \ init export filename

: ?getexpfile   ( --- f1 )      \ get a filename, and test to see if it
                                \ already exists before overwriting it.
                imp/exp.init
                ?shiftkey dup
                if      drop
                        8 6 73 12 box&fill
                        space >rev export$ count type >norm
                        ."    Enter=accept  ESC=cancel"
                        on> autoclear
                        >attrib1
                        10 9 expbuf 59 lineeditor       ( --- f1 )
                        >norm
                        expbuf c@ 0<> and
                        if      expbuf ed2hndl $>handle \ set filename
                                ed2hndl pathset drop    \ init path
                                already_exists?         \ prompt for replace
                                                        \ if already exists.
                                10 11 at
                        else    true scrshow
                        then
                else    10 6 70 8 box&fill space
                then    ;

: export        ( --- )
                putline
                marking 0=
                if      .nomark exit
                then    "  Copy marked text to filename: " ">$ =: export$
                ?getexpfile ?exit
                ." Copying text to " ed2hndl count type ." ..."
                ed2hndl hcreate 0=
                if      0.0 ed2hndl movepointer
                        off> wblen
                        markend 1+ markstrt
                        ?do     i linewrite ?leave
                        loop    flushwrite      ( --- f1 )
                        if      true " \4 Failed, Disk FULL " ?softerror
                        else    ." ..Done " ?doingmac 0=
                                if      7 tenths then
                        then    ed2hndl hclose
                        " \4 Error Closing File " ?softerror
                        mark-clear
                else    true " \4 Failed, Couldn't CREATE file " ?softerror
                then    getline scrshow ;

' export is exportx     \ patch into smaller editor

: excut         ( --- )         \ Cut out marked text
                ?browse ?exit
                putline
                marking 0=
                if      .nomark exit
                then    "  Cut marked text to filename: " ">$ =: export$
                ?getexpfile ?exit
                ." Cutting text to " ed2hndl count type ."  ..."
                ed2hndl hcreate 0=
                if      0.0 ed2hndl movepointer
                        off> wblen
                        markend 1+ markstrt
                        do      i linewrite ?leave
                        loop    flushwrite 0=
                        if      ed2hndl hclose drop
                                curline markstrt >
                                if      markstrt backto.line
                                else    markstrt to.line
                                then    markend markstrt - #deletelines
                                linedelete      \ in case we're on last line
                                ." ..Done " ?doingmac 0=
                                if      7 tenths then
                                clipdown
                        else    true " \4 Failed, Disk FULL " ?softerror
                        then    mark-clear
                else    true " \4 Failed, Couldn't CREATE file " ?softerror
                then    getline scrshow ;

' excut is excutx       \ patch into smaller editor

: import        ( --- )
                ?browse ?exit
                ?showfull ?exit
                getinpfile ?exit
                true save!> imode
                ed2hndl hopen 0=
                if      putline
                        getline
                        0.0 ed2hndl movepointer
                        0.0 filepointer 2!
                        off> loadline
                        ibreset
                        shoml nln suln
                        10 09 70 12 box&fill
                        ed2hndl save!> seqhandle
                        ."  Importing from " seqhandle count type bcr
                        ."  Inserting lines...     Press \r ESC \0 to Abort"
                        57 11 at .time
                        cursor-off
                        ?doingmac 0=
                        if      5 tenths then
                        begin   lineread  dup c@ 0<> ?full 0= and
                                key? if key 27 <> and then
                        while   loadline @ 7 and 0=
                                if      30 11 at >rev
                                        loadline @ 4 .r >norm
                                        loadline @ 31 and 0=
                                        if      57 11 at .time
                                        then
                                then    insertaline
                        repeat  drop    <ldel>
                        restore> seqhandle
                        ed2hndl hclose drop
                then    scrshow cursor-on ?cursor
                restore> imode emptykbd ?showfull drop ;

' import is importx     \ patch into smaller editor

