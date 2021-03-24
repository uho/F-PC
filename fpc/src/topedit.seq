\ TOPEDIT.SEQ   Memory edit.                            By Tom Zimmer

comment:

  Memory edit, allows reentering the editor without having to re-read
the edit file from disk. This results in a much faster turn around time
for development.  Changes made during an edit will still be saved at
the end of each edit session.

comment;

editor definitions

handle memfile

: ?readfile     ( --- )
                newfl ?exit   \ don't try to read if its a new file
                edinit
                ed1hndl memfile over c@ 1+ caps-comp \ if file not the same
                edready 0= or                      \ or editor not ready
                if      read-write
                        ed1hndl hopen dup          \ try to open read-write
                                                   \ if it fails, then try
                        if      drop               \ read-only
                                read-only          \ open for reading
                                ed1hndl hopen      \ try to open the file
                                dup 0=             \ if it did, use browse
                                                   \ mode, as it's read-only
                                if      on> ?browse
                                then
                        then
                        abort" Can't open file!"   \ abort if can't
                        toggling 0=   \ make file switch as fast as possible!
                        if      savecursor         \ save cursor position
                                savescr
                                15 8 65 12 box&fill bcr
                                ."  \1 Reading " space .ed1hndl
                                read.oldfile       \ read the file
                                3 tenths
                                restscr
                                restcursor         \ restore cursor position
                        else    off> toggling
                                read.oldfile
                        then
                        ed1hndl memfile $>handle   \ copy to memfile
                        ed1hndl hclose drop        \ close it
                        sinit                      \ init mem structure
                        on> edready                \ say everything ready
                then    ;

: cold-edinit        ( --- )
                defers initstuff
                memfile clr-hcb
                off> edready ;

' cold-edinit is initstuff

forth definitions

: push/pop-level ( --- f1 )     \ push or pop a level on the edit nest stack
                leavesave
                if      leavesave 0>
                        hdepth maxh < or
                        if      leavesave 0<            \ push if -1
                                if      ed1>hstack
                                then
                                off> newfl              \ NOT a new file
                                hndlsave ed1hndl $>handle
                                listsave loadline !
                                off> screenchar
                                newbrowse =: ?browse
                        else    cursor-off
                                22 6 58 10 box&fill bcr
                                ."   Link/Edit NEST LIMIT reached!"
                                beep 1 seconds cursor-on
                        then    false           \ nest one
                        off> leavesave
                else    hdepth
                        if
\+ split-wind                   hdepth 1 =
\+ split-wind                   splitwind? and
\+ split-wind                   if      split-wind
\+ split-wind                   then
                                hstack>ed1      \ popup one
                                off> newfl              \ NOT a new file
                                on> backing-out \ we are poping 1 lvl
                                false
                        else    true            \ at stack bottom
                        then
                then
                ?browse                         \ select the proper type
                if      ['] hypertypeL is typeL
                else    ?dosio
                        if      ['] (typeL)        is typeL
                        else    (lit) defers typeL is typeL
                        then
                then    ;

: <red>         ( --- )
                savescr
                ?browse                         \ select the proper type
                if      ['] hypertypeL is typeL
                else    ?dosio
                        if      ['] (typeL)        is typeL
                        else    (lit) defers typeL is typeL
                        then
                then
                off> hdepth                     \ clear handle stack
                off> backing-out                \ not backing out of edit
                backingup =: renaming
                begin   mark-clear              \ 06/12/91 tjz per
                                                \ Mike Christopher
                        ?readfile
                        backing-out 0=          \ only set screen line of new
                                                \ entry, not on returning
                        if      newfl   0=      \ if its not a newfile
                                ?browse 0= and  \ and we aren't browsing
                                if      7
                                else    1
                                then    first.textline + =: scrline
                        then    off> backing-out
                        reedit
                        ed1hndl memfile $>handle
                        pop-extra
                        if      begin   hdepth 0>
                                while   hstack>ed1      \ popup one
                                repeat
                                off> pop-extra
                        then    push/pop-level
                until   off> ?browse
                ?dosio
                if      ['] (typeL)        is typeL
                else    (lit) defers typeL is typeL
                then
                cr ;

: <ed>          ( --- )         \ Redefined to work from memory.
                seding  0=
                if      ?fileopen
                then
                seqhandle hclose drop           \ close current file
                seqhandle ed1hndl $>handle      \ copy file to edit handle
                <red>
                seqhandle hopen drop            \ open current file
                ;

: file-line_view ( n1 a1 --- )
                $file 0=
                if      loadline !
                        <ed>
                else    drop
                        savecursor
                        savescr
                        cursor-off
                        15 8 65 12 box&fill
                        bcr ."   \4 Couldn't locate " >attrib4 .seqhandle
                        62 #out @ - spaces >norm
                        beep 15 tenths
                        cursor-on
                        restscr
                        restcursor
                then ;

: cfa_view      ( a1 --- )
                >viewfile
                file-line_view ;

: ?leave_set    ( -- )          \ set preference for leave or prompt on last
                                \ open file.
                ?leaveprompt 0= =: leavenow ;

headers

: view          ( | <word> --- )
                on> newbrowse
                on> ?browse
                off> seding
                off> newfl
                ?leave_set
                >in @ bl word swap >in ! c@
                if      bl word hfind 0= ?missing
                        cfa_view
                else    <ed>
                then    ;

' view   alias browse
' view   alias b
' view   alias v
' view   alias l
' view   alias LL

: ed            ( | word --- )
                off> newbrowse
                off> ?browse
                off> seding
                off> newfl
                ?leave_set
                >in @ bl word swap >in ! c@
                if      bl word hfind 0= ?missing
                        cfa_view
                else    <ed>
                then    ;

' ed is editfile
' ed alias e            \ an alias meaning Edit a word

: help          ( | <name> --- )
                on> newbrowse
                on> ?browse
                off> seding
                off> newfl
                ?leave_set
                >in @ bl word swap >in ! c@
                if      here count 29 min helpbuf place
                        $2020 helpbuf count + !
                        bl word hfind 0= ?missing
                        >viewfile                     \ -- offset a1
                        " HLP" ">$ over $>ext
                        $file 0=
                        if      drop
                                findword
                                if      <ed>
                                then
                        else    drop
                        then
                else    dofhelp
                then    ;


' help alias h

: edit          ( n1 --- )
                off> newbrowse
                off> ?browse
                off> seding
                off> newfl
                ?leave_set
                1 ?enough =: loadline
                <ed> ;

: list          ( n1 --- )
                on> newbrowse
                on> ?browse
                off> seding
                off> newfl
                ?leave_set
                1 ?enough =: loadline
                <ed> ;

: viewfrom      ( n1 --- )              \ browse starting after line n1
                1 ?enough               \ need a parameter
                =: read-from            \ skips (doesn't read) n1 lines
                1 list ;                \ of the current file

' viewfrom alias vf

: leaveprompton ( -- )          \ prompt for new file on leaving editor
                on> ?leaveprompt ;

: leavepromptoff ( -- )         \ DON'T prompt for a new file on leaving edit
                off> ?leaveprompt ;

: autosaveon    ( --- )         \ turn ON automatic save on idle
                on> autosaving? ;

: autosaveoff   ( --- )         \ turn OFF automatic save on idle
                off> autosaving? ;

: unedit        ( --- )         \ de-allocate the memory taken by SED
                tsegb 0= ?exit
                unpointer> baseseg      \ release the editors text buffer
                off> tsegb
                off> lseg
                off> dseg
                off> maxsegs
                off> #edsegs
                off> edready
                off> ldel.cnt
                memfile clr-hcb ;

' unedit is clearmem

defined elisting nip #if                \ only load if printing loaded

: listing       ( --- )
                decimal
                ?fileopen
                off> memfile
                seqhandle ed1hndl $>handle      \ copy file to edit handle
                off> newfl
                ?readfile
                off> renaming
                elisting
                off> memfile ;

' listing is dolisting

#endif

headerless      \ back to headerless for the rest of the editor

