\ AUTOLOAD.SEQ    Automatically load from F-PC.CFG      by Tom Zimmer

comment:

        If you load this file and re-save the system, F-PC will
        automatically try to load the file F-PC.CFG each time F-PC starts
        up.  You can put some of your configuration stuff in that file,
        and it will take effect at each boot time.

coment;

: autoload      ( --- )
                " F-PC.CFG" ">$ $fload drop     \ we don't care if it exists
                defers default ;

' autoload is default

