\ NEWCOM.SEQ    A simple utility to allow you to make small COM files.

comment:

        This file gives you a way to make small COM files out of
        single CODE definitions. Simply place your code in the code
        definition as shown by the comments. When you laod this file
        the code will be automatically saved ina file TEST.COM as
        shown at the bottom of the file.

comment;

code test       ( --- )
                clear_labels
\
\ ********** Insert YOUR code here **********
\
\ Leave the following code at the end, it returns to DOS.
\
                mov ax, # 0
                mov dx, ax
                int $21
                end-code

' test here over -

: docom         ( Addr len | filename -- ) \ Save code from external segment.
                seqhandle+ !HCB
                seqhandle+ HDELETE   DROP
                seqhandle+ HCREATE   ABORT" Save Create ERR!"
                seqhandle+ HWRITE 0= ABORT" Save Write  ERR!"
                seqhandle+ HCLOSE    ABORT" Save Close  ERR!" ;

docom test.com          \ makes a small COM file.


