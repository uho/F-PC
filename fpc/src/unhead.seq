\ UNHEAD.SEQ         New BE-heading mechainism for F-PC      by Tom Zimmer

\ A new BEHEAD mechanism for F-PC.

\ features:
\               Not sensitive to vocabulary search order.
\               You don't have to allocate space for the beheaded headers.
\               Internal headers are built from the top of head space down.
\               All vocabularies are searched during the behead process.
\               Full compatibility with the previous beheading mechanism.
\               A new word I: has been added for Internal : definitions.

: unthread      ( tlink thread -- )     \ unlink all heads in thread
                                        \ above tlink
                swap >r
                begin   dup @ r@ u>             \ while link is above tlink
                while   dup @
\                       dup l>name .id ?cr
                        y@ over !               \ unlink top head
                repeat  @                       \ then check entire chain
                ?dup                            \ unless thread is zero
        if      begin   dup y@ ?dup             \ while not at chain end
                while   dup r@ u>               \ if head is above tlink
                        if
\                               dup l>name .id ?cr
                                y@ over y!      \ unlink this head
                        else    nip             \ discard previous link
                        then
                repeat  drop
        then    r>drop ;

: unvoc         ( tlink voc-body -- )   \ unlink vocabulary
                #threads 2* 0
                do      2dup i +        \ -- head thread
                        unthread        \ *unlink above head
             2 +loop    2drop ;         \ *cleanup stack

#headsegs 4089 min 16 um* drop value I:end

: unhead        ( a1 -- )       \ unlink all heads above a1
                >r voc-link
                begin   @ ?dup
                while   dup #threads 2* -       \ *step back to body
                        r@ swap                 \ -- tlink voc-body
                        unvoc                   \ unlink vocabulary
                repeat
                r> ydp @ umin ydp !             \ reduce header pointer
                #headsegs 4089 min 16 um* drop !> i:end ;

0 value beheadable
0 value save_"header

: "iheader      ( a1 -- )
                save> ydp                               \ save head ptr
                I:end over c@ 5 + - dup ydp ! !> I:end  \ to end of head spc
                <"header>                               \ make the header
                restore> ydp                            \ restore head ptr
                save_"header 0= ?exit                    \ leave if empty
                save_"header is "header
                off> save_"header ;

: |             ( -- )
                beheadable 0= ?exit             \ only if beheadable
                save_"header  ?exit             \ don't nest this operation
                @> "header !> save_"header      \ preserve current header func
                ['] "iheader is "header ;

: I:            ( name -- )     \ defind a temporary definition
                | : ;

: headers       ( -- )                  \ select new words to have headers
                beheadable 0= ?exit
\                cr ." Headers    " seqhandle count type loadline @ 6 u.r
                ['] <"header> is "header
                off> save_"header ;

: headerless    ( -- )
                beheadable 0= ?exit
\                cr ." Headerless " seqhandle count type loadline @ 6 u.r
                ['] "iheader is "header ;

: hwords-       ( -- )
                off> beheadable ;

: hwords+       ( -- )
                on> beheadable
                headers ;

: behead        ( -- )                  \ discard all internal heads
                ydp @ unhead ;

hwords+         \ enable beheading mechanism as default state

