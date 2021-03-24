\ MACROS.SEQ    Keyboard macros for F-PC                  by Tom Zimmer

\ anew macros

comment:
                        Keyboard macros for F-PC.
To DEFINE a macro;
        Press Alt-M             \ starts defining, waits for next key
        Press Alt-1             \ tells it which macro to make, can be
                                \ Alt-1 through Alt-5, waits for first
                                \ key of macro
        Start entering keys.    \ keys are passed on to program and saved too
        Press Alt-M             \ Ends definition of the new macro
        Thats all there is to MAKING macros.

To PERFORM a macro;
        Press Alt-1             \ Macro processor passes back all keys
                                \ one for each call to key until empty
        Thats all there is to DOING macros.

To REPEAT a macro;
        Press Alt-R             \ We will be repeating a macro key
        Type a # between 0-999  \ The number of times to repeat the macro
        Press Enter             \ Accept the number of times to repeat
        Press Alt-1             \ the macro to repeat.
comment;

only forth also hidden also definitions

  0    value ?mdelay
  1    value mdelay_val

headerless

248 constant macbase            \ Alt-1
  8 constant #macros            \ Through Alt-0
128 constant macsiz

#macros macsiz *D pointer macseg

handle macfile                  \ make the macro file handle

macfile !hcb F-PC.MAC           \ initialize macro file name

: %macinit      ( -- )
                macseg 0= abort" Couldn't allocate MACRO buffer!"
                macseg 0 #macros macsiz * 0 lfill ;

%macinit                \ initialize macros for use

: macinit       ( --- )
                defers initstuff
                %macinit ;

' macinit is initstuff

headerless

: mdelaytgl     ( -- )
                ?mdelay 0= !> ?mdelay ;

: ms:           ( n1 --- seg n1 )
                macseg swap ;

variable curmac
\ 0 value ?doingmac     \ moved down to UTILS.SEQ to make it available
                        \ to test if we are currently doing a macro.
                        \ useful in the editor to reduce the screen updates
                        \ while performing macros.
variable mactimes       \ number of times to perform a macro
variable repkey         \ macro key we are repeating
variable makingmac

: savemacs      ( --- )
                macfile hcreate
                if      37 7 at
                        ." \r Could not make Macro file! " beep
                else    0 #macros macsiz * dup>r macfile macseg exhwrite
                        r> -    37 5 at >rev
                        if      ."  Write error to Macro file! " beep
                        else    ."  Macros Saved.\s14"
                        then    >norm macfile hclose drop
                then    1 seconds ;

: loadmacs      ( --- )
                macfile hopen dup 0=
                if      drop
                        #macros macsiz *       \ length of read
                        0 over macfile macseg exhread - dup
                        \ clear out the buffer on read length wrong!
                        if      macseg 0 #macros macsiz * 0 lfill
                        then    macfile hclose drop
                then    ( --- f1 )      \ true if failed
                37 5 at >rev
                if      ."  No Macros to load.\s05\b"
                else    ."  Macros Loaded.\s13"
                then    >norm 1 seconds ;

: viewmacs      ( --- )
                savescr
                6 2 74 over #macros 2* + 3 + box&fill
                ." Press \r ESC \0 when done viewing macros."
                bcr bcr macsiz 2/
                #macros 0
                do      i 1+ 1 .r ." "
                        macseg macsiz i * 2 pick          typeL bcr
                        ."   "
                        macseg macsiz i * 2 pick + 2 pick typeL bcr
                loop    drop
                defers key drop
                restscr ;

: .askmacro     ( --- )
                savecursor
                cursor-off savescr >norm
                ['] noop save!> dobutton
                10  4 70 19 box&fill
                ."  \r Making a MACRO... "
                bcr bcr
        ."  First press \r Alt-1 \0 through \r Alt-8 \0 to specify the macro"
                bcr
        ."  you are making, then; enter the keys of your macro,"
                bcr
        ."  finally; press Alt-M again to finish making a macro."
                bcr
        ."  컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴컴"
                bcr
        ."  To CANCEL making a macro press \r ESC \0 now."
                bcr bcr
        ."  To PERFORM a macro press \r Alt-1 \0 through \r Alt-8 \0 when "
                bcr
        ."  you are \r NOT \0 looking at this window."
                bcr bcr
                ."  Press \r V \0 to VIEW the currently defined macros."
                bcr
                ."  Press \r D \0 to toggle delayed macros: "
                ?mdelay
                if      ."  ON  "
                else    ."  OFF "
                then    bcr
                ."  Press \r L \0 to load macros, or \r S \0 to save macros."
                defers key
                 27 over       = if drop 178            then
                'v' over bl or = if drop 178 viewmacs   then
                'l' over bl or = if drop 178 loadmacs   then
                's' over bl or = if drop 178 savemacs   then
                'd' over bl or = if drop 178 mdelaytgl  then
                restore> dobutton
                restscr
                restcursor ;

: .repmac       ( -- x y x' y' ) \ display repeat message, and return the
                                 \ x/y corrdinates of both key prompts
                16 4 63 15 box&fill
                ." \1  Auto repeat a MACRO or other keyboard key  "
                bcr
                bcr ."  Enter a number between 000 and 999 for the"
                bcr ."  number of times to repeat the key:  " at?
                bcr
                bcr ."  Then PRESS Enter to be prompted for the key"
                bcr ."  to repeat:  " at?
                bcr
                bcr ."  PRESS ESC to CANCEL repeat a key" ;

: setmac        ( n1 --- )      \ set the current macro pointer.
                macsiz * curmac ! ;

: ?abortmac     ( -- f1 )
                bioskey? dup            \ does user want to abort mac
                if      drop            \ discard nonzero
                        bioskey         \ get the key pressed
                        $FF and 27 =    \ did he press ESC?
                then    ;

: ?domac        ( --- <c1> )    \ Are we doing a macro, if so return a key.
                ?doingmac
                if      ?abortmac
                        if      off> ?doingmac
                                off> mactimes
                        then
                        curmac @ ms: c@l                \ get the macro key
                        curmac @ 1+ ms: c@l 0=          \ test for all done
                        if      off> ?doingmac
                        then    curmac incr
                        ?mdelay                         \ delay macros?
                        if      mdelay_val tenths       \ yes, slow down
                        then    2r> 2drop
                then    ;

: ?domac1       ( c1 --- c2 )   \ If a macro key, start macro and return
                                 \ the first macro key if its good.
                dup macbase dup #macros 1- + BETWEEN
        if      on> ?doingmac macbase - setmac
                curmac incr
                curmac @ 1- ms: c@l ?dup 0=
                if      off> ?doingmac
                        curmac @ 1- macsiz / macbase +
                else    curmac @ ms: c@l 0=             \ test for all done
                        if      off> ?doingmac
                        then
                then
        then    ;

: ?repmac       ( --- <c1> )
                mactimes @
                if      mactimes decr
                        ?abortmac
                        if      off> mactimes
                                off> ?doingmac
                        then
                        repkey @ ?domac1 2r> 2drop
                then    ;

: ?repmac1      ( c1 --- c1 )
                dup 147 = makingmac @ 0= and    \ Alt-R = repeating
                if      drop
                        save> tx save> ty
                        savecursor
                        savescr cursor-on .repmac 2>r 2>r
                        >attrib1
                        0
                        begin   begin   2r@ at dup 4 .r space
                                        (key) dup '0' '9' between
                                while   '0' - swap
                                        100 /mod drop 10 * + 999 min
                                repeat  dup   13 <>     \ not CR
                                        over  27 <> and \ not ESC
                                        over 147 <> and \ not Alt-R
                        while   drop
                        repeat  2r> 2drop
                        2r@ at space 2r> at
                        swap 1- 0MAX 999 min mactimes !
                        13 =
                        if      (key) dup repkey !
                                dup 27 =
                                if      drop
                                        off> mactimes
                                        147
                                else    dup emit
                                        2 tenths
                                then
                        else    off> mactimes
                                147
                        then
                        restscr
                        restcursor
                        restore> ty restore> tx
                then    ;

: ?addmac       ( c1 --- c1 )   \ conditionally add a key to current macro.
                makingmac @
                if      dup curmac @ ms: c!l
                        curmac incr
                        0 curmac @ ms: !l
                then    ;

: ?start/stopmac ( --- c1 )     \ Conditionally start of stop making a macro.
                save> tx save> ty
                begin   defers key
                        dup 178 = makingmac @ 0= and    \ Alt-M = Make macro
                        if      drop .askmacro
                                                        \ Alt-1 to Alt-5
                                dup macbase dup #macros 1- +
                                BETWEEN 0=              \ macro key number
                                if      restore> ty restore> tx
                                        exit    \ ********* EXITS HERE ****
                                then
                                macbase - setmac
                                savecursor
                                savescr cursor-off
                                20  6 60 10 box&fill
                                bcr
                                ." \s04\r Enter the keys of your macro \:03"
                                restscr
                                restcursor
                                curmac @ ms: macsiz 0 lfill
                                makingmac on
                                defers key
                        then    dup 178 = makingmac @ and \ Alt-M and making
                while   drop makingmac off
                        savecursor
                        savescr cursor-off
                        20  6 60 10 box&fill
                        bcr ."     \r *** Make Macro Completed *** \:10"
                        restscr
                        restcursor
                repeat
                restore> ty restore> tx
                ;

: mackey        ( --- c1 )      \ This definition looks more like PASCAL
                macseg 0=
                if      defers key
                else    ?domac  ?repmac  ?start/stopmac
                        ?addmac ?repmac1 ?domac1
                then    ;


\ Alt-1 = 248, to Alt-5 = 252

' mackey is key

only forth also definitions

headers

