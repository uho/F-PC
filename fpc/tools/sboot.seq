\ SBOOT.SEQ             Sample User BOOT                by Tom Zimmer

comment:

 This file contains an example of how to make F-PC perform your own
function in place of starting F-PC and just running Forth.

  Before describing how to modify F-PC, I will present a description of
the things F-PC does at BOOT time so you can have a better understanding
of the startup process.

  At BOOT time F-PC proceeds through the following sequence:

        ----------------------------------------------------

      : COLD

{defered}       SETSEG
                        Setup Segments
                        Verify DOS > 2
                        Adjust memory allocation
                        Seg cursor position
                VMODE.SET
                        test Video mode
                        Adjust display attributes
{defered}                       >COLOR
                                or
{defered}                       >MONO
                        Set the Video segment
{defered chain} INITSTUFF
                        Various initializations, performed in a chain
                        through this DEFERed word. Things like:
                                MACROS
                                EDITOR
                                SAVE SCREEN
                                READ BUFFER SIZE
                                FILE SYSTEM
                                DIRECTORY BUFFER etc.
{defered}       BOOT
                        HELLO
                                Initialize TIB
                                Initialize VOCABULARY to FORTH
{defered chain}             *** DEFAULT ***
                                        HDEFAULT
                                                Move command line to TIB
                                                OPEN a file if available
{defered}                               .HELLO
                                                Display signon message
{defered}                               .CURFILE
                                                Display current file
{defered}                               INTERPRET
                                                Process command line
                QUIT ;

        ----------------------------------------------------

  As you can see from the above sequence, there are several places you can
modify what F-PC does at BOOT time. A substantial portion of the above
process MUST be done, and so we want to be careful to tie in at the right
point.  In general eveything before BOOT must be done for F-PC to operate
properly. If we tie in at BOOT though we will have to do some additional
initialization to specifically much of the stuff done by HELLO.  If we tie
in at DEFAULT, we usually still want the DOS command line to be moved to
TIB, and probably still want a file to be OPENed, so my normal choice
would be to use DEFERS to link into DEFAULT without eliminating the
function that is already there.  This will then turn DEFAULT into a
DEFERed chain.  If we want to in addition have our own signon message, we
can replace the functions in .HELLO and/or .CURFILE although a user
application need not return from the chained DEFAULT, and as such .HELLO
will never get performed.  Here then without further delay is MYDEFAULT.

comment;

: mydefault     ( --- )
                defers default          \ link into default as additional
                                        \ stuff to be done.

\ Your program stuff goes here. *****************************

                cr ." This is my program "
                cr ." Press a key to terminate "
                key drop                \ wait for a key before leaving
                cr

\ This ends your program, so leave. *************************

                bye ;                   \ got it so leave

' mydefault is default

\ Now save the system back to disk with the name MYBOOT.EXE.

FSAVE MYBOOT.EXE

