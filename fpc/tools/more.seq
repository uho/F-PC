\\  MORE.SEQ        Screen update control for F-PC      by Tom Zimmer

        This file adds four new words to F-PC;  PAGED, SLOWER, FASTER
        and NORMAL.  These words allow you to control the screen output
        of F-PC in two ways.  PAGED selects a display mode where F-PC
        will wait for a key after 100 calls to ?KEYPAUSE.  This
        translates to about a screen full of WORDS.  Any key but ESC will
        continue with the next screen full.  SLOWER and FASTER deselect
        PAGED, and either increase of decrease a delay that occurs in
        ?KEYPAUSE, reducing the screen output rate to a usable level.
        Multiple executions of SLOWER or FASTER double or halve the delay
        for more precise control of the screen output.  NORMAL restores
        screen display speed to an undelayed or paged mode.


{

0 value morecnt
8 value slowms

: %more         ( -- )
                incr> morecnt
                morecnt 100 >
                if      key 27 = ?dostop
                        off> morecnt
                else    key?
                        if      key 27 = ?dostop
                                key 27 = ?dostop
                        then
                then    ;

: %slow         ( -- )
                slowms ms
                key?
                if      key 27 = ?dostop
                        key 27 = ?dostop
                then    ;

' %more >body @ constant more_body
' %slow >body @ constant slow_body

: paged         ( -- )
                @> ?keypause slow_body =
                if      ['] ?keypause ['] %slow switch_bodies
                then
                @> ?keypause more_body <>
                if      ['] ?keypause ['] %more switch_bodies
                then    off> morecnt ;

: slower        ( -- )
                @> ?keypause more_body =
                if      ['] ?keypause ['] %more switch_bodies
                then
                @> ?keypause slow_body <>
                if      ['] ?keypause ['] %slow switch_bodies
                else    slowms 100 min 2* !> slowms
                then    ;

: faster        ( -- )
                @> ?keypause more_body =
                if      ['] ?keypause ['] %more switch_bodies
                then
                @> ?keypause slow_body =
                if      slowms dup 2/ 5 max !> slowms 5 <
                        if      ['] ?keypause ['] %slow switch_bodies
                        then
                then    ;

: normal        ( -- )
                @> ?keypause more_body =
                if      ['] ?keypause ['] %more switch_bodies
                then
                @> ?keypause slow_body =
                if      ['] ?keypause ['] %slow switch_bodies
                then    ;

}

