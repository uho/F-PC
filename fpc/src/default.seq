\ DEFAULT       Open file specified from command line.  by Tom Zimmer

\ Link this file into the FILELIST chain.

FILES DEFINITIONS

VARIABLE DEFAULT.SEQ

FORTH DEFINITIONS

128 CONSTANT DOS-LINE
CREATE CFGHNDL STRING,-T KERNEL.CFG" B/HCB ALLOT

: DOS>TIB       ( --- )
                dos-line count dup #tib ! tib swap cmove >in off ;

: HDEFAULT      ( -- )
                %off> ?loaded,          \ disable LOADED, this time ONLY
                cfghndl $fload drop     \ we don't care if it exists
                seqhandle clr-hcb dos-line c@ 0<>
                if      dos>tib                       \ move dosline to tib
                        bl word 1+ c@ ASCII - <>      \ is - in place of file
                        if      here    $file         \ can we open it?
                                if      >in off       \ if not just passon
                                then    loadline off
                        then            \ if first word is - then skip HOPEN
                then    ;               \ skip whole thing if dosline is null

' HDEFAULT IS DEFAULT

