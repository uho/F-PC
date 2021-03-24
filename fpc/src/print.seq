\ PRINT.SEQ     A simple utility to allow printing to a disk file.

comment:

 This utility allow all printing to be sent to a disk file rather than the
printer. Use it after loading as follows:

        PFILE MYPRNT.PRN <Enter>        \ select a file to print to
        PRINT WORDS <Enter>             \ print a list of words
        PCLOSE <Enter>                  \ close the print file

  Additional PRINT statments issued after a PFILE, and before a PCLOSE will
be appended to the same print file.

comment;

: pclose        ( --- )         \ Restore printing to the printer.
                ['] 0= save!> pathset           \ disable path tool
                prnhndl hclose drop
                " PRN." prnhndl ">handle
                write-only prnhndl hopen drop
                restore> pathset                \ restore path tool
                ['] <?ptr.ready> is ?printer.ready ;

' pclose alias toprinter

                                \ a1 = addr of filename to make & print into
                                \       a1 is a counted string.
: $pfile        ( a1 --- f1 )   \ f1 = true if failed to make file else false
                ['] 0= save!> pathset           \ disable path tool
                prnhndl hclose drop
                0 save!> defext                 \ no default extention
                ( a1 --- ) prnhndl $>handle
                restore> defext                 \ re-enable default ext.
                prnhndl hcreate
                restore> pathset                \ restore path tool
                if      pclose true
                else    ['] true is ?printer.ready false
                then    ;

: pfile         ( | name --- )  \ Print is to goto diskfile "name"
                bl word $pfile abort" Failed to CREATE file." ;

' pfile alias fileprint

