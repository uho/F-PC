\ SMACRO.SEQ            An example of MACRO usage       by Tom Zimmer

comment:

  This file contains an example of how to use macros in the F-PC editor
to reduce the keystrokes required to perform a given task.

  The first example is a real one.  I decided to shorten the glossary
entries.  A typical entry is shown below for +! :


+!              ( n1 a1 --- )
                FORTH           KERNEL1
        Increment the value at a1 by n1.  This is equivalent to the
        following:   DUP @ ROT + SWAP ! but much faster.


Since all of the words in the glossary are in the  FORTH  vocabulary, I
decided to delete the word  FORTH .  Furthermore, I could then join the
second line containing the source file name with the first line, and do
a little more editing at the same time.  The desired new entry would be:


+!      ( n1 a1 --- )           KERNEL1
        Increment the value at a1 by n1.  This is equivalent to the
        following:   DUP @ ROT + SWAP ! but much faster.


The glossary is a very large file, and the manual editing required to
correct this would have been prohibitive, so I created a macro to join
the second line with the first line and remove the Vocabulary from each
entry when I press a single MACRO key.

  Here then is the key sequence that will make a macro to join the first
and second lines of the second type glossary entry and remove the Vocabulary
from the entry.  The macro will then go to the next paragraph which starts
the next entry.

  This macro is always executed when the cursor in on the first line of a
glossary entry at the left column, and we are in INSERT mode.

        Alt-M           Start a MACRO
        Alt-1           We are starting the macro for Alt-1
        INS             Select OVERWRITE mode
        TAB             Move to first TAB position
        Ctrl-T          Remove spaces before stack picture
        TAB             Move to 2nd TAB
        TAB             Move to 3rd TAB
        TAB             Move to 4th TAB
        INS             Go back into INSERT mode
        DEL             Delete the last char in the line forcing a join line
                        Cursor is now on the word FORTH
        Ctrl-T          Delete the Vocabulary
        HOME            Move back to beginning of line
        Alt-G_N         Goto Next paragraph
        Alt-M           Complete macro

  Macros are performed as they are created, so you must be on a glossary
entry that needs to be modified while you are in the process of making
the macro.

  In the following section I have included several copies of the +!
glossary entry.  Try making the macro described above while you are on the
first entry, then press Alt-1 to try the macro on each successive entry.

  Macros can be saved with Alt-M_S, and loaded again with Alt-M_L.  You can
view the currently defined macros with Alt-M_V, but they will look very
funny since they are mostly graphics characters.


----------------------  Try some macros here --------------------


+!              ( n1 a1 --- )
                FORTH           KERNEL1
        Increment the value at a1 by n1.  This is equivalent to the
        following:   DUP @ ROT + SWAP ! but much faster.

+!              ( n1 a1 --- )
                FORTH           KERNEL1
        Increment the value at a1 by n1.  This is equivalent to the
        following:   DUP @ ROT + SWAP ! but much faster.

+!              ( n1 a1 --- )
                FORTH           KERNEL1
        Increment the value at a1 by n1.  This is equivalent to the
        following:   DUP @ ROT + SWAP ! but much faster.

+!              ( n1 a1 --- )
                FORTH           KERNEL1
        Increment the value at a1 by n1.  This is equivalent to the
        following:   DUP @ ROT + SWAP ! but much faster.

+!              ( n1 a1 --- )
                FORTH           KERNEL1
        Increment the value at a1 by n1.  This is equivalent to the
        following:   DUP @ ROT + SWAP ! but much faster.

+!              ( n1 a1 --- )
                FORTH           KERNEL1
        Increment the value at a1 by n1.  This is equivalent to the
        following:   DUP @ ROT + SWAP ! but much faster.

HRENAME         ( handle1 handle2 --- return-code )
                FORTH           HANDLES
        Change the name of the file specified in handle1 to the name
        specified in handle2. Can be used to move a file from one
        directory to another on the same drive. Returns 18 if the
        rename was good, not zero.

HRENAME         ( handle1 handle2 --- return-code )
                FORTH           HANDLES
        Change the name of the file specified in handle1 to the name
        specified in handle2. Can be used to move a file from one
        directory to another on the same drive. Returns 18 if the
        rename was good, not zero.


  As you can see, having performed the macro on one of the last two
glossary entries for HRENAME, a macro will not always work as you expect.
If the information being manipulated does not follow the rules, you can get
quite unexpected results.  Use caution with macro, they can be wonderful
time savers, but it is difficult to make a macro that will always work.

  This is only the start for macros.  Experiment with some junk text to see
what other neat things they can do.

  When you leave this file you probably DO NOT want to save your changes.

  Press  Alt-F10  to leave SED without saving any changes.

comment;

cr .( Use LISTING to print this file then try editing it. )

