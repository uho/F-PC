\ SEQTOBLK.SEQ  Convert from Sequential file to Blocks. by Tom Zimmer


: ?writerror    ( f1 --- )
                if      cr ." Error Writing to " seqhandle+ count type
                        seqhandle+ hclose drop
                then    ;

: transblk      ( --- F1 )
                ascii \ pad c!
                pad 64 seqhandle+ hwrite 64 - ?writerror
                14 0
                do      lineread dup count 2- 64 min 0 max + 64 blank
                        1+ 64 seqhandle+ hwrite 64 - ?writerror
                loop    pad 1+ 64 seqhandle+ hwrite 64 - ?writerror
                        ." ." outbuf c@ 0= ;

: conv          ( --- )
                cr ." Only converts files with extension .SEQ"
                cr ." New files are created with a .SCR extension."
                cr ." DONOT specify the file extension."
                cr ." Filename to convert? "
                query bl word $hopen abort" Couldn't Open file!"
                seqhandle seqhandle+ $>handle
                " SCR" ">$ seqhandle+ $>ext
                seqhandle+ hcreate abort" Couldn't Create the .SCR file."
                pad 1024 blank
                pad 1024 seqhandle+ hwrite 1024 - ?writerror
                off> INSTART
                off> INLENGTH
                begin   transblk
                until   seqhandle+ hclose drop
                        seqhandle hclose drop ;



