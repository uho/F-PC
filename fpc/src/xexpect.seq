\ XEXPECT.SEQ   A version of EXPECT that allows line editing  by Tom Zimmer

comment:

        This little utility allows you to use the line editors control
        key sequences to edit the Forth command line.  If you make an
        error while typing, you can recover the previously entered line
        by pressing ESC. Terminate the entry with the <Enter> key.

        You can also recover previously entered Forth command lines up
        to 4, by pressing Up or Down arrow to step through the command
        line list.

        This utility adds about 1k to the system.

comment;

only forth also hidden also definitions

headerless

 12 constant xmax       \ number of command lines to stack.
133 constant cmdlen     \ length of one command line.
  0    value save-get1? \ first time we press down arrow this is 0.

cmdlen xmax 1+ * s>d pointer xbseg      \ extended expect buffer segment

create xtmp cmdlen allot
0 value xbuf#

: xbinit        ( --- )
                xbseg 0=
                if      cr ." Couldn't allocate command line save buffer."
                        abort
                then
                xbseg 0 cmdlen xmax 1+ * 0 LFILL ;

xbinit          \ Allocate some space so we can use line edit now.

: xbuf_init     ( --- )
                defers initstuff
                xbinit ;

' xbuf_init is initstuff

: xbuf#-        ( --- )
                xbuf# 1- 0<
                if      xmax =: xbuf#
                else    decr> xbuf#
                then    ;

: xbuf#+        ( --- )
                xbuf# 1+ xmax u>
                if      off> xbuf#
                else    incr> xbuf#
                then    ;

: >xbuf         ( --- a1 )
                xbuf# xmax min 0MAX cmdlen * ;

: save-get      ( a1 --- )      \ a1 = CFA of buf inc or dec word
                >r
                editbuf c@
                if      ?cs: editbuf dup>r xbseg >xbuf r> c@ 1+ cmovel
                then    r> execute
                xbseg >xbuf 2dup c@l >r ?cs: editbuf r> 1+ cmovel
                off> ecursor .eline
                on> autoclear
                on> save-get1? ;

: xup           ( --- )
                ['] xbuf#- save-get ;

: xdown         ( --- )
                save-get1?
                if      ['] xbuf#+
                else    ['] noop
                then    save-get ;

: xexpect       ( a1 n1 --- )
                xbseg printing @ 0= and         \ use old expect if printing
        if      off> save-get1?
                ['] xup     save!> doup
                ['] xdown   save!> dodown
                ['] defmenu save!> equit
                ['] >norm   save!> >edattrib
                            save>  keysfuncptr
                >keys1
                xbuf#+
                xtmp off
                on> autoclear           \ clear line if first char is letter
                off> stripping_bl's     \ don't strip trailing spaces
                swap >r >r              \ save destination address under
                off> ecursor
                insertmode off
                #out @ #line @ xtmp r> <ledit> drop
                r>                      \ recover destination address
                doend .ecursor
                xtmp c@
                if      ?cs: xtmp dup>r xbseg >xbuf r> c@ 1+ cmovel
                then    ( a1 --- )      \ a1 is the address passed to EXPECT.
                xtmp count >r swap r@ cmove   \ move the line to TIB
                R> span ! space
                restore> keysfuncptr
                restore> >edattrib
                restore> equit
                restore> dodown
                restore> doup
        else    (expect)
        then    ;

' xexpect is expect

only forth also definitions

headers

