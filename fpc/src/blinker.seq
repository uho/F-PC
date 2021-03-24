\ BLINKER.SEQ           Set blinkable state             Tom Zimmer
comment:
Normally colors 0 through 7 can be used interchangably in either forground
or background, and colors 8 to 15 can be used in forground, but will cause
the character to blink if used in the background.  This file switches off
the blink attribute, so colors 8 to 15 can be used in the background.

Also in thie file is a word to set the color palette on VGA type displays,
and words to get and set the border color.

comment;

code blink!     ( n1 -- )       \ set the background color blink state
                                \ n1=0=noblink, n1=<>0=blinkable background
                pop bx
                mov ax, # $1003
                int $10
                next    end-code

: blinkoff      ( -- )          \ disable blinking background color 8 - 15
                0 blink! ;

: blinkon       ( -- )          \ enable blinking background colors 8 - 15
                1 blink! ;

code pal!       ( color pallete -- )    \ set the color palette primitive
                                        \ See DOS technical manuals for
                                        \ more information.
                pop bx
                pop cx
                mov bh, cl
                mov ax, # $1000
                int $10
                next    end-code

code border!    ( n1 -- )               \ set border color to n1=0-15
                pop bx
                mov bh, bl
                mov ax, # $1001
                int $10
                next    end-code

code border@    ( -- n1 )               \ get the current border color
                                        \ VALID ON VGA ONLY
                mov ax, # $1008
                int $10
                sub ax, ax
                mov al, bh
                1push   end-code

\S *************************************************************************

\ code to save and restore the border color automatically on entry to and
\ exit from an F-PC application.

0 value borderprev

: bordersave    ( -- )                  \ save current border color for later
                defers initstuff
                border@ !> borderprev ;

' bordersave is initstuff

: borderrestore ( -- )                  \ restore the previous border color
                defers byefunc
                borderprev border! ;

' borderrestore is byefunc

