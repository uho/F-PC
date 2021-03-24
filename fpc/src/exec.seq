\ EXEC.SEQ      A utility for calling DOS from Forth.   by Tom Zimmer

only forth also hidden also definitions

: emm-initstuff ( -- )
                defers initstuff
                emm-init ;

' emm-initstuff is initstuff

: fpc>emm       ( --- )      \ move FPC to expanded memory
                emm-status @ ?exit              \ leave if no EMM available
                0 cur_emm_hndl !                        \ reset current hndl
                ?cs: emmpars @ + emmparst !             \ set start of saved
                #pars @ emmpars @ - u2/ $200 / 1+       \ calc pages needed
                #fpcpages !                             \ set pages needed
                emm-avail-pages emm-status @ ?exit      \ get available EMM
                #fpcpages @ <                           \ if not enough memory
                if      emm-status on exit
                then
                #fpcpages @ emm-alloc-pages emmhndl !   \ allocate pages
                emmhndl @ cur_emm_hndl !                \ make current
                emm-status @ ?exit                      \ leave if error
                #fpcpages @ 0                           \ move FPC to EMM
                ?do     i 0 emmhndl @ emm-map-pages     \ get page
                        emm-status @ ?leave             \ leave on error
                        emmparst @ I $400 * + 0         \ source of move
                        ?emm: 0 16384 cmovel            \ move the page to EMM
                loop    ;

\ the handle EXTHNDL is located in the file EMMEXEC.SEQ

: fpc>disk      ( --- )      \ move FPC to a disk file
                true dsk-status !                       \ init to failed
                ['] 0= save!> pathset                   \ disable pathset
                exthndl hcreate                         \ make the file
                restore> pathset                        \ restore it
                ?exit                                   \ leave if error
                ?cs: emmpars @ + emmparst !             \ set start of saved
                #pars @ emmpars @ - u2/ $200 / 1+       \ calc pages needed
                #fpcpages !                             \ set pages needed
                dsk-status off                          \ reset error status
                #fpcpages @ 0                           \ move FPC to EMM
                ?do     dsk-status @ ?leave             \ leave on error
                        0 16384                         \ source addr & len
                        exthndl                         \ file handle
                        emmparst @ I $400 * +           \ source seg of move
                        exhwrite                        \ move page to file
                        16384 - dsk-status !            \ set status
                loop    dsk-status @                    \ if error occured
                if      exthndl hdelete drop            \ then delete file
                then    ;

: fpc>out       ( -- )          \ try to flush F-PC out of memory
                emm-present? use-emm @ and      \ use EMM?
                if      fpc>emm                 \ then try to use it
                else    emm-status on           \ else disable it
                then
                emm-status @ use-disk @ and     \ emm bad & disk turned on?
                if      fpc>disk                \ then try to use it
                else    dsk-status on           \ else disable it
                then    ;

: initcmdpath   ( --- )         \ Initialize the Command path
                defers initstuff
                comspec@ comspec$ cmdpath $>handle ;

' initcmdpath is initstuff      \ Put into initialization chain.

: $sys          ( countedstring --- f1 ) \ spawn a shell
                emmsysend paragraph emmpars !  \ set needed paragraphs
                exec$      $100 erase
                exec.param $10  erase
                dup c@
        if      count tuck exec$ 4 + swap cmove
                " /c " exec$ 1+ swap cmove
                3 + exec$ c! exec$ count + off
        else    drop exec$ off
        then    44 @    exec.param      !       \ environment segmnt
                ?cs:    exec.param  4 + !       \ command line seg
                exec$   exec.param  2 + !       \ and offset
                $0D exec$ count + c!            \ append a carraige return
                FPC>OUT                         \ copy FPC to expanded mem
                RESTORE_VECTORS                 \ restore interrupt vectors
                <extexec>                       \ actually do the system call
                SET_VECTORS                     \ recapture interrupt vectors
                SETCRITICAL                     \ reset critical interrupt
                cursor_pos_init                 \ restore cursor position
\u blinkoff     blinkoff                        \ disable background blink
                ;

: ?syserror     ( n1 --- )      \ handle ONLY error codes 2 and 8 from $sys
                dup  2 = abort" Can't find COMMAND.COM"
                dup  8 = abort" Not enough memory"
                drop ;

defer clearmem  ' noop is clearmem

: nd$sys        ( countedstring -- f1 )         \ shell with no disksave
                save> use-disk diskoff                  \ don't save to disk
                $sys
                restore> use-disk                       \ restore state of
                use-disk @                              \ disk save flag
                if      diskon                          \ and select mode
                then    ;

forth definitions

: "swapfile     ( a1 n1 -- )            \ set the disk swapfile for $SYS
                dup 0=
                if      2drop
                        " FPCIMAGE.$$$"                 \ set to default name
                then    exthndl ">handle                \ set drive & file
                diskon ;                                \ enable disk save

: swapfile      ( | name -- )
                bl word count "swapfile ;

comment:

The SYS word relys on a string compiled in the handle CMDPATH, to
contain the name and path to COMMAND.COM. For SYS to work, this string
must specify the actual location of COMMAND.COM on your hard disk,
or floppy. The drive may be omitted, which will cause SYS to look on
the current drive.

comment;

: sys           ( command --- )
                clearmem
                0  word cr $sys ?syserror ;

' SYS ALIAS `   ( command --- )

: ``            ( command --- )
                clearmem
                0  word cr nd$sys ?syserror ;

hidden definitions

: cmdbuf        rp0 @ 100 - ;           \ Down from return stack,
                                        \ yet above TIB.

: "syscommand   ( a1 n1 c1 --- )        \ pass string a1,n1 to dos with line
                                        \ following appended to it.
                clearmem
                >r ">$ cmdbuf over c@ 1+ cmove
                r> word count
                dup>r cmdbuf count + swap cmove
                r> cmdbuf c@ + cmdbuf c!
                cmdbuf count + off
                cmdbuf nd$sys ?syserror ;

: dir.name      ( --- )
                16 save!> tabsize
                #OUT @ 64 > IF CR THEN
                #out @ >r pad 30 + 12 bounds
                do      i c@ ?dup
                        if emit else leave then
                loop    10 #out @ r> - - spaces
                pad 21 + c@ 16 and
                if      ." <DIR>"
                then    tab restore> tabsize ;

: $dir          ( a1 --- )
                here over c@ 1+ cmove
                here pathset drop
                ."  For directory " here count type
                here count + off here 1+
                CR  PAD SET-DTA $23 find-first  \ $23=find any files
                BEGIN   255 and 0=
                WHILE   dir.name find-next REPEAT  ;

forth definitions

: dir           ( <filespec> --- )      \ directory of <filespec>.
                " dir " 0 "syscommand ;

: del           ( <filespec> --- )      \ delete files
                " del " bl "syscommand ;

\ ' del alias delete

: chdir         ( <filespec> --- )      \ change directory
                " chdir " bl "syscommand seqhandle >hndle @ 0<
                IF      seqhandle dup clr-hcb pathset drop
                        -2 seqhandle >hndle !
                THEN    ;

' chdir alias cd        \ Watch OUT, this is also a HEX number.

: copy          ( <filespec> --- )      \ copy files
                " copy " 0 "syscommand ;

: ren           ( <filespec> --- )      \ rename files
                " ren " 0 "syscommand ;

' ren  alias rename
' dark alias cls

comment:

: "setdrive     ( a1 n1 --- )           \ set drive a as default drive.
                clearmem
                ">$ nd$sys ?syserror
                seqhandle >hndle @ -2 =
                if      -1 seqhandle >hndle !
                then    ;

: a:            ( --- )                 \ set drive b as default drive.
                " a:" "setdrive ;

: b:            ( --- )                 \ set drive b as default drive.
                " b:" "setdrive ;

: c:            ( --- )                 \ set drive c as default drive.
                " c:" "setdrive ;

comment;

                \ Here are some additional system commands you can
                  \ add if you need them. Just un-comment: them out.
comment:

: rd            ( <filespec> --- )      \ remove directory
                " rd " bl "syscommand ;

' rd alias rmdir

: md            ( <filespec> --- )      \ make directory
                " md " bl "syscommand ;

' md alias mkdir

: format        ( <drivespec> --- )     \ format disk
                " format " bl "syscommand ;

: ftype         ( <filespec> --- )      \ type a file
                " type " bl "syscommand ;

: path          ( <pathspec> --- )      \ gt or set search path
                " path " bl "syscommand ;

: cls           ( --- )
                " cls " bl "syscommand ;

comment;

only forth also definitions


