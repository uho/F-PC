\ MONOCROM.SEQ  Monochrome support words                by Tom Zimmer

DECIMAL

\ Video attribute control mechanism for monochrome by Tom Zimmer

$07 VALUE NORMVAL                               \ VALUE FOR NORMAL VIDEO
$78 VALUE REVVAL                                \ VALUE FOR REVERSE VIDEO
$07 VALUE NORMALVAL
$78 VALUE REVERSEVAL

: >NONE         ( --- ) NORMVAL ATTRIB C! ;     \ NORMAL
: >NONEBG       ( --- ) NORMVAL $F0 AND ATTRIB C@ $0F AND OR ATTRIB C! ;
: >REVERSE      ( --- ) REVVAL  ATTRIB C! ;     \ REVERSE
: >UL           ( --- ) $01 ATTRIB C! ;         \ UNDERLINE
: >BOLD         ( --- ) $7F ATTRIB C! ;         \ BRIGHT
: >BOLDUL       ( --- ) $79 ATTRIB C! ;         \ BOLD UNDERLINE
: >BOLDBLNK     ( --- ) $8F ATTRIB C! ;         \ BOLD BLINK
: >REVBLNK      ( --- ) $F0 ATTRIB C! ;         \ REVERSE BLINK

\       Some extra words, not loaded for now.

\ : >BOLDBLNKUL   ( --- ) $89 ATTRIB C! ;       \ BOLD BLINK & UNDERLINE
\ : >BLINK        ( --- ) $87 ATTRIB C! ;       \ BLINK
\ : >BLANK        ( --- ) $00 ATTRIB C! ;       \ BLANK, NO DISPLAY

' >NONE    IS >NORM
' >REVERSE IS >REV
' >NONEBG  IS >NORMBG

>NORM           \ Default to Normal video.

: >MONO         ( --- )         \ Select hilighting for monochrome monitor.
                ['] >UL         IS >ATTRIB1 ['] >BOLDBLNK IS >ATTRIB5
                ['] >BOLDUL DUP IS >ATTRIB2               IS >ATTRIB6
                ['] >BOLD   DUP IS >ATTRIB3               IS >ATTRIB7
                ['] >REV    DUP IS >ATTRIB4               IS >ATTRIB8
                >NORM ;
        >MONO

' >MONO IS INITMONO     \ Enable attributes according to video board.

' >MONO IS INITCOLOR    \ Enable attributes SAME AS MONO FOR NOW.

\ Reverse video support.

: invert-screen         ( --- )
                        blank.color
                        video-seg @ rows cols 2* * 1
                        do      dup i c@l dup NORMVAL =
                                if      drop REVVAL over i c!l
                                else    REVVAL =
                                        if      NORMVAL over i c!l
                                        then
                                then
                     2 +loop    drop show.color ;

: white-on-black        ( --- )
                        NORMVAL NORMALVAL <>
                        if      invert-screen
                        then
                        NORMALVAL  =: NORMVAL
                        REVERSEVAL =: REVVAL
                        >norm ;

: black-on-white        ( --- )
                        NORMVAL REVERSEVAL <>
                        if      invert-screen
                        then
                        REVERSEVAL =: NORMVAL
                        NORMALVAL  =: REVVAL
                        >norm ;

