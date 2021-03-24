\\ NEXPECT.SEQ   A version of EXPECT that allows line editing

        From XEXPECT by Tom Zimmer, modified by Mike Mayo and
        subsequently again modified by Tom Zimmer.

        This utility allows you to use the line editors control
        key sequences to edit the Forth command line.  If you make an
        error while typing, you can select and recover a previously
        entered line by pressing UP or DOWN ARROW.  Complete the entry
        with the Enter key, or cancel with the ESC key.

        You can also recover previously entered Forth command lines up
        to 12, by pressing Up or Down arrow to step through the displayed
        command line list.

        This utility adds about 1.5k to the system.

{

           only forth  also
\+ editor       editor also             \ if editor present, include it
                hidden also definitions

\ if ?SHIFTKEY is not defined and available from the editor, then
\ define it here
\- ?shiftkey    : ?shiftkey ( -- f1 ) 0 $417 c@l $03 and 0<> ;

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

0 value nshown  \ flag to say that the lines are already shown on the screen

: ?save-editbuf ( -- )
        editbuf c@ 2 >
        if  ?cs: editbuf dup>r xbseg >xbuf r> c@ 1+ cmovel  then
        ;

2variable nxysave
0 value ninsert

: nquit         ( -- )
                restscr
                nxysave 2@ !> ey drop 0 !> ex   \ place editor on new line
                off> nshown
                ninsert dup !> insertmode
                if      big-cursor
                else    norm-cursor
                then
                ['] >norm   !> >edattrib
                ['] defmenu !> equit ;

: .nexpect-box  ( -- )
                insertmode @ !> ninsert
                at? nxysave 2! savescr
                0 rows xmax 1+ rows 3 - min - 3 -
                cols 1-  rows 2- box&fill
        3 rows 2- at ." \r Select/edit a command line "
        ." \1 Press \3\1 \3\1 to select,  \3sh-\1 \3sh-\1 to move \0"
                insertmode on big-cursor
                ['] >attrib3 !> >edattrib
                ['] nquit    !> equit ;

\ new save-get
: nsave-get ( -- )
        xmax 1+ rows 3 - min >r
        tx 1+ ty 1+ dup =: bline at
        r@ 0 do xbseg i cmdlen * countl typel cols 2- sp>col
                bcr                     \ show command lines buffer
             loop
        rows r> - 2- xbuf# +    \ row we are editing
        =: ey  1 =: ex          \ place editor on new line
        xbseg >xbuf 2dup c@l >r ?cs: editbuf r> 1+ cmovel
        off> ecursor
        ;

: xup ( -- )
    nshown if
        ?save-editbuf
        ?shiftkey if
                        xbseg >xbuf 2dup c@l >r ?cs: editbuf r> 1+ cmovel
                        xbseg >xbuf
                        xbuf#-
                        xbseg >xbuf 2dup c@l >r 2swap r> 1+ cmovel
                        ?cs: editbuf dup>r xbseg >xbuf r> c@ 1+ cmovel
                  else
                        xbuf#-
                  then
   else editbuf c@
        if      ?cs: editbuf 2dup c@l >r xbseg >xbuf r> 1+ cmovel
        then
        xbuf#-
        .nexpect-box
   then
        on> nshown
        nsave-get ;

: xdown ( -- )
    nshown if
        ?save-editbuf
        ?shiftkey if
                        xbseg >xbuf 2dup c@l >r ?cs: editbuf r> 1+ cmovel
                        xbseg >xbuf
                        xbuf#+
                        xbseg >xbuf 2dup c@l >r 2swap r> 1+ cmovel
                        ?cs: editbuf dup>r xbseg >xbuf r> c@ 1+ cmovel
                  else
                        xbuf#+
                  then
   else .nexpect-box
   then
        on> nshown
        nsave-get ;

: new-expect       ( a1 n1 --- )
                xbseg printing @ 0= and         \ use old expect if printing
        if      savescr
                savecursor
                ['] xup     save!> doup
                ['] xdown   save!> dodown
                ['] defmenu save!> equit
                ['] >norm   save!> >edattrib
                            save>  keysfuncptr
                >keys1
                xbuf#+
                xtmp off
                off> stripping_bl's     \ disable removal of trailing bl's
                off> autoclear          \ disable auto entry clearing
                swap >r >r              \ save destination address under
                off> ecursor
                insertmode off
                off> nshown
                #out @ #line @ xtmp r> <ledit>

                r>                      \ recover destination address

                swap  xtmp c@ 2 >  and  \ only store longer lines
                if      ?cs: xtmp dup>r xbseg >xbuf r> c@ 1+ cmovel
                else    xbuf#-          \ only advance if we got something
                then    ( a1 --- )      \ a1 is the address passed to EXPECT.
                xtmp count >r swap r@ cmove   \ move the line to TIB
                R> span !
                restore> keysfuncptr
                restore> >edattrib
                restore> equit
                restore> dodown
                restore> doup
                restcursor
                restscr
                xtmp count type space   \ show the actual command line
        else    (expect)
        then    ;

' new-expect is expect

only forth also definitions

headers

