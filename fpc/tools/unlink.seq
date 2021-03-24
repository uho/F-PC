\ UNLINK.SEQ    An extension to the defered word control set by Tom Zimmer

\ **********************************************************************
\ UNLINK a word from somewhere in a defered word chain.  Searches for
\ 'name' in the chain of defered word a1 and removes it from the chain.
\ Stops when the word is found, when NOOP is found in the chain, or it
\ has tried upto ULMAX times.
\ **********************************************************************

9 value ulmax   \ max unlink depth try
0 value ultrys  \ number of unlink trys performed

: UNLINK        ( a1 | name -- )        \ unlink name from the defered
                                        \ chain a1
                ' >r
                off> ultrys
                dup @rel>abs [ ' key @rel>abs ] literal = >r
                >body r> if @ up @ + then
                dup @ @rel>abs [ ' defers @rel>abs ] literal <>
                ABORT" Can only UNDEFER through a : (colon) definition."
                dup @ dup r@ =
        if      >body @ +xseg 0 @L swap !
        else    nip
                begin   dup >body @ +xseg 0 @l dup r@ <>
                        over ['] noop <> and
                        ultrys ulmax < and              \ not reached limit
                while   nip                             \ try next level
                        incr> ultrys                    \ bump try count
                repeat  dup ['] noop <>
                        ultrys ulmax < and
                if      >body @ +xseg 0 @L
                        swap >body @ +xseg 0 !L         \ found it, remove
                else    2drop
                then
        then    r>drop ;


