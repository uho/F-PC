\ BOXTEXT.SEQ   Some simple boxes around text.          by Tom Zimmer

comment:

        BOXTEXT.SEQ

          A cute little routine that works like ." to print messages
        to the screen.  The difference here is that .BOX" requires an
        X and Y coordinate of where to start printing the message,
        and it also prints a box around your text string, with
        graphic line draw characters.  .BOX" is used as follows:

                : boxtest       ( --- )
                                10 10 .box" hello there"
                                15 20 .box" This is a box test" ;

          As you can see it is used like ." .

        BOX  &  BOX&FILL

          These words have been generalized simewhat to automatically
        position the cursor on their first empty line after drawing
        the box. You can then simply use .", TYPE, or EMIT or SPACE
        to display text on this first line. To get to the next line
        of the box, use BCR for BOX CR which will position the cursor
        on the first character position of the second boc line. Here
        is an example:


                : example       ( --- )
                                10 10 35 15 box&fill
                                    ." This is the first line"
                                bcr ." this is the second line"
                                bcr ." this is the third line"
                                bcr ." ect." cr ;   \s

        You can load this example by typing 29 LOAD <enter>. After
        doing that type EXAMPLE <enter> to see the box and text.

comment;

only forth also hidden also definitions

0 value tx   0 value ty
0 value bx   0 value by

0 value fbx
0 value boxchars
0 value bline                   \ box line where text will go next

: <box>         ( left top right bottom filled --- )
                =: fbx
                rows 1- min =: by
                cols 1- min =: bx
                =: ty =: tx
                tx ty at boxchars 1 TYPE        \ top left corner
                boxchars 1+ c@ bl <>
                if      boxchars 1+             \ top line character
                        bx tx - 1- 0MAX 0
                       ?do      dup 1 TYPE      \ top side of box
                        loop    drop
                else    bx ty at
                then
                boxchars 2+ 1 TYPE              \ top right corner
                tx by at
                boxchars 5 + 1 TYPE             \ bottom left corner
                boxchars 6 +
                bx tx - 1- 0MAX 0
               ?do      dup 1 TYPE              \ bottom side of box
                loop    drop
                boxchars 7 + 1 TYPE             \ bottom right corner
                boxchars 3 +                    \ left and right box chars
                by ty 1+ over min
               ?do      tx i at dup 1 TYPE      \ left side of box
                        fbx                     \ fill the box?
                        if      bx tx - 1- spaces
                        then
                        bx i at dup 1+ 1 TYPE   \ right side of box
                loop    drop
                tx 1+ ty 1+ dup =: bline at ;

forth definitions

: >box          ( --- )
                " ÚÄÜ³ÛÀÜÛ" drop !> boxchars ;

: >menu         ( --- )
                " ³ Û³ÛÀÜÛ" drop !> boxchars ;

: bcr           ( --- )         \ box carraige return, to start of next line
                incr> bline
                tx 1+ bline at ;

: box           ( left top right bottom --- )
                >box false <box> ;

: box&fill      ( left top right bottom --- )
                >box true <box> ;

: (.box)        ( x y --- )
                >box X>"BUF
                >r over r@ c@ + 3 + over 2+ box
                space r> count type space
                bx by 1+ at ;

: .box"         ( x y | text --- )
                compile (.box) X," ; immediate

: menubox       ( left top right bottom --- )
                >menu false <box> ;

only forth also definitions

\s
: boxsample     ( --- )
                20 2
                do      i 2* i .box" Ziping along!"
                loop
                10 10 .box" hello this is a sample of BOXED TEXT"
                20 15 .box" Here is even more text!!" ;

boxsample

