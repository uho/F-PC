\ WINSTACK.SEQ          Pull Down WINDOW Stack Display     by Tom Zimmer

\ Idea from Chuck Gibson

comment:

  Display the stack vertically on the screen below the stack depth
entry in the status line.  Enabled as long as you hold down the RIGHT and
LEFT SHIFT keys together.

comment;

only forth also definitions hidden also

headerless

defined ?showstack nip                  \ if ?SHOWSTACK is already defined
#if ' noop is bgstuff #endif            \ then clear bgstuff to NOOP

3 value smask   \  1 = right shift,  2 = left shift, 4 = Ctrl,  8 = Alt
                \ 10 = Scroll Lock, 20 = Num Lock,  40 = Caps Lock

: ?showstack    ( --- )         \ display a pulldown stack picture
                defers bgstuff                  \ on the defered chain
                depth 0< ?exit                 \ don't bother if less than 1
                $40 $17 c@L smask and smask =   \ check for ENABLE shift key
                if                              \ if pressed THEN doit
                        savecursor              \ save current screen pos
                        savescr                 \ save the screen
                        cursor-off              \ turn off the cursor
                        depth dup>r             \ save depth for later
                        if                      \ if depth non-zero
                                18 0 at >rev
                                ."  Depth " r@ (.) dup>r type 4 r> - spaces
                                >norm
                                18 1 28 r@ rows 2- min
                                1+ >menu true <box>
                                                \ the draw a box for stack
                                28 1 at ." \r Top of stack "
                                0 r@ 1- rows 3 - min  \ up to ROWS elements
                                do      19 i 1+ at
                                                \ position cursor for each
                                        i pick 7 u.r 2 spaces
                                                \ print stack value & space
                            -1 +loop            \ loop till done
                        else    17 0 at         \ else display EMPTY
                                >rev ."  Stack Empty " >norm
                        then    r>drop          \ discard copy of depth
                        begin   $40 $17 c@L smask and smask = 0=
                        until                   \ loop till SHIFT released
                        restscr                 \ restore screen
                        restcursor              \ restore cursor position
                then    ;

' ?showstack is bgstuff

only forth also definitions

headers

