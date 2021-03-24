\ SEDITWP.SEQ   SED wordprocessor functions             by Tom Zimmer

editor definitions

\ Sub functions used by word wrap.

: split.lineend ( --- )
                wrap.buf linebuf.len blank
                rmargin @ 1- =: screenchar <<space>
                if      space> screenchar 1- 0MAX =: screenchar
                then    screenchar 1+ lmrgn 1+ max
                dup>r =: screenchar
                linebuf screenchar linelen over - 0MAX >r +
                1+ dup wrap.buf 1+ r@ cmove
                r@ wrap.buf c!
                r> blank modified
                putline getline wrapped 0=
                if      wrap.buf c@ =: wraplen
                        on> wrapped r@ =: wraploc
                then    r>drop ;

: prepend.split ( --- )
                linebuf 1+ rmargin @ bl skip 0=
                wrap.buf c@ rmargin @ > or
        if      drop linebuf 1+ lmrgn +
                off> screenchar <nln> off> screenchar
        else    wrap.buf c@ 1+ >r linebuf 1+ dup r@ +
                linelen 1+ r@ + ch/l min r@ - cmove>
                linebuf 1+ r> blank
        then    ch/l linebuf c! dup linebuf 1+ -
                @> rmargin 2 - min =: lmrgn
                >r wrap.buf count r@ swap cmove
                wrap.buf c@ 1+ +!> linelen
                wrap.buf c@ r> linebuf 1+ - + =: screenchar
                modified putline getline ;

\ main function for word wrap.
\ You must set the right margin before this function will work.

: ?wrapfunc     ( --- )         \ conditionally split line
                ?browse ?exit
                off> wraploc off> wrapped off> wraplen
                linelen @> rmargin < ?exit              \ should we bother?
                ?full ?maxlines or ?exit                \ are we full?
                begin   linebuf linelen + c@ bl =       \ strip trailing BL's
                while   linelen 1- 0MAX =: linelen
                repeat
                linelen screenchar 1+ max =: linelen    \ at least to cursor
                linelen @> rmargin < ?exit              \ should we bother?
                screenline curline screenchar
                begin   linelen rmargin @ >=    \ line too long?
                        ?full 0= and
                while   ?addline split.lineend
                        ?addline <sdln>  prepend.split
                repeat  =: screenchar dup curline <>
                if      backto.line
                        <sdln> incr> screenline suln
                else    drop
                then    =: screenline   ;

' ?wrapfunc is ?wrap

\ Sub functions for paragraph justification

variable leadblnks

: ?getword      ( --- f1 )
                rmargin @ linelen 1+ - 0MAX <sdln>
                linebuf 1+ rmargin @ bl skip rmargin @ over -
                leadblnks ! 2dup bl scan bl wrap.buf 1+ c!
                nip - swap over wrap.buf 2+ swap cmove
                1+ dup wrap.buf c! > wrap.buf c@ 1 > and
                ?full 0= and ;

: getword       ( --- ) screenchar >r
                leadblnks @ =: screenchar
                modified del<>bl's delbl's <suln>
                wrap.buf count linebuf 1+ linelen + swap cmove
                wrap.buf c@ +!> linelen r> =: screenchar
                modified ;

: ?delline      ( --- )
                ?lastline ?exit
                curline 1+ #lineseg 0 c@l 2 =
                if      <sdln> <ldel> <suln>
                then    ;

: fillline      ( --- )
                begin   ?getword
                while   getword ?delline
                repeat  ;

\ Main function for paragraph justification.

: justify       ( --- )
                ?browse ?exit
                rmargin @ 131 >
                if      savescr cursor-off
                        17 6 62 11 box&fill
                        ."  You must set the right margin to a value" bcr
                        ."  less than 132 before using justification."
                        bcr bcr
                        ."   \r ** Use Alt-S Setup ** \0    Press a \r KEY "
                        beep key drop
                        restscr cursor-on showcur
                else    ?full ?maxlines or curline #lineseg 0 c@l 2 = or
                        if      sdln off> screenchar exit
                        then    true save!> imode
                        begin   linelen 0> ?lastline 0= and
                                ?full 0= and
                        while   ?wrap fillline
                                incr> screenline clipdown scrshow
                        repeat  begin   linelen 0= ?lastline 0= and
                                while   <sdln>
                                repeat  off> screenchar  off> lmrgn
                        last.textline first.textline - 2/ curline min
                        first.textline + =: screenline clipdown
                        restore> imode emptykbd scrshow ?showfull drop
                then    ;

' justify is jstfy

: rmset         ( --- )         \ set right margin to current column
                20  6 60 12 box&fill
                bcr ."  Right margin currently at column " rmargin @ 3 .r
                bcr
                bcr
                ."  Set right margin to column "
                on> filtering
                49 10 at tib 6 expect  span @ #tib ! >in off
                off> filtering
                22  8 at >rev
                bl word  dup c@ 0> >r number? r> and
                if      drop 10 max 132 min dup rmargin ! dup =: ermargin
                        ."  Right margin set to column " 3 .r 5 spaces
                else    2drop ."   ** Right margin NOT changed. **   "
                then    >norm cursor-off 3 tenths scrshow ;

: sedsetup      ( --- )         \ setup function for SED
                savescr cursor-off
                ['] noop save!> dobutton
                16  6 66 12 box&fill
                ."  Setup options..\s15\r ESC \0 = cancel"
                bcr bcr
                ."  \r R \0 - set Right margin \r W \0 - adjust Window size"
                bcr bcr
                ."  \r S \0 - Split window     \r Z \0 - Zoom Window"
                key bl or >r
                restscr cursor-on showcur
                'r' r@ = if rmset           then
                's' r@ = if splitwind       then
                'w' r@ = if adjwind         then
                'z' r@ = if zoomwind        then
                r>drop
                restore> dobutton
                scrshow showstat cursor-on ;

' sedsetup is sedset

forth definitions

