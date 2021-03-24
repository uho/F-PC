\ SMENU.SEQ             A sample menu file              by Tom Zimmer

only forth also definitions hidden also
\       ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
\       ³ This is an example of menu usage.               ³
\       ³ You can make a copy of this file and edit to    ³
\       ³ suit your needs.                                ³
\       ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

\ Install the functions into these defered words you want performed
\ when a menu item is selected.

defer item1
defer item2
defer item3
defer item4
defer item5
defer item6
defer item7
defer item8
defer item9

 0 value amsave                 \ a place to save the previous menu column
 0 value amline                 \ Menu starting line and column on screen
 0 value amcolumn

newmenu menu1$
        menuline"  A item1            Alt-1 " item1
        menuline"  B item2            Alt-2 " item2
        menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
        menuline"  C item3                  " item3
        menuline"  D item4            Alt-4 " item4
        menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
        menuline"  Quit item5           F10 " item5
endmenu

newmenu menu2$
        menuline"  E item6            Alt-6 " item6
        menuline"  F item7            Alt-7 " item7
        menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
        menuline"  G item8               F8 " item8
        menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
        menuline"  H item9               F9 " item9
endmenu

newmenubar amenubar +," A menu1 "         \ the menu bar contains only two items
                    +," B menu2 "
endmenu
create amenulist menu1$ ,               \ and two lists of functions
                 menu2$ ,

\ initialize the default condition of the menu bar

amenubar   =: menubar
amenulist  =: menulist
' amline   is mline
' amcolumn is mcolumn
' drop     is doother

\ defered routine to handle all keys the menu handler doesn't understand.
\ can be used to insert any unknown key pressed in an editor if you
\ want, or it can just throw the keys away as is done here. The
\ character the menu driver didn't understand is on the stack when
\ AOTHER is called.

defer aother            ' drop is aother

: amenu         ( --- )         \ the rolodex menu driver
                amsave =: mcol
                savemenu
                amenubar     =: menubar
                amenulist    =: menulist
                ['] amline   is mline
                ['] amcolumn is mcolumn
                ['] aother   is doother
                menu
                restmenu
                mcol =: amsave ;

\s
  This word AMENU is then called whenever a menu operation is to be
performed.  In an editing application, I normally use ESC to enable
the menu.  To do this you look for the user to press the ESC key
while typing in text, if the escape key is pressed then execute RMENU
rather than inserting the key pressed.

