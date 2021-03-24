\ SEDSHELL      Shell to DOS from SED                   by Tom Zimmer

: ".errmsg      ( a1 n1 --- )
                savecursor
                savescr
                cursor-off
                18  6 52 12 box&fill
                bcr 2 spaces type
                bcr
                bcr ." \s09Press any \r KEY \b" key drop
                restscr
                restcursor ;

create command.buf 64 allot
       command.buf off

: %doDOS        ( --- )                 \ perform DOS command in COMMAND.BUF
                putline                 \ save current line if needed
                >norm
                curline >r              \ where to come back to
                lastline toline+        \ goto the last file line

                tend dup 0 c@l 1+       \ seg len ---
                0 swap                  \ seg off len

\ The 100 + is in segments, or 1600 bytes
\ the minimum valid space between TOFF & TEND.

                toff 100 + 0 rot        \ seg off seg off len
                cmovel                  \ move last line down
                ?cs:
                toff 100 + dup 0 c@l 1+ paragraph +     \ segment new-end
                ?cs: -
                setblock 0=             \ deallocate unneeded space
                if      command.buf $sys dup 2 =
                        if      "  Couldn't find COMMAND.COM  " ".errmsg
                        then    8 =
                        if      " Not enough memory to run DOS" ".errmsg
                        then    cursorset
                else            "  Couldn't free edit buffer  " ".errmsg
                then
                ?cs: #pars @ setblock 0=        \ reallocate space again
                if      toff 100 + dup 0 c@l 1+ paragraph \ seg len
                        0 swap                           \ seg off len
                        tend 0 rot 16 *                 \ seg off seg off len
                        cmovel
                else            " Couldn't re-allocate buffer " ".errmsg
                        toff 100 + lastline >lineptr tl:!
                        toff 100 + dup 0 c@l 1+ paragraph +
                        lastline 1+ >lineptr tl:!
                then
                r> backto.line          \ go back to where we came from
                getline ;

: docompile     ( --- )
                ?shiftkey
                if      command.buf c@ 0= ?exit
                else    savescr                 \ save the screen
                        8 6 73 13 box&fill
        ."  \r Enter a DOS command line. \0   \1 Enter \0=accept  ESC=cancel"
                        bcr bcr bcr bcr
        ."  Press \1 Enter \0 on an empty command line to spawn a DOS shell"
                        bcr
        ."  for multiple commands. Use \2 EXIT \0 to return to the editor."
                        on> autoclear
                        >attrib1
                        10 9 command.buf 59 lineeditor       ( --- f1 )
                        >norm
                        restscr 0= ?exit        \ leave if canceled edit
                then
                savescr dark
                %doDOS
                command.buf c@ 0<>
                if      cr ." Press a \1 KEY \0 to return to the editor.."
                        key drop
                then
                restscr scrshow ;

' docompile is Ctrl-J

