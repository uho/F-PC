\ STATUS.SEQ    Display a status line at screen top.    by Tom Zimmer

0 value vocv    \ show vocabulary stack value flag

: <.STAT>       ( --- )
                printing @ ?exit        \ NO status if printing
                savecursor
                base @ >r decimal
                0 0 at >attrib1
                ."  C - " sp@ here - 0 1000 um/mod nip (u.) type ." k : - "
                #listsegs xhere drop xseg @ - - 16 *d
                1000 um/mod nip (u.) type ." k"
                2 spaces depth
                if      >attrib4 ."  Depth " depth (u.) dup>r type
                        4 r> - spaces >attrib1
                else    ." Stack Empty"
                then    2 spaces seqhandle >hndle @ -1 =
                if      seqhandle dup clr-hcb pathset drop
                        -2 seqhandle >hndle !
                then    .seqhandle cols #out @ - spaces
                vocv                            \ if vocabulary showing is on
                if      cols 11 - 1 at >attrib3
                        current @ body> >name space %.id eeol
                        >attrib1
                        #vocs 1- context over 0
                        do      dup @ ?dup 0= if nip i swap leave then
                                cols 11 - i 2+ at
                                body> >name space %.id eeol 2+
                        loop    drop
                        cols 11 - over 2+ at space
                        ['] root >name %.id eeol >norm
                        1+ #vocs over - bounds over min
                        ?do     cols 11 - i 2+ at 11 spaces
                        loop
                then    >attrib1
                cols 7 - 0 at space
                dtbuf off
                gettime drop build-hm dtbuf count type space >norm
                r> base !
                restcursor ;

: .STATUS       ( -- )
                defers status
                ?stack
                statv @
                if      <.stat>
                then    ;

' .STATUS IS STATUS             \ Status printed for command lines.
' <.STAT> IS LOADSTAT           \ Status printed while loading.

: STATON        ( --- )
                STATV ON ;

: STATOFF       ( --- )
                STATV OFF ;

STATON          \ default to displaying status line

: VOCON         ( -- )
                ON> VOCV ;

: VOCOFF        ( -- )
                OFF> VOCV ;

VOCON           \ default to displaying the vocabulary stack

