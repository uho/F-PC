\\

>bg
ledbutton
{

: tt    cr
        attrib c@ >R
        $100 0 do
                i 16 mod 0= if cr then
                i attrib !
                ." ABCD"
              loop
        r> attrib c!
        cr
        ;

: .colours ( -- )
        10 0 do at? ." this is a lot of text         " 1+ at  loop ;

: bigcolours ( bg fg -- ) \
        8 mod swap 8 mod
        2dup     >bg     >fg
         0  0 at .colours
        2dup 8 + >bg     >fg
        40  0 at .colours
        2dup     >bg 8 + >fg
         0 10 at .colours
        2dup 8 + >bg 8 + >fg
        40 10 at .colours
        2drop
        ;

0 value saved

: ttbutton ( --- ) \ colour view button handler
        mousebutton
        case
                2 of    saved if  restscr off> saved  then
                        endof
                1 of    saved if restscr then
                        savescr on> saved
                        cls
                        mousexy
                        3 /             \ y colour of 8 ( background )
                        swap
                        10 /            \ x colour of 8 ( foreground )
                        bigcolours
                        endof
                drop
        endcase
        ;

: t1 ( -- ) \ test colour combinations
        attrib c@ >R
        ['] ttbutton save!> dobutton   \ install new and save current
                                        \ mouse button driver
        cls
        8 0 do  cr
                8 0 do  j 16 * i     + attrib ! ." ABCD\0 "
                        attrib @ 128 + attrib ! ." ABCD\0³"     loop
                cr
                8 0 do  j 16 * i + 8 + attrib ! ." ABCD\0 "
                        attrib @ 128 + attrib ! ." ABCD\0³"     loop
                cr
              loop
        key drop
        restore> dobutton               \ restores previous button driver
        r> attrib c!
        ;


