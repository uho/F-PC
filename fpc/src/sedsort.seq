\ SEDSORT.SEQ   Sort the lines of a paragraph           by Tom Zimmer

editor definitions

\- colsave      0 value colsave

variable sortto
variable sortfrm

create sort1.buf 142 allot
create sort2.buf 142 allot

: sortline      ( --- )
                linebuf 1+ sort1.buf 1+ 132 cmove
                linelen sort1.buf c! ldel
                @> sortfrm dup @> sortto
                do      sort2.buf colsave + 15 blank
                        i #lineseginfo 2- ?cs: sort2.buf rot
                        colsave 15 + min
                        cmovel sort2.buf colsave +
                        sort1.buf 1+ colsave + 20 compare 0>
                        if drop i leave then
                loop    backto.line <nln>
                sort1.buf dup linebuf 132 cmove
                c@ =: linelen  @> rmargin linebuf c!
                modified putline getline
                sortfrm incr   @> sortfrm to.line ;

: ssort         ( --- )         \ sort lines of a paragraph
                ?browse ?exit
                ['] noop save!> dobutton
                savescr savecursor cursor-off
                screenline 4 + dup 14 >
                if      12 -
                then    16  swap 60 over 7 + box&fill
                ."   Sort the LINES of the current PARAGRAPH "
                bcr
                ."   at the current cursor COLUMN = " screenchar 1+ . ." ?"
                bcr bcr
                ."   \1 Y = Yes do the sort \0   \2 ESC = CANCEL "
                bcr bcr
                ."   Turn on CAPS LOCK to sort CASE sensitive "
                cursor-on showcur
                0 >r                            \ a dummy value on rstack
                begin   r>drop                  \ discard the dummy
                        key >r                  \ get a key
                        r@ upc    'Y' =         \ is it a 'Y'
                        r@ ( esc ) 27 = or 0=   \ or an ESC
                while   beep                    \ if not loop till it is
                repeat  r> upc                  \ get key back, and uppercase
                restscr restcursor
                restore> dobutton
                'Y' <> ?exit                    \ compare against 'Y'
                                                \ exit if it isn't
                linelen 0= ?lastline or if sdln exit then
                off> lmrgn      \ clear left margin to zero
                screenchar =: colsave
                ?capslock 0= save!> caps
                     true save!> imode
                           save> screenline
                        0 save!> screenchar
                curline =: sortto
                sdln
                curline =: sortfrm
                begin   modified
                        putline getline
                        linelen 0> ?lastline 0= and
                while   sortline
                        statfunc
                repeat  sortto @ backto.line
                restore> screenchar
                restore> screenline
                restore> imode
                restore> caps
                scrshow ;

' ssort is sortlin

forth definitions

