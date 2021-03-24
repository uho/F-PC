\ DOSIO.SEQ     DOS keyboard drivers                    by Tom Zimmer

comment:

  This file allows the use of file re-direction into forth, so a file
can contain a bunch of lines of Forth commands.  Forth will automatically
terminate when it sees the Control-Z at the end of the file.

  The normal form is as follows:

        PROGRAM_NAME <Input_file >Output_file  return

  Another form allows direct Forth commands on the command line:

        PROGRAM_NAME forth_words >Output_file  return

  Where the output is directed to the Output_file.

  You cannot however pass forth_words into PROGRAM_NAME, and at the
same time pass in Input_file.  Input_file will then be ignored.

  Load this file, and save the resulting system using FSAVE.

comment;

: bdoskey       ( --- C1 )
                0 7 bdos dup control Z =
                if      bye             \ If Control-Z, then leave.
                then    ;

' false         is key?                 \ Re-defer terminal input to DOS.
' bdoskey       is key

: interpx       ( --- )                 \ new INTERPRET to ignore LF
                tib c@ control J =
                if      bl tib c!       \ mask LF to blank
                then    defers interpret ;

: dosdefault    ( --- )                 \ allows auto leave after execution
                dos>tib
                >in @ bl word swap >in ! c@
                if      interpret
                        bye
                then    ;

: dosbye        ( --- )                 \ eliminates the "Goodaye" message
                restore_vectors
                0 0 bdos ;

' interpx       is interpret            \ Filters out line feeds
' noop          is .hello               \ Quiet .HELLO
' noop          is .curfile             \ Quiet .CURFILE
' noop          is dark                 \ Disable screen clear
' dosdefault    is default              \ Enable auto leave after interpret
' dosbye        is byefunc              \ Quiet BYE

slow                                    \ Select terminal output to DOS.
statoff                                 \ Turn OFF status display

cr .( You can now save this DOS re-directable system with FSAVE. ) cr

