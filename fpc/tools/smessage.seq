\ SMESSAGE.SEQ          Sample MY Startup Message       by Tom Zimmer

comment:

  This file contains an example of how to change the signon message
to display a user defined message in place of the default system
message.

comment;

: myhello       ( --- )
                ." Hello!"  ;   \ here is the message

' myhello is .hello             \ install new hello function into .HELLO

\ We will also disable the displaying of the current file at boot time

' noop is .curfile

