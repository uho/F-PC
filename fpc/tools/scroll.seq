\ SCROLL.SEQ    A sub screen scroll routine for F-PC    by Tom Zimmer

comment:

        Here are a couple of utility routines that you might find
        useful, they allow you to specify a window area of the screen
        to be scrolled up or down by one line.

        All parameters are ZERO based, that is the top left corner of the
        screen is 0,0 and the lower right of the screen is 79,24.  The
        attribute of the blank area scrolled in is controlled by the
        variable ATTRIB with the words >ATTRIB1 ect.

        EXAMPLE:

                0 12 79 24 SCROLL-UP

        This will scroll the lower half of the screen up one line.


comment;

CODE SCROLL-UP  ( left upper right lower --- )  \ scroll window up one line
                pop cx
                pop dx                  \ dl = right column
                mov dh, cl              \ dh = lower row
                pop ax
                pop cx                  \ cl = left column
                mov ch, al              \ ch = upper row
                mov bh, attrib          \ filler attribute
                mov ax, # $0601         \ 06 = scroll, 01 = one line
                int $10
                next   end-code

CODE SCROLL-DN  ( left upper right lower --- )  \ scroll window down one line
                pop cx
                pop dx                  \ dl = right column
                mov dh, cl              \ dh = lower row
                pop ax
                pop cx                  \ cl = left column
                mov ch, al              \ ch = upper row
                mov bh, attrib          \ filler attribute
                mov ax, # $0701         \ 06 = scroll, 01 = one line
                int $10
                next   end-code

