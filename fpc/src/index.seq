\ INDEX.SEQ     Build an index of hyper text links      by Tom Zimmer


only forth also definitions hidden also editor also

handle indexhndl        indexhndl !hcb hyper.ndx

create crlf$ $0D c, $0A c,

: index.fname   ( --- )
                fstime @ 0=
                if      ['] hyperchar >body 1 indexhndl hwrite drop
                        seqhandle >pathend" indexhndl hwrite drop
                        crlf$ 2 indexhndl hwrite drop
                        fstime on
                then    ;


: index.file    ( --- )
                IBRESET
                0.0 seqhandle movepointer
                0.0 filepointer 2!
                off> fstime
                ." ." ?cr
                20000 1
                do      lineread c@ 0= ?leave
                        searchsetup search nip
                        if      index.fname
                                $2020 outbuf count + 2- ! \ terminate bl's
                                outbuf count 1 /string  \ skip (ø)
                                2dup bl scan nip - 1+   \ addr and len +1
                                indexhndl hwrite drop   \ write to file
                                i 0 <# $0A hold $0D hold #S #>
                                                \ convert to $ + CRLF
                                indexhndl hwrite drop
                                ?keypause
                                PRINTING @ 0= @> statv and
                                IF <.STAT> THEN
                        then
                loop    ;

: bindex        ( file_spec --- )       \ Print first line of files
                SAVESTATE
                ?in-empty               \ if nothing following command
                if      " *.txt" ">$ $>tib
                        withname off
                then
                indexhndl hcreate abort" Couldn't make INDEX file."
                ['] hyperdest >body 1 slook.buf place
                ['] index.file fallof
                crlf$ 2 indexhndl hwrite drop
                indexhndl hclose drop
                RESTORESTATE bye ;

