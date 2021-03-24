\ SWINDOW.SEQ           A sample POPUP window           by Tom Zimmer

comment:

  This file contains an example of yow to make a popup window.  While it
may seem complicated at first, we are really doing only a few simple things.
We save the screen positon, turn off the cursor, and save the screen before
drawing the box and writing into it. After the user views the box and
presses a key, we restore the things we saved.  Actally pretty simple.

comment;

: popup ( --- )
        #out @ #line @ 2>r      \ save cursor position for later
        cursor-off              \ remove cursor from screen
        savescr                 \ preserve current screen contents

        15 05 60 09 box&fill    \ DRAW THE BOX on the screen

                                \ PUT SOME TEXT IN IT
        ."        This is a sample POPUP window" bcr

                                \ following line in reverse video
   >rev ."    Screen attributes can be used as well    " >norm bcr

        ."  Press a key to restore the previous screen"

        key drop                \ wait for user to press a key
        restscr                 \ restore previous screen
        cursor-on               \ turn cursor back on
        2r> at ;                \ restore cursor position

