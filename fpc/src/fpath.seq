\ FPATH.SEQ     Allow easy open/load of files from OTHER directories

\ Link this file into the FILELIST chain.

FILES DEFINITIONS

VARIABLE FPATH.SEQ

FORTH DEFINITIONS

comment:

        This file allows F-PC to keep its system source files in a
        different directory from your work directory. It also allows you
        to specify 2 library directories for other utilities etc.


comment;

CREATE FPATH$     160 ALLOT     \ Room for a LOO..NG path.
CREATE PATHHNDL B/HCB ALLOT     \ A temporary handle
0 VALUE PATHPTR                 \ A pointer into the string of paths
0 VALUE PATHLEN

: PATH1         ( --- A1 )      \ return the first path, always current dir
                fpath$ count %!> pathlen        \ and length
                             %!> pathptr        \ Reset the path pointer
                pathhndl dup>r clr-hcb          \ clear out the handle
                r@ pathset drop r> ;            \ install current directory

: NPATH         ( --- A1 F1 )           \ f1 = true if end of list
                pathptr pathlen                 \ a1 n1
                2dup ASCII ; scan               \ a1 n1 a2 n2
                1- 0MAX %!> pathlen      \ set new length
                dup 1+ %!> pathptr              \ and pointer
                nip over - dup pathhndl c!      \ set handle length
                pathhndl 1+ swap cmove          \ move the data
                pathhndl c@ >r
                pathhndl count + 1- c@ ASCII \ <>
                if      ASCII \ pathhndl count + c!
                        1 pathhndl c+!
                then    pathhndl r> 0= ;

: SKIP.BLANKS   ( --- )
                source >in @ /string tuck bl skip nip - >in +! ;

: "FPATH+       ( a1 n1 -- )            \ append a path to current path
                fpath$ c@ 159 <         \ max length less than 159 chars
                if      fpath$ c@ 0>
                        if      " ;" fpath$ +place      \ add a semicolon
                        then
                        2dup -trailing                  \ remove trailing bls
                        2dup + 1- c@ ascii \ = +        \ remove trailing '\'
                        fpath$ +place                   \ add to path
                then    2drop ;

: <FPATH+>      ( | <path> --- f1 )     \ f1 = true if null string
                skip.blanks
                ASCII ; word c@          \ we get a word
                if      here count "fpath+
                then    here c@ 0= ;

: FPATH+        ( | <paths-string> --- )
                %save>  #tib
                >in @   bl word drop >in @ #tib ! >in !
                begin   <fpath+>
                until   %restore> #tib ;

                \ Set the current library path for LIBOPEN and LIBLOAD
: FPATH         ( | <path-string> --- )
                fpath$ off              \ initialize the path string
                fpath+ ;

                \ display the current library path
: .FPATH        ( --- )
                fpath$ count type space ;

                \ prepend the path in hndl to name at a1
: PREPEND.APATH ( a1 hndl --- a1 )
                dup c@ >r                       \ save length of handle
                swap                            \ bring name to top
                dup  2+ c@ ASCII : =            \ do we already have a drive?
                over 1+ c@ ASCII \ = or         \ or a path specified?
                r> 0= or 0=                     \ or handle is empty
                                                \ skip prepend if we do
             if dup c@ >r >r                    \ save name location & length
                r@ 1+ over c@ r@ + 1+ r@ c@ cmove> \ make room for path
                dup r@ over c@ 1+ cmove         \ move in path
                r> r> over c+!                  \ correct count
             then  nip ;

: ?OPEN.ERROR   ( f1 --- )
                abort" Open Error!" ;

CREATE $FILE_BUF 64 ALLOT

: <$FILE>         ( a1 --- f1 )         \ f1 = true if failed to open
                dup>r count $file_buf place
                $file_buf path1 prepend.apath
                $hopen  dup
                if      0=
                        begin   r@ count $file_buf place
                                npath   0=
                                if      $file_buf swap prepend.apath
                                        $hopen 0=
                                else    drop 0= true
                                then
                        until
                then    dup
                        \ if we can't open, then replace original file
                        \ in case we are going to be creating the file
                if      drop
                        r@ count $file_buf place
                        $file_buf path1 prepend.apath
                        $hopen
                then    r>drop ;

: $FILE         ( A1 --- F1 )
                <$FILE>
                0.0 seqhandle movepointer       \ reset to beginning of file
                0.0 filepointer 2!
                loadline off                    \ reset file offset
                ibreset
                0 %!> screenchar ;              \ --- f1

                \ Open a specified filename from ANY FPATH directory
: FILE          ( | <filename> --- )
                gfl bl word $file ?open.error
                ." of " seqhandle endfile d. ." bytes."
                0 0 seqhandle movepointer ;     \ reset to biginning of file

: $FLOAD        ( a1 --- f1 )           \ load the counted string file a1,
                                        \ return F1=TRUE if file DID NOT OPEN
                sequp                           \ step to next file in stack
                filepointer 2@                  \ where next lineread will be
                outbuf c@ 0 d- 2>r              \ backup to start of this line
                                                \ so we can restore it later
                <$file> dup>r 0=                \ try to open the file
                if      <fload>                 \ it opened, load it
                then    r>                      \ -- f1
                2r> filepointer 2!              \ restore previous file ptr
                seqdown ;                       \ and force a re-read of
                                                \ the previous file line

: FLOAD         ( | <filename> --- )
                bl word                         \ get filename
                $fload  ?open.error ;           \ perform the load

