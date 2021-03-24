\ SEDSORT.SEQ   Sort the lines of a paragraph           by Tom Zimmer

only forth also editor definitions also

I: nextpar     ( --- )
                begin   linelen  0> ?lastline 0= and while <sdln> repeat
                begin   linelen  0= ?lastline 0= and while <sdln> repeat ;

I: prevpar      ( --- )
                <suln>
                begin   linelen  0= ?lastline 0= and while <suln> repeat
                begin   linelen  0> ?lastline 0= and while <suln> repeat
                nextpar ;

I: delpar       ( --- n1 )
                ?lastline if 0 exit then
                0
                begin   linelen
                        ?lastline 0= and
                while   1+ <ldel>
                repeat                  \ return count of deleted lines
                begin   linelen 0=
                        ?lastline 0= and
                while   1+ <ldel>
                repeat  ;

I: undelpar     ( n1 --- )
                0
                ?do  <lundel> loop ;         \ un-delete the deleted lines

I: save1lin     ( --- )
                linebuf 1+ sort1.buf 1+ 10 cmove
                44 7 at sort1.buf 1+ 8 type ;

I: #linelen     ( n1 --- n2 )   \ return line length n2 of line n1.
                #lineseginfo nip nip 2- ;

I: sortpar      ( --- )
                curline >r              \ save where we are
                save1lin delpar >r
                @> sortto backto.line
                begin   linebuf 1+ sort1.buf 1+ 8 compare 0> 0=
                        ?lastline 0= and
                        curline 2r@ drop < and
                while   nextpar
                repeat  r> undelpar
                r> to.line nextpar ;

: parsort       ( --- )         \ sort lines of a paragraph
                linelen 0= ?lastline or if sdln exit then
                20 06 60 08 box&fill ."  Paragraph sorting ... "
                cursor-off
                screenchar =: colsave
                ?shiftkey save!> caps
                     true save!> imode
                           save> screenline
                        0 save!> screenchar
                curline =: sortto
                nextpar
                begin   ?lastline 0=
                        key? 0= and
                while   sortpar
                        showstat
                repeat  sortto @ backto.line
                key? if key drop then
                restore> screenchar
                restore> screenline
                restore> imode
                restore> caps
                cursor-on
                scrshow ;

' parsort is sortlin

only forth also definitions

