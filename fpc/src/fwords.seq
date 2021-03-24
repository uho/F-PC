\ FWORDS.SEQ    File searching                          by Tom Zimmer

comment:

  Some powerful file manipulation words are now being loaded, these
words allow printing, searching and listing the first line of
sequential files.  Here is a synopsis:

        FLOOK  <string> <filespec#1> <filespec#2> ...  to end of line
        INDEX  <filespec#1> <filespec#2> ...             "      "
        FPRINT <filespec#1> <filespec#2> ...             "      "

  Each of the words may be followed by as many filespecs as will fit on
a line.  The filespecs will be precessed left to right.  Filespecs can
be "*.*", or "*.SEQ", or "ANYFILE", or any other filespec you want.  It
is probably not a good idea to use these words on .EXE or .COM files
though.

  Here is an example of how FLOOK might be used:

        FLOOK <string> F-PC COLOR STATUS <enter>

will search the files F-PC.SEQ, COLOR.SEQ, and STATUS.SEQ for <string>

comment;

                only
                forth  also
\u editor       editor also
                hidden also definitions

defined slook.buf nip 0= #if    \ if SLOOK.BUF doesn't exist, define it

create slook.buf 36 allot

#endif

defer donfile           \ A function to do on all specified files

' noop is donfile

variable noise
2variable bytes_srch
  0 value files_srch
  0 value occur_srch

headerless

  0 value +a.?                  \ plus a dot?

variable fstime

: .file-once    ( --- )
                fstime @ 0=
                if      cr .seqhandle fstime on
                then    ;

code searchsetup ( --- a1 n1 a2 n2 )
                mov bx, # slook.buf 1+          \ slook.buf count
                push bx
                mov al, slook.buf byte
                sub ah, ah
                push ax
                mov bx, # outbuf 1+             \ outbuf count
                push bx
                mov al, outbuf byte
                1push
                end-code

: searchfile    ( --- )
                IBRESET
                0.0 seqhandle movepointer
                0.0 filepointer 2!
                off> fstime
                @> noise if ." ." ?cr then
                20000 1
                do      lineread c@ 0= ?leave
                        searchsetup search nip
                        if      @> noise
                                if      .file-once
                                        cr i 3 .r space
                                else    cr
                                then    outbuf count 2- type
                                incr> occur_srch
                                ?keypause
                                PRINTING @ 0= @> statv and
                                IF <.STAT> THEN
                        then
                loop    @> fstime if cr then ;

defined reedit nip #if          \ ONLY if REEDIT is defined

: searchedit    ( --- )
                [ forth ]
                IBRESET
                0.0 seqhandle movepointer
                0.0 filepointer 2!
                ." ." ?cr
                off> newbrowse
                off> ?browse
                off> seding
                on> leavenow
                20000 1
                do      i 127 and 0= if ?keypause then
                        lineread c@ 0= ?leave
                        searchsetup search nip
                        if      i =: loadline
                                savecursor
                                savescr
                                <ed>
                                restscr
                                restcursor
                                leave
                        then
                loop
                off> leavenow ;

#endif

variable withname

: .firstline    ( --- )
                IBRESET
                0.0 seqhandle movepointer
                0.0 filepointer 2!
                cr lineread count 2- 0MAX withname @
                if      .seqhandle 20 #out @ - spaces
                        60
                else    79
                then    min type
                ?keypause ;

headers

forth definitions

: fallof        ( func | file_specs --- )       \ Do something to all files
                                                  \ matching file_specs.
                is donfile              \ Set function to be performed.
                0.0  bytes_srch 2!
                0 =: files_srch
                dirseg 0= if #tib @ >in ! exit then
                begin   >in @ #tib @ <
                while   bl word         \ else get the file spec
                        dup count + 1- c@ '.' = =: +a.?
                        dup
                        $getdir              \ and read the directory files.
                        #fls 0=
                        if      cr count type ."  No matching files."
                        else    drop    #fls 0
                                ?do     i >fadr 1+ c@l '.' <>
                                        if      i >fadr dir>pad >r
                                                here seqhandle+ $>handle
                                                seqhandle+ >pathend
                                                dup seqhandle+ 1+ - r@ +
                                                seqhandle+ c!
                                                r> cmove
                                                +a.?    \ add a dot
                                                if      '.'
                                                        seqhandle+ count + c!
                                                        1 seqhandle+ c+!
                                                then
                                                seqhandle+  count + off
                                                seqhandle+  $hopen 0=
                                                if      seqhandle endfile
                                                              bytes_srch D+!
                                                        incr> files_srch
                                                        PRINTING @ 0=
                                                        @> statv and
                                                        IF      <.STAT>
                                                        THEN    donfile
                                                then    close   ?keypause
                                        then
                                loop
                        then
                repeat  cr ;

I: ?in-empty    ( --- f1 )              \ is anything left in input stream?
                >in @ bl word c@ 0= swap >in ! ;

: get-filespecs ( --- )
                ?in-empty               \ if nothing following command
                if      cr ." File spec(s) to search [*.seq] ->"
                        query
                        ?in-empty       \ if nothing following then
                        if      " *.seq" ">$ $>tib \ substitute "*.seq"
                        then
                then    ;

: flook         ( search_string file_specs --- ) \ Search files for string
                SAVESTATE noise on
                off> occur_srch
                ?in-empty               \ if nothing following command
                if      cr ." String to LOOK for     ->" query 0 word
                else    bl word
                then    count 29 min slook.buf place
                get-filespecs ['] searchfile fallof
                RESTORESTATE
                cr files_srch .     ." Files searched, "
                   bytes_srch 2@ d. ." Total bytes searched, "
                   occur_srch u.    ." Occurances found." ;

defined reedit nip #if          \ ONLY if REEDIT is defined

: editall       ( search_string file_specs --- ) \ edit all files containing
                SAVESTATE
                ?in-empty               \ if nothing following command
                if      cr ." String to LOOK for and EDIT ->" query 0 word
                else    bl word
                then    count 29 min slook.buf place
                get-filespecs ['] searchedit fallof
                RESTORESTATE ;

#endif

: index         ( file_spec --- )       \ Print first line of files
                SAVESTATE
                ." \n\n**** Use SPACE to pause, and ESC to stop. ****\n\:03"
                withname on
                ?in-empty               \ if nothing following command
                if      " *.seq" ">$ $>tib \ substitute "*.seq"
                        withname off
                then    ['] .firstline fallof
                RESTORESTATE ;

defined listing nip #if         \ load only if LISTING is loaded

: fprint        ( file_specs --- )      \ Print files specified.
                ?printer.ready 0= if  cr .offline quit then
                SAVESTATE
                more? 0=       \ if nothing following command
                if      cr ." File spec(s) to print ->" query
                then
                on> ?listing
                ?in-empty 0=
                if      ['] listing fallof
                else    ." No file(s) specified to print."
                then
                off> ?listing
                RESTORESTATE ;

#endif

only forth also definitions

