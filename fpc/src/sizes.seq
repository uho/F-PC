\ SIZES.SEQ     Get the size of any Forth words CFA     by Tom Zimmer


handle sizehndl
0 value size-seg
defer sfind             ' find is sfind

: getsize       ( --- )
                size-seg ?exit
                " KERNEL.SIZ" sizehndl ">handle
                sizehndl hopen
                if      cr ." Failed to open KERNEL.SIZ"
                else    cr ." Reading 64k image of cfa sizes.."
                        $1000 alloc 8 = memchk nip =: size-seg
                        0 $ff00 sizehndl size-seg exhread $ff00 -
                        if      cr ." Read error from SIZE file"
                        then    sizehndl hclose drop cr
                then    ;

getsize

: size-save     ( --- )                 \ save the cfa sizes file
                size-seg 0= ?exit
                cr ." Saving 64k image of cfa sizes.."
                sizehndl hcreate
                if      cr ." could not create size file"
                else    0 $ff00 sizehndl size-seg exhwrite $ff00 -
                        if      cr ." write error to size file"
                        then    sizehndl hclose drop
                        off> size-seg
                then    cr ;

: size-set      ( --- )
                size-seg 0= ?exit               \ must be initialized
                size-seg dup>r 0 @L             \ if non-zero then
                if      here r@ 0 @L -          \ calculate actual length
                        r@ dup 0 @L !L          \ fill in code length WORD
                        xdp @                   \ length of list
                        r@ dup 0 @L 2+ !L       \ fill in list length WORD
                        xhere paragraph + xdpseg !
                        xdp off                 \ round up list
                then    here r> 0 !L ;

: s-header      ( | <name> --- )
                size-set
                ( defers header )       \ DEFERS is not defined yet
                <header> ;              \ so must explicitly specify

' s-header is header                    \ link into header for future headers

here size-seg 0 !L
xhere paragraph + xdpseg ! xdp off      \ round up list

: size          ( | <name> --- )
                getsize size-seg 0=
                abort" Could not read KERNEL.SIZ"
                bl word ?uppercase sfind 0= ?missing       ( --- cfa )
                ." CFA = "  dup h.
                size-seg over @L ?dup
                if      ." Size of CODE = " dup u.
                        ."  LIST = "
                        4 <
                        if      0 u.
                        else    size-seg swap 2+ @L u.
                        then
                else    ." SIZE is not available"
                        drop
                then    ;


