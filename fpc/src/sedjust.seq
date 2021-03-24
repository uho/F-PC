\ LEFTJUST.SEQ  Words to manipulate a paragraphs left edge.

editor definitions

variable left#

: getleft#      ( --- )
                savescr
                ['] noop save!> dobutton
                screenline 7 - dup 1 <
                if      12 +
                then
                16 swap 64  over 5 + box&fill
                ."  Adjust paragraph left margin.. \r ESC \0 = cancel"
                bcr bcr
                ."  Press 1 to 9 for # of spaces to shift right" bcr
                ."  Press \1 - \0 to REMOVE leading spaces"
                showcur key
                restore> dobutton
                restscr
                '-' over =
                if      -1 left# !  drop exit then
                dup '0' '9' between
                if      48 - dup left# !
                else    0 left# !
                then    drop ;

: rzero         ( --- )
                true save!> imode               \ run in insert mode
                screenchar >r
                screenline curline
                begin   linelen 0>              \ while line not empty
                        ?lastline 0= and        \ and not lastline
                while   0 r@ 0
                        ?do     chrptr 1- c@ bl <> ?leave
                                bdel modified 1+
                        loop    ?dup
                        if      %lwrd ( 0 !> screenchar )
                                1 max 80 min 0
                                do      bl schr
                                loop
                        then    r@ !> screenchar
                        sdln scrshow
                repeat  backto.line =: screenline
                r> !> screenchar
                restore> imode
                emptykbd scrshow ;

: lzero         ( --- )
                ?browse ?exit
                off> lmrgn
                ?ctrlkey
        if      rzero
        else    screenline curline
                begin   linelen 0>              \ while line not empty
                        ?lastline 0= and        \ and not lastline
                while   chrptr c@ bl =
                        if      delbl's
                                modified
                        then    sdln scrshow
                repeat  backto.line =: screenline
                emptykbd scrshow
        then    ;

: ljust         ( --- )         \ adjust left margin by 4 chars
                ?browse ?exit
                ?shiftkey
                if      lzero exit
                then
                ?full ?maxlines or
                if      sdln exit
                then
                getleft# left# @ 0= ?exit
                left# @ 0<
                if      lzero
                else    save> screenline         \ preserve SCREENLINE,
                        true  save!> imode              \          IMODE,
                        mxlln save!> rmargin            \       &  RMARGIN
                        curline
                        begin   linelen 0> ?lastline 0= and
                        while   left# @ dup 0
                                do      bl schr
                                loop    negate  +!> screenchar
                                sdln scrshow
                        repeat
                        restore> rmargin
                        restore> imode
                        backto.line
                        restore> screenline
                        emptykbd scrshow
                then    ;

' ljust is lftjust

forth definitions

