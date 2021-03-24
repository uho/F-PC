\ NEWFILE.SEQ   Create a new file, and edit it.         by Tom Zimmer

defined edit nip 0= #if \s #endif       \ stop loading if EDIT not defined

only forth also definitions hidden also

: newfile       ( t1 --- )      \ Open an existing file or CREATE a new one.
                bl word c@ 0=                       \ if no name on line
                if      cr ." Filename to create? "             \ prompt him
                        #out @ #line @ here dup off 24 lineeditor 0=
                        here c@ 0= or abort" No file specified"
                then    here
                seqhandle dup>r hclose drop         \ close any file open
                r@ $>handle                         \ move name to SEQHANDLE
                r@ hopen                            \ if we can't open it then
                if      r@ hcreate                  \ create the new file
                        abort" Create file FAILED"  \ abort if we can't
                        2573                        \ decimal for CRLF
                        sp@ 2 r@ hwrite 2drop       \ write an empty line
                        r@ hclose drop              \ update the directory
                        r@ hopen drop               \ for the new file
                else    ." \1 File already exists! "
                        1 seconds                   \ warn already exists
                then    r>drop
                0 edit ;                            \ enter editor

' newfile is makefile

' newfile alias sed             \ for compatibility with F-PC 2.25

