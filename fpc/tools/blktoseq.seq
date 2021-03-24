\ BLKTOSEQ.SEQ  Conversion program, moves .BLK to .SEQ. by Tom Zimmer

handle cnvhcb                   \ Input file to convert
handle outhcb                   \ output file we are converting to.

create crlf 13 c, 10 c,         \ line terminator

create blockbuf 1024 allot      \ Block conversion buffer
       blockbuf 1024 erase

variable lastln
variable altoff                 \ Offset to shaddow or ALT screen.

: convblk       ( n1 --- )      \ convert a block to a sequence of lines.
                1024 um* ( 64. d+ ) cnvhcb movepointer  \ set input pointer
                blockbuf dup 1024 ( 64 - ) cnvhcb hread \ read a block
                over + swap
                do      \ i c@ ascii \ <>
                    ( if  ) i 64 -trailing dup >r
                        lastln @ over or 0=
                        if      2drop
                        else           outhcb hwrite drop
                                crlf 2 outhcb hwrite drop
                        then    r> lastln !
                    ( then )
            64 +loop    ;

: commentblk    ( n1 --- )      \ convert comment block and code block
                " comment:" outhcb hwrite drop
                     crlf 2 outhcb hwrite drop
                convblk
                " comment;" outhcb hwrite drop
                     crlf 2 outhcb hwrite drop ;

: convfile      ( --- )                 \ convert file from BLK to SEQ
                cnvhcb endfile 1024 um/mod swap
                if      1+              \ include end of last block.
                then    altoff off
                cnvhcb handle>ext " .BLK" caps-comp 0=  \ Is files ext .BLK?
                if      2/ dup altoff !                 \ then set shadow
                then    0                               \ offset to Alt scrn
                do      i convblk altoff @              \ if altoff <>0
                        if      i altoff @ + commentblk \ write comment scrn
                        then
                        ." ." ?cr
                loop ;

: conv          ( --- name )
                70 rmargin !                            \ set right margin
                cnvhcb !hcb cnvhcb 1+ c@ ascii . =      \ of null filename
                if      cr ." Name of file to convert? " \ prompt for filenam
                        query cnvhcb !hcb               \ stuff in cnvhcb
                then
                cnvhcb outhcb $>handle          \ duplicate in outhcb
                " SEQ" ">$ outhcb $>ext         \ and change ext to .SEQ
                cr ." Converting from " cnvhcb count type \ anounce conversn
                   ."  to " outhcb count type
                cnvhcb handle>ext " .SEQ" caps-comp 0=  \ check input <> .SEQ
                abort" Cannot convert .SEQ to .SEQ "    \ error if it is
                cr cnvhcb hopen abort" Open file error" \ open input file
                outhcb hcreate abort" Create ERROR!"    \ make output file
                0.0 outhcb movepointer                  \ reset file pointer
                lastln on
                convfile                                \ convert the file
                cnvhcb hclose drop                      \ close in
                outhcb hclose drop ;                    \ close out

only forth also definitions
