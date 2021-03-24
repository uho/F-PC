\ SVALUES.SEQ           An sample file on VALUE usage   by Tom Zimmer

comment:

  This file contains an example of how an existing program can be modified
to use a VALUE word in place of a VARIABLE to enhance program readability
and performance.

NOTE:   VALUEs are NOT more portable than VARIABLEs, and will NOT make
        your programs easier to move to another Forth system.

  Observe the use of the word COUNTER in the following program segments.

comment;

\ program segment using a VARIABLE named COUNTER

create vowels ," aeiouAEIOU"
variable counter

: vcount        ( | <string> --- )  \ get count of vowels in <string>
                counter off
                0 word  drop
                vowels count bounds
               ?do      here count
                        begin   i c@ scan dup
                        while   1 counter +!
                                1 -1 d+
                        repeat  2drop
                loop    cr ." Found " counter @ . ." vowels" ;

\ program segment using a VALUE named COUNTER

create vowels ," aeiouAEIOU"
0 value counter

: vvcount       ( | <string> --- )  \ get count of vowels in <string>
                off> counter
                0 word drop
                vowels count bounds
               ?do      here count
                        begin   i c@ scan dup
                        while   incr> counter
                                1 -1 d+
                        repeat  2drop
                loop    cr ." Found " counter . ." vowels" ;

