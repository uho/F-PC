\ AUTOFOR.SEQ   experimental auto forward referencing   by Tom Zimmer

comment:

  This file contains the tools to allow automatic forward reference
resolution of Forth words.  there is a runtime overhead which is
equivalent to a DEFERed word.  Words that are forward referenced are
automatically defined, and then when really defined, they are automatically
resolved.

comment;

0 value savein

: F]            ( -- )  \ Automatic defing of forward referenced words
                        \ as they are encountered.
                state on
        begin   ?stack
                >in @ !> savein                 \ Preserve >IN
                defined dup
                if      0>
                        if    execute
                        else   x,   then
                else    drop    %number  0=
                        if      2drop           \ cleanup stack
                                reveal          \ include this definition
                                warning @
                                if      cr here count type
                                        ."  creating forward reference"
                                then
                                savein !> >in   \ Restore >IN
                                here x,         \ compile HERE in existing def
                                defer           \ create a new DEFERed word
                        else    double?
                                if          [compile] dliteral
                                else drop   [compile] literal   then
                        then
                then    true done?
        until   ;

: "fheader       ( str --- )    \ automatic forward reference resolution
                                \ version of "HEADER.
                spcheck
                dup find        recursive
                if      dup     (lit) "fheader > \ must be defined after &
                                                 \ ONLY if its a DEFERed word
                        over @rel>abs ['] number @rel>abs = and
                        if      warning @
                                if      over cr count type
                                        ."  forward reference resolved"
                                then
                                \ then resolve the reference
                                here over >body !
                        then
                then    drop ( str )
                align  yhere 2- y@ cnhash  here cnhash  <>
                if      yhere here cnhash dup y@ rot umin swap
                        y! ( >name hash entry )
                then    ,view
                yhere over current @ hash dup @  y,  ( link  ) ! ( current )
                yhere last ! ( remember nfa )
                yhere ?cs: rot  dup c@  width @  min 1+ >r  ( yh cs str )
                yhere ys: r@ cmovel ( copy str ) r> ydp +! align ( nam )
                128 swap ycset   128 yhere 1- ycset   ( delimiter bits )
                here y, ( cfa in header )
                yhere here cnhash 2+ y! ( valid stopper in next n hash entry)
                ;

: fheader       ( | name --- )
                bl word ?uppercase "fheader ;

: autofon       ( --- )
                ['] f] is ]
                ['] fheader is header ;

: autofoff      ( --- )
                ['] (]) is ]
                ['] <header> is header ;

\ we will now turn on automatic forward referencing

autofon         \ This enables it

warning on      \ still want to be warned whenever one occurs

