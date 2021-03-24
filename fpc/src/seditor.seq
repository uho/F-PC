\ SEDITOR.SEQ   Sequential EDitor          Written by 1987 Tom Zimmer

comment:

Hello -

        SED the Sequential EDitor was written by Tom Zimmer.

        SED is released into the Public Domain. It is included as an
        imbedded portion of the F-PC Forth system, and may be used as
        needed to develop programs on that system. SED is provided in
        source form in the F-PC system to allow you the ability to change
        SEDs characteristics. The Forth system F-PC is also in the public
        domain, and as such you may do with F-PC and SED as you wish.

                                        Tom Zimmer

comment;

decimal         \ always use default to decimal

editor definitions

: statusline    ( -- )
                splitwind?
                if      0
                else    first.textline 1-
                then    ;

  ' first.textline alias helpline
            250 constant ch/l
               187 value helpkey        \ default value is F1 key
                 0 value torig          \ origin of text in text segment
           2573 constant crlfval        \ value of line terminator CRLF.
           8224 constant blbl           \ value of two blanks.
            255 constant linebuf.len

  60 value autosave-seconds
true value autosaving?

55 value prtlines                       \ print lines per page
 0 value keychar                        \ key just pressed
 0 value changed                        \ edit changed flag
 0 value ?eddone                        \ is the edit done?
 0 value gotten                         \ have we already gotten a line?
 0 value imode                          \ insert mode flag
 0 value lmrgn
 0 value lchng                          \ line changed flag
 0 value marking                        \ marked lines shown in reverse?
 0 value markdone
 0 value markfst
 0 value markstrt                       \ mark/get line #
 0 value markend
 0 value markchar                       \ mark/get character offset
 0 value etabsize       8 =: etabsize   \ default to 8 char increment
 0 value ermargin     132 =: ermargin   \ default to 132 char right margin
 0 value updated                        \ have we updated to disk yet?
 0 value ldel.cnt                       \ count of line deletes
 0 value leavesave
 0 value leavenow                       \ leave editor now, don't unnest
 0 value ?leaveprompt    \ do we prompt if the last file is being closed?
 0 value pop-extra
 0 value %read-from
 0 value ?exp_tabs

0 value ?border
0 value lookflg         \ did we find anything last time?
0 value wrapped
0 value wraplen
0 value wraploc
0 value filtering       \ are we looking for ESC and Alt-F10?

create nfil  2 c, 13 c, 10 c,   \ A counted empty file string

0 value linelen

create slook.buf   32 allot     \ search buffer
       slook.buf   32 blank 1 slook.buf c!

248 value hyperdest     \ hypertext character, marks a link destination
249 value hyperchar     \ hypertext character, marks a source link

defer showstat
defer sbutton   ' beep is sbutton       \ screen editor button handler

defer exit.edit         ' quit          is exit.edit  \ default to just quit
defer normkey           ' bl            is normkey
defer normfilter        ' noop          is normfilter
defer normbgstuff       ' noop          is normbgstuff
defer normbutton        ' noop          is normbutton
defer ins-cursor        ' big-cursor    is ins-cursor
defer reset_defered     \ set later to DEFERRESET

0 value vstaton
0 value statcnt

: ?capslock     ( --- f1 ) 0 $417 c@l $40 and 0<> ;
: ?altkey       ( --- f1 ) 0 $417 c@l $08 and 0<> ;
: ?ctrlkey      ( --- f1 ) 0 $417 c@l $04 and 0<> ;
: ?shiftkey     ( --- f1 ) 0 $417 c@l $03 and 0<> ;     \ 05/25/90 tjz

create  linebuf ( linebuf.len ) 300 allot
        linebuf ( linebuf.len ) 300 blank

create split.buf linebuf.len allot split.buf linebuf.len blank
create  wrap.buf linebuf.len allot  wrap.buf linebuf.len blank
create fdbuf     linebuf.len allot fdbuf     linebuf.len erase

0 value csaveflg        \ are we saving characters

0 value ldel.buf

create --'s.buf 132 allot

: gremit create c, does> 1 type ;

$c0 gremit |.   $c4 gremit --   $b3 gremit |    $d9 gremit .|
$bf gremit '|   $da gremit |'

$c8 gremit ||.  $cd gremit ==   $ba gremit ||   $bc gremit .||
$bb gremit '||  $c9 gremit ||'

: -s    ( n1 --- )
        --'s.buf 132 ?browse if $cd else $c4 then fill
        --'s.buf swap type ;

: .g|          ( --- )         \ display a virtical bar character
                ?browse
                if      ||
                else    |
                then    ;

: .g'|         ( --- )
                ?browse
                if      '||
                else    '|
                then    ;

: .g|.         ( --- )
                ?browse
                if      ||.
                else    |.
                then    ;

: .g.|         ( --- )
                ?browse
                if      .||
                else    .|
                then    ;

: .g|'         ( --- )
                ?browse
                if      ||'
                else    |'
                then    ;

: .l           ( n1 n2 --- )   \ Print left justified in fld
                >r (u.) dup>r type r> r> swap - spaces ;

: emptykbd  ( --- )     \ empty any keyboard typeahead
            ?DOSIO
            if    begin       key?
                  while       (key) drop
                  repeat
            else  begin       0 $41A @L
                              0 $41C @L - abs 2 >     \ keyboard depth > 1 key
                  while       bioskey drop
                  repeat
            then  ;

                \ $02 = Shift key, $08 = Alt key, $40 = Caps lock.

: modified     ( --- )         \ mark line and text as having been modified.
                on> lchng on> changed ;

create  end-spcs 80 allot
        end-spcs 80 177 fill    \ 177 is a nice gray character.

: edeeol       ( --- )         \ clear the screen line.
                window.right @> #out - spaces ;

: end-eeol     ( --- )         \ clear the screen line to gray
                ?DOSIO
                if      @> #out @> #line at
                then    window.right @> #out -
                0max    dup 80 <
                if      end-spcs swap type
                else    80 /mod 0
                        ?do     end-spcs   80 type
                        loop    end-spcs swap type
                then    ;

: creeol       ( --- )         \ erase next line.
                cr edeeol first.textcol @> #line at ;

: erase.bottom ( --- )
                first.textcol @> #line rows 1- over - 1 max 0
                do creeol loop at ;

: terminate.edit       ( --- )
                creeol creeol ." Leaving now...." creeol
                erase.bottom exit.edit ;

: ?<>bak       ( --- )                 \ verify current file is not a .BAK
                ed1hndl handle>ext 1+ " BAK" caps-comp 0=
                if      off> renaming
                then
                ed1hndl handle>ext 1+ " $$$" caps-comp 0=
                if      off> renaming
                then    ;

: set.newfile  ( --- )         \ setup memory for a new file
                creeol ."    New File Created "  creeol
                0.2 currentsize 2!
                off> curline                    \ clear current line
                off> lastline                   \ and total lines
                tsegb lineptr tl:!              \ first segment setup
                ?cs: nfil tsegb 0 3 cmovel      \ move in a counted CRLF $
                incr> lastline                  \ inrement total lines
                5 tenths ;

: ?softerror   ( bool a1 n1 --- )      \ bool = false if OK, else type msg
                rot
                if      >r 36 r@ 2/ - 6 over r@ + 2 + 9 box&fill
                        space r> \type
                        bcr ."   \1 Press - \2 ESC "
                        cursor-off
                        begin   beep
                                key 27 ( ESC ) =
                        until
                        cursor-on
                        off> autosaving?
                else    2drop
                then    ;

: placeline    ( a1 --- )
                >r                      \ save line address
                ?cs: r@                 \ moving from line seg & address
                lineptr tl:@ 0          \ to text line seg and offset = 0
                r@ c@ len-accum         \ sum in length to total file size
                1+ cmovel               \ move the data
                r> c@ 1+ paragraph      \ calculate segments to next line
                lineptr tl:@ +          \ add to current lines segment
                incr> curline           \ bump to next line
                lineptr tl:!            \ save seg in line pointer table.
                incr> lastline ;        \ add a line to total lines

: ?0fix        ( a1 -- a1 )            \ fix files of zero length
                dup c@ 0=               \ if line is of length zero
                if      2573 over 1+ !  \ fill in a CRLF
                        2 over c!       \ and set line length to 2
                then    ;

: read.openfile ( --- )                 \ read a file that is already open.
                ?<>bak
                ibfull =: iblen          \ set maximum length read buffer
                0.0 ed1hndl movepointer
                0.0 filepointer 2!
                ibreset
                0 save!> loadline
                ed1hndl save!> seqhandle
                read-from dup if 1- then dup =: %read-from
                0                       \ skip lines till read from line
                ?do     lineread drop
                loop    off> read-from  \ reset read from counter
                off> curline
                off> lastline
                0.0 currentsize 2!
                off> rmmax
                tsegb lineptr tl:!      \ first segment setup
                tsegb #edsegs + $100 - =: tend
                lineread ?0fix placeline
                begin   lineread rmsave endtst? and
                while   placeline
                repeat  drop
                restore> seqhandle
                restore> loadline ;

: .partial     ( --- )
                savecursor
                savescr
                cursor-off
                14 6 63 14 box&fill
                bcr ."     This file is \r TOO BIG \0 to fit in memory."
                bcr
                bcr ."    A partial read was performed. Press a \r KEY "
                bcr
                bcr ." \s10\1  Starting in BROWSE mode!!  \b"
                emptykbd key? if key drop then key drop
                on> ?browse
                restscr
                restcursor ;

: read.oldfile  ( --- )         \ get existing file
                off> newfl
                read.openfile           \ read it
                outbuf c@ 0<>           \ did we get it all
                if      .partial        \ if not then warn user a
                then    ;               \ partial read was performed

: warn-prompt  ( --- )
                ." \4 NO ROOM TO SAVE \0 changes made to this file !!\b\:03"
                bcr bcr
                ." \t  You might try using Alt-W to write to another drive."
                bcr
                bcr ." \s16PRESS A KEY to acknowledge \b"
                emptykbd key? if key drop then key drop
                off> autosaving? ;

: ?diskfull    ( --- f1 )
                renaming 0= ?browse or
                if      false exit
                then
                ed1hndl >nam 1+ c@ ':' =
                if  ed1hndl >nam c@ bl or 96 - else 0 then
                getdiskfree * 0 128 um/mod nip UM*      \ 05/25/90 tjz
                65000.  128 um/mod nip 0 d< dup
                if      savescr cursor-off
                        8 4 72 16 box&fill
                        bcr ." \s24\2 WARNING !! "
                        bcr
                        bcr
                        ."   You have LESS than 65000 bytes free on disk\b\:03"
                        bcr
                        bcr ."   There may be " warn-prompt
                        off> renaming
                        off> backingup
                        restscr cursor-on
                then    ;

: ?enoughdisk  ( --- f1 )      \ true if there is enough disk space to save
                ed1hndl >nam 1+ c@ ':' =
                if  ed1hndl >nam c@ bl or 96 - else 0 then
                getdiskfree * 0
                renaming 0=
                if      currentsize 2@ d+
                then    128 um/mod nip UM*      \ 05/25/90 tjz
                #edsegs tend toff - - 5 / 4 * 8 / 0 d< dup
                                        \ * .8 / 8 to 128 bytes units
                if      savescr cursor-off
                        8 4 72 14 box&fill
                        bcr ." \s24\4 WARNING !! \b\:03"
                        bcr
                        bcr ."   There is " warn-prompt
                        restscr cursor-on
                then    0= ;

                                \ n1 = edit file line number
                                \ f1 = true if error
: linewrite    ( n1 --- f1 )   \ write a text line and return flag
                >lineptr tl:@ dup>r 1   \ source segment & offset
                wseg wblen              \ dest   segment & offset
                r> 0 c@l dup>r cmovel   \ length and move it
                r> +!> wblen            \ bump length
                wblen writelim >
                if      0 wblen ed2hndl wseg exhwrite wblen = dup
                        if      off> wblen
                        then    0=
                else    false
                then    ;

: flushwrite   ( --- f1 )      \ write the remainder of the write buffer
                wblen 0<>
                if      0 wblen ed2hndl wseg exhwrite wblen = dup
                        if      off> wblen
                        then    0=
                else    false
                then    ;

: write.file   ( --- )         \ write file in ed2hndl
                                \ WRITE.FILE assumes we are on FIRST line.
                ?browse ?exit   \ leave if we are in browse mode
                ed1hndl ed2hndl b/hcb cmove     \ move name to work handle
                renaming
                if      " $$$" ">$ ed2hndl $>ext        \ write to .$$$
                then
                ed2hndl hcreate                 \ create the new file
                dup " \4 Error Making File " ?softerror ?exit  \ *** EXIT ***
                0.0 ed2hndl movepointer
                off> wblen                      \ reset write buffer
                lastline 1+ 1 max maxlines min 0
                ?do     i linewrite ?leave
                loop
                flushwrite      ( --- f1 )
                " \4 Error while writing, probably out of space " ?softerror
                ed2hndl hclose " \4 Error Closing File " ?softerror ;

0 value escflg

: skeyfilter    ( n1 --- n2 )
                normfilter
                filtering 0= ?exit
( escape key )  dup  27 = if drop 13 on> escflg then
( Alt-F10 key)  dup 241 = if drop 13 on> escflg then
(     F10 key)  dup 196 = if drop 13 on> escflg then ;

: put           ( --- )         \ save a file
                write.file ;

: linebuf:      ( --- seg a1 )  \ a useful primitive
                ?cs: linebuf ;

: lineseginfo   ( --- seg a1 n1 )   \ segment of current line & length
                curline #lineseg 1 over 0 c@l ;

: showcur       ( --- )         \ display cursor at proper loc
                screenchar winoff - first.textcol +
                window.left max window.right min screenline at ;

: #lineseginfo  ( n1 --- seg a1 n2 )
                #lineseg 1 over 0 c@l ;

: stripbl's     ( --- )         \ strip off trailing blanks
                linebuf count -trailing linebuf c! drop ;

: discard.BAK   ( --- )
                renaming 0= ?exit
                ed1hndl ed2hndl $>handle
                " BAK" ">$ ed2hndl $>ext
                ed2hndl hdelete drop ;

: discard.$$$   ( --- )
                renaming 0= ?exit
                ed1hndl ed2hndl $>handle
                " $$$" ">$ ed2hndl $>ext
                ed2hndl hdelete drop ;

: norm>bak      ( --- err )     \ rename the normal filename to be .BAK
                                \ return err = error code if it failed
                                \ return err = 0 if no error
                read-write                      \ try to open it read/write
                ed1hndl hopen dup 0=            \ does original file exist?
                if      drop
                        ed1hndl hclose drop     \ close it for now
                        " BAK" ">$ ed2hndl $>ext \ change ED2HNDL to .BAK
                        ed2hndl hdelete drop    \ delete old backup if there
                        ed1hndl ed2hndl hrename \ rename original to .BAK
                then    ;                       \ exist, we don't care

: ?ferr         ( err -- err )
                dup dup
                case
                        2 of    "  File does not exist "        endof
                        3 of    "  No Path found "              endof
                        5 of    "  File is READ ONLY "          endof
                                "  Unknown file error "
                        drop
                endcase ?softerror ;

: recover.$$$   ( --- err )             \ return false if all is OK!
                                        \ else return code for error
                renaming dup 0= ?exit drop
                ed1hndl ed2hndl $>handle
                " $$$" ">$ ed2hndl $>ext
                ed2hndl hopen dup 0= swap ?exit drop
                                                \ leave if .$$$ doesn't exist?
                ed2hndl hclose drop             \ close it for now
                norm>bak dup 0=                 \ no error,
                over 2 = or                     \ or file doen't exist
                if      drop                    \ then rename $$$ to norm
                        " $$$" ">$ ed2hndl $>ext \ change ED2HNDL to .$$$
                        ed2hndl ed1hndl hrename \ rename .$$$ to original
                then    ;

editor also

: ?expand_tabs  ( -- )                  \ conditionally expand tabs
                ?exp_tabs 0= ?exit      \ only if expand tabs flag is on
                linebuf 1+ linelen
                begin   9 scan dup
                while   over bl swap c!         \ change to a blank
                        1 /string 2dup          \ step past tab
                        linelen over -          \ calculate text position
                        tabsize @ mod tabsize @ swap -
                        tabsize @ mod >r        \ distance to move
                        over r@ + swap cmove>   \ expand the text
                        over r@ blank           \ fill expanded area with bl's
                        swap r@ + swap          \ adjust remaining text
                        r> +!> linelen          \ adjust line length
                repeat  2drop ;

: getline       ( --- )         \ get current line to linebuf.
                linebuf linebuf.len blank
                lineseginfo >r
                linebuf: 1+ r@ ch/l 2+ min cmovel  ( --- )
                r@ 2- =: linelen
                r> linebuf + 1- dup @ crlfval =
                if      blbl swap !
                else    drop  2 +!> linelen
                then    ?expand_tabs
                ch/l linebuf c!
                off> lchng
                on> gotten ;

: putline       ( --- )
                lchng 0= ?exit          \ only save if changed
                gotten 0= ?exit
                stripbl's               \ restore linebuf to file
                crlfval linebuf count + !
                2 linebuf c+!
                lineptr tl:@ 0 c@l      \ Get OLD line length
                linebuf c@ - negate     \ NEW length from OLD = Difference
                s>d currentsize D+!     \ adjust file size for NEW line
                linebuf:                \ source in line buffer
                lineptr dup tl+ tl:@    \ next line segment
                linebuf c@ 1+ paragraph - \ minus segment for current line
                dup rot tl:!            \ seg current line segment
                dup =: tend             \ set TEND
                0 linebuf c@ 1+ cmovel  \ move the data into text segment
                off> gotten ;

: toline-       ( n1 --- )
                0MAX
                curline over <= if drop exit then
                dup>r #lineseg                  \ source line segment
                toff over - >r                  \ amount moved is saved
                tend r@ -                       \ destination line segment
                2dup - negate r@ swap >r        \ save distance moved
                cmove-pars>                     \ move the segments
                r> curline r> r@ swap >r
                adj_ptr_lines                   \ adjust the line ptr tbl
                r> negate dup +!> toff +!> tend
                r> =: curline ;

: toline+       ( n1 --- )
                lastline min
                curline over >= if drop exit then
                >r
                curline  #lineseg               \ start segment
                r@ #lineseg over - >r           \ amount moved is saved
                toff                            \ destination segment
                2dup - negate r@ swap >r        \ save distance moved
                cmove-pars                      \ move the segments
                r> r> r@ swap >r curline
                adj_ptr_lines                   \ adjust the line ptr tbl
                r> dup +!> toff +!> tend
                r> =: curline ;

: curline+      ( --- )         \ move down one line in text
                curline lastline = ?exit
                lineseginfo 1+ >r 1- toff 0 r@ cmovel
                toff lineptr tl:! r> paragraph +!> toff
                incr> curline lineptr tl:@ =: tend ;

: curline-      ( --- )         \ move up one line in text
                curline 0= ?exit
                curline 1- >lineptr tl:@ dup 0 c@l 1+ >r 0
                lineptr tl:@ r@ paragraph - 0 r@ cmovel
                r@ paragraph negate +!> toff
                lineptr dup tl:@ r> paragraph - swap tl- tl:!
                decr> curline lineptr tl:@ =: tend ;

                \ conditional lastline and firstline tests

: ?lastline     ( --- f1 ) curline lastline >= ;

: ?firstline    ( --- f1 ) curline 1 < ;

: sinit         ( --- ) \ initialize file, and linelist table
                off> changed
                on> imode
                on> markstrt
                on> markend
        \ setup tend to point to lst possible segment in 64k block
                tsegb #edsegs + =: tend
                lastline 1- >lineptr tl:@ dup 0 c@l paragraph + =: toff
        \ set line beyond last actual line to just beyond end of buffer
                tsegb #edsegs + lastline >lineptr tl:!
                lastline =: curline
                0 toline-               \ go back to first line
                decr> lastline
                off> updated off> lookflg
                off> curline off> lmrgn
                first.textline =: screenline
                off> curline getline ;

: pagechar      ( --- )
                last.textcol ( 1- ) !> #out  ?DOSIO
                if      @> #out @> #line at
                then    ." \r" ;

code ?page-char ( n1 --- )
                pop ax
                sub dx, dx
                mov bx, ' prtlines >body        \ 08/06/90 TJZ allow PRTLINES
                                                \ to be changed to a VALUE
                div bx
                cmp dx, # 0
             0= if      mov ax, # ' pagechar
                        jmp ax
                then
                next    end-code

defer sltypel   ' typeL is sltypel

: exsltypel     ( seg off len -- )      \ type and expand tabs
                rot save!> sseg
                begin   2dup 9 scan dup                 \ look for tab
                while   2dup 2>r                        \ save remainder
                        nip -
                        @> sseg -rot
                        #out @ + last.textcol 1+ min #out @ -
                                                        \ clip to scrn width
                        typel                           \ output preceeding
                        #OUT @ first.textcol - 0max
                        TABSIZE @ MOD TABSIZE @ SWAP -
                        #out @ + last.textcol 1+ min #out @ -
                        SPACES
                        2r> 1 /string                   \ recover remainder
                                                        \ & remove the TAB
                repeat  2drop
                @> sseg -rot
                #out @ + last.textcol 1+ min #out @ -
                typel                                   \ type line remainder
                restore> sseg ;

: sltype        ( n1 --- ) \ n1 is data line
                ?DOSIO
                if      @> #out @> #line at
                        (key?) if drop exit then
                then    >norm
                marking
                if      dup markstrt markend between
                        if >rev then
                then
                on> nosetcur
                #lineseginfo 2- clipline sltypeL edeeol
                off> nosetcur ;

0 value lincol          \ column of linenumber in status line

: doborder      ( --- )
                window.right cols <
                if      window.right  statusline       at .g'|
                        window.left   last.textline 1+ at .g|.
                else    first.textcol last.textline 1+ at
                then
                ed1hndl count dup 8 +
                text.width 2- swap - 2 /
                1- >norm -s
                >attrib1 ."  File = " type space >norm
                window.right cols 1- min #out @ - 0MAX -s
                ?DOSIO 0=                       \ no lower right corner with
                window.right cols < and         \ DOS I/O
                if .g.| then
                window.left 2+ last.textline 1+ at
                ." \4 HELP=F1 "
                window.right 11 - last.textline 1+ at
                ." \4 MENU=ESC "
                window.right cols <
                if      last.textline 1+ first.textline
                        ?do     ( last.textcol )
                                window.right i at .g|
                                window.left  i at .g|
                        loop
                        mouseflg
                        if      >attrib4
                                window.right first.textline at          ." "
                                window.left first.textline  at          ." "
                                window.right 13 - last.textline 1+ at   ." "
                                window.right last.textline 4 - at       ." U"
                                window.right last.textline 3 - at       ." P"
                                window.right last.textline 1 - at       ." D"
                                window.right last.textline     at       ." N"
                                >norm
                        then
                then    off> ?border ;

\ *************************************************************************
\ Improvements to the status line of the editor           By John A. Peters
\ *************************************************************************

: <statfunc>    ( --- )         \ show file status to user
                >attrib1
                ."  Line="       @> #out =: lincol
                                 curline  %read-from + 1+ 1 u.r
                ." /"            lastline %read-from + 1+ 3 .l
                30 sp>col
                ." Column="      screenchar            1+ 1 .r
                ." /"            rmargin @                3 .l
                45 sp>col
                ." Page="        curline  prtlines /   1+ 1 .r
                ." /"            lastline prtlines /   1+ 3 .l
                59 sp>col
                ." Chars=" currentsize 2@        1   d.r
                window.right 7 - sp>col
                >norm window.right @> #out - 0MAX -s
                ?border
                if      doborder
                then    ;

\ *************************************************************************
\ *************************************************************************

: ?.changed    ( -- )          \ display char for file modified
                changed updated or              \ if changed or updated
                if      changed                 \ if changed
                        if      ." \4c"         \ display a 'c' for changed
                        else    ." \4u"         \ display a 'u' for updated
                        then
                else    1 -s                    \ else display a dash
                then    ;

: fullfunc      ( --- )         \ status for when file is full > 64k
                window.left dup 0MAX statusline at >norm 0>=
                if      .g|'
                then
                ?.changed 1 -s
                ." \5MEM FULL" <statfunc> ;

: statfunc      ( --- )
                window.left dup 0MAX statusline at >norm 0>=
                if      .g|'
                then
                ?.changed 1 -s
                marking markdone 0= and
                if
." \2 MARKING TEXT \r  Use up and down arrow to select lines of text.  \2 F3=Done "
                        2 -s
                else    ?browse
                        if              ." \4 BROWSE "
                        else    imode
                                if      ." \4 INSERT "
                                else    ." \1OVERTYPE"
                                then
                        then    <statfunc>
                        mouseflg
                        if      71 statusline at ." \4\0Ä"
                        else    73 statusline at
                        then    >attrib4
                        browselevel 0>
                        if      ."  +"
                                browselevel 3 .l
                        else    ."  F10 "
                        then
                then    >norm ;

' statfunc is showstat

: ?full         ( --- f1 )      \ is memory full?
                tend toff - $100 < ;     \ need more than $100 = 1600 decimal

: ?showfull     ( --- f1 )      \ set status func for memory
                ?full dup       \ condition
                if      ['] fullfunc is showstat
                else    ['] statfunc is showstat
                then    ;

: ?maxlines     ( --- f1 )
                lastline 4 + maxlines u> ;

: ?left/right   ( --- )
                screenchar text.width 1- -      \ winoff must be at least
                winoff max                      \ but not less than now
                =: winoff                       \ new value
                screenchar winoff <             \ left edge check
                if      screenchar =: winoff
                then    ;

: sdisp         ( --- )
                first.textcol screenline at on> nosetcur
                marking
                if      curline markstrt markend between
                        if >rev then
                then
                ?CS: linebuf 1+ linelen clipline typeL edeeol
                curline ?page-char off> nosetcur >norm ;

: scrshow       ( --- )         \ display screen full of file.
                cursor-off
                ?left/right
                first.textline curline screenline
                first.textline - -
                0MAX dup last.textline 1+ first.textline - + swap
                do      i curline =     >norm
                        if      sdisp
                        else    dup !> #line first.textcol =: #out
                                i lastline <=
                                if      i sltype
                                else    end-eeol
                                then    i ?page-char
                        then    1+
                loop    drop >norm cursor-on ;

: <sdln>        ( --- ) putline curline+ getline ;

: <suln>        ( --- ) putline curline- getline ;

: sdisplay      ( --- )         \ display current screen line.
                cursor-off sdisp cursor-on ;

: ins.linelist  ( --- )                 \ add new entry to line pointer list.
                lineptr tl: dup tl+ tl:
                maxlines curline - 2- 2* cmovel>
                incr> lastline
                lineptr dup tl+ tl:@    \ next line segment
                1-                      \ minus segment for current line
                dup rot tl:!            \ seg current line segment
                =: tend                 \ set TEND
                lineptr tl:@
                2 over 0 c!l            \ set length to 0
                crlfval swap 1 !l       \ put in CRLF
                0.2 currentsize D+!     \ Adjust file size
                ;

: ?appendline   ( --- )
                ?lastline
                if      lineptr tl:@ dup>r 0    \ from  seg offset
                        r@ 1- 0                 \ to    seg offset
                        tsegb #edsegs + r> -    \ length in segments
                        16 *                    \ convert to bytes
                        cmovel                  \ move the data
                        lineptr tl:@ 1-         \ correct line pointer value
                        lineptr tl:!            \ save into line table
                        tsegb #edsegs + 1-
                        lineptr tl+ tl:!        \ new last = 1 before end
                        lineptr tl+ tl:@        \ segment of NEW last line
                           2 over 0 c!l         \ set count 2
                        crlfval swap 1 !l       \ put in CRLF
                        tsegb #edsegs +         \ get the last segment
                        lineptr tl+ tl+ tl:!    \ save in lastline + 1
                        incr> lastline          \ one more line
                        0.2 currentsize D+!     \ adjust length
                then    ;

: clipdown      ( --- )
                screenline >r
                last.textline lastline curline - 0MAX -
                screenline max last.textline min
                curline first.textline + min
                dup =: screenline r> <>
                if      scrshow then    ;

defer ?mark-plus        ' noop is ?mark-plus

: sdln          ( --- )         \ sequential line down
                ?lastline ?exit
                <sdln> incr> screenline
                ?mark-plus clipdown ;

: <shom>        ( --- )         \ home to beginning of file
                putline 0 toline-
                first.textline =: screenline
                getline ;

: shom          ( --- )
                <shom>
                off> screenchar
                off> lmrgn
                scrshow ;

: suln         ( --- )         \ sequential line up
                ?firstline ?exit
                <suln> decr> screenline
                ?mark-plus screenline >r
                screenline first.textline - curline min
                0MAX first.textline + dup =: screenline r> <>
                if      scrshow
                then    ;

: ?cursor       ( --- )
                imode if ins-cursor else norm-cursor then ;

: line>ldel.buf ( --- )
                dseg
                if      dseg ldel.buf 2dup mxlln +
                        ldel.cnt maxdline 1- min mxlln * cmovel>
                        ldel.cnt 1+ maxdline 1- min =: ldel.cnt
                        linelen linebuf c! ?cs: linebuf dseg ldel.buf
                        linelen 1+ mxlln min cmovel
                then    ;

: ldel>linebuf  ( --- )
                dseg
                if      dseg ldel.buf 2dup c@l
                        ?cs: linebuf rot 1+ cmovel
                        linebuf c@ =: linelen
                        dseg ldel.buf 2dup mxlln + 2swap
                        ldel.cnt maxdline min dup 1- =: ldel.cnt
                        mxlln * cmovel
                then    ;

: #deletelines  ( n1 --- )
                0MAX ?dup 0= ?exit
                >r curline r@ lastline min bounds
                ?do     i >lineptr tl:@ 0 c@l negate -1 currentsize D+!
                loop
                r@ tl* tl:@ =: tend
                lineptr tl: dup r@ tl* + tl: 2swap
                maxlines >lineptr lineptr r@ tl* + - cmovel
                r> negate +!> lastline
                getline modified ;

: linedelete    ( --- )
                ?lastline       \ if we are on the last line, then
                                 \ just clear the line don't delete it.
                if      lineptr tl:@ 0 c@l negate s>d currentsize D+!
                        2 s>d currentsize D+!
                        tsegb #edsegs + 1- dup lineptr tl:! =: tend
                           2 curline #lineseg 0 c!l   \ install count of 2
                        crlfval curline #lineseg 1 !l \ containing only CRLF
                else    lineptr tl:@ 0 c@l negate s>d currentsize D+!
                        lineptr dup tl+ tl:@ =: tend
                        maxlines >lineptr over - >r
                        tl: dup tl+ tl: 2swap r> cmovel
                        decr> lastline
                then    getline modified ;

: <ldel>        ( --- )         \ delete the current line.
                line>ldel.buf linedelete ?showfull drop ;

: ldel          ( --- )
                ?browse ?exit
                <ldel> scrshow ;

: to.line       ( n1 --- )
                putline toline+ getline ;

: backto.line   ( n1 --- )
                putline toline- getline ;

: .elapse       ( --- )
                ." Edit time " time-elapsed b>t
                ttime 2@ form-time count type ;

: updt          ( --- )         \ save changes if any to disk.
                ?browse ?exit
                save> tx
                save> ty
                savescr
                cursor-off
                changed 0=
                if      8 6 70 10 box&fill
                        bcr ."  \2 NO CHANGES to save in "
                        >attrib2 .ed1hndl >norm 5 tenths
                else
                        save> screenline
                        curline >r
                        8 7 70 9 box&fill
                        ."  \2 Saving Changes to "
                        >attrib2 .ed1hndl >norm
                        <shom>
                        discard.bak
                        ?enoughdisk
                        if      put off> changed on> updated
                        else    showstat
                        then
                        r> to.line
                        restore> screenline
                then    5 tenths scrshow ?cursor emptykbd
                restscr
                restore> ty
                restore> tx
                cursor-on showcur ;

defer try_to_open       ' noop is try_to_open

: ?newopen      ( -- )
                ?eddone                 \ if ?eddone true
                hdepth 1 < and          \ and handle depth = 0
                leavesave 0= and        \ and leavesave is false
                leavenow 0= and         \ and doleave is false
                if      savescr
                        18 15 62 18 box&fill
                        ."  \1 Type in the name of a file to edit, or " bcr
                        ."     \1 press \2 ESC \1 to leave the editor. "
                        try_to_open
                        restscr
                        leavesave negate =: leavesave
                                        \ convert -1 to 1 to make <RED>
                                        \ not save where we are leaving from
                then    ;

: squt          ( c1 --- c1 )   \ discard changes and exit
                ?shiftkey >r
                renaming changed and
                if      put
                then
                off> loadline
                off> screenchar
\                discard.$$$
                on> ?eddone
                off> edready
                r> 0=
                if      ?newopen
                else    on> pop-extra
                then    0 rows 1- at
                off> lmrgn ;

: sesc          ( c1 --- c1 )   \ save changes and exit
                curline 1+ =: loadline
                <shom>
                cursor-off
                changed
                if      savescr
                        6 6 74 10 box&fill bcr
                        ."  Saving Changes to " .ed1hndl bcr
                        ?enoughdisk
                        if      discard.bak
                                put
                                recover.$$$ ?ferr 0=
                                if      on> ?eddone
                                        off> changed
                                        7 tenths
                                then    restscr
                                ?newopen
                        else    restscr scrshow showstat
                        then
                else    savescr
                        true    updated
                        if      drop recover.$$$ ?ferr 0=
                        then
                        if      on> ?eddone
                                off> changed
                                restscr
                                ?newopen
                        else    restscr scrshow showstat
                        then
                then    0 rows 1- at
                off> lmrgn cursor-on ;

defer <nlnx>    ' noop is <nlnx>

                \ conditionally add a line
: ?addline      ( --- )
                ?lastline
                if      screenchar ch/l =: screenchar
                        <nlnx> =: screenchar
                then    ;

: ?rightshow    ( --- )
                winoff
                screenchar text.width 1- -      \ winoff must be at least
                winoff max                      \ but not less than now
                dup =: winoff                   \ new value
                <>                              \ if new not equal old
                if      scrshow                 \ then update screen
                then    ;

: rchr          ( --- )         \ right a character
                screenchar 1+ ch/l 1- min dup =: screenchar
                132 >=                  \ limit to column 132
                if      off> screenchar ?addline sdln scrshow
                then    ?rightshow ;

: chrptr        ( --- a1 )      \ cur character line pointer
                screenchar linebuf 1+ + ;

                                \ goto beginning of curent line
: shoml         ( --- )
                off> screenchar
                off> lmrgn
                off> winoff
                scrshow ;

: sendl         ( --- )         \ goto end of current line
                stripbl's linebuf c@ =: linelen
                ch/l linebuf c!
                linelen =: screenchar
                ?rightshow ;

: send          ( --- )         \ goto end of file
                putline lastline toline+
                last.textline curline 1+ min =: screenline
                getline sendl scrshow ;

: ?leftshow     ( --- )         \ reshow screen of screen scrolled
                screenchar winoff <
                if      screenchar =: winoff
                        scrshow
                then    ;

: lchr          ( --- )         \ left a character
                -1 +!> screenchar screenchar 0<
                if      off> screenchar suln sendl scrshow
                else    ?leftshow
                then    ;

0 value keycnt
0 value not-saved?
2variable savetime

: autosave      ( --- )
                ?browse ?exit
                autosaving? 0= ?exit
                keycnt 1000 >
                if      not-saved?
                        if      gettime t>b savetime 2!
                                off> not-saved?
                        else    off> keycnt
                                                \ 60k = 10 minutes
                                gettime t>b savetime 2@ d-
                                autosave-seconds 100 *d
                                100. dmax d>
                                changed and
                                if      off> not-saved?
                                        savecursor updt restcursor
                                then
                        then
                else    incr> keycnt
                then    ;

: ?showstatus   ( --- )
                normbgstuff
                autosave
                vstaton 0= ?exit
                statcnt 40 >
                if      off> statcnt off> vstaton
                        @> #out @> #line showstat at ?cursor
                then    incr> statcnt ;

: statkey       ( --- c1 )
                normkey
                off> keycnt
                on> not-saved?
                off> statcnt ;

\ : pdn           ( --- )         \ go down a page in file
\                 ?lastline ?exit
\                 putline getline
\                 last.textline 1+ first.textline - 2- 3 screenline - + 1 max 0
\                ?do      putline curline+ getline
\                         ?lastline
\                         if      last.textline =: screenline leave then
\                 loop    3 last.textline min =: screenline
\                 ?mark-plus clipdown scrshow emptykbd ;

: pdn           ( --- )         \ go down a page in file
                ?lastline ?exit
                putline getline
                last.textline 1+ first.textline - 2- 0 max 0
               ?do      putline curline+ getline
                        ?lastline
                        if      last.textline =: screenline leave then
                loop
                ?mark-plus clipdown scrshow emptykbd ;

\ : pup           ( --- )         \ go up a page in file
\                 ?firstline ?exit
\                 putline getline
\                 last.textline 1+ first.textline - 2- screenline 3 - + 1 max 0
\                ?do      putline curline- getline
\                         ?firstline
\                         if      first.textline =: screenline leave then
\                 loop    3  first.textline curline + min =: screenline
\                 ?mark-plus scrshow emptykbd ;

: pup           ( --- )         \ go up a page in file
                ?firstline ?exit
                putline getline
                last.textline 1+ first.textline - 2- 0 max 0
               ?do      putline curline- getline
                        ?firstline
                        if      first.textline =: screenline leave then
                loop    screenline first.textline curline + min =: screenline
                ?mark-plus scrshow emptykbd ;

: >space        ( --- )         \ move to next space in line
                linelen dup screenchar over min
                ?do     linebuf 1+ i + c@ dup bl =
                        swap 127 > or
                        if      drop i leave then
                loop    =: screenchar   ;

: space>        ( --- )         \ move to non blank in line
                linelen dup screenchar over min
                ?do     linebuf 1+ i + c@ dup bl <>
                        swap 127 > 0= and
                        if      drop i leave then
                loop    linelen min =: screenchar ;

: <<space>      ( ---  n1 )     \ n1 = offset from line strt to prev space
                0 dup screenchar
                ?do     linebuf 1+ i + c@ dup bl =
                        swap 127 > or
                        if      drop i leave then
            -1 +loop    dup =: screenchar ;

: <text         ( --- )      \ move to previous text in line.
                0 dup screenchar
                ?do     linebuf 1+ i + c@ dup bl <>
                        swap 127 > 0= and
                        if      drop i leave then
            -1 +loop    =: screenchar ;

: %scrllft      ( n1 --- )
                winoff 0>
                if      winoff over - 0MAX =: winoff
                        winoff text.width 1- + screenchar min =: screenchar
                        scrshow
                then    drop ;

: scrllft       ( --- )
                4 %scrllft ;

: %scrlrt       ( n1 --- )
                winoff text.width + 252 <
                if      dup +!> winoff
                        winoff screenchar max =: screenchar
                        scrshow
                then    drop ;

: scrlrt        ( --- )
                4 %scrlrt ;

: rwrd          ( --- )
                ?shiftkey if scrlrt  exit then
                screenchar linelen @> rmargin min =
                ?lastline 0= and
                if      off> screenchar sdln scrshow exit
                then    >space
                screenchar linelen >=
                if      scrshow exit then
                space> scrshow ;

: %lwrd         ( --- )         \ go back to previous word.
                screenchar 0= ?firstline   0= and
                if      suln linelen =: screenchar scrshow exit
                then    screenchar 1- 0MAX =: screenchar
                <text   screenchar 0=
                if      scrshow exit
                then    <<space>
                if      incr> screenchar
                then    @> rmargin screenchar min =: screenchar scrshow ;

: lwrd          ( --- )         \ go back to previous word.
                ?shiftkey
                if      scrllft
                else    %lwrd
                then    ;

: splitline     ( --- )
                linebuf screenchar + 1+ dup split.buf 1+
                linelen screenchar - 1+ 0MAX dup>r cmove
                r> split.buf c! ch/l screenchar - blank
                screenchar =: linelen
                ?appendline modified <sdln>
                linebuf linebuf.len blank
                split.buf count linebuf 1+ lmrgn + swap cmove
                split.buf c@ lmrgn + dup linebuf c! =: linelen
                ins.linelist modified <suln> ;

: <nln>         ( --- ) \ inserts line if in insert mode.
                ?showfull ?maxlines or
                if beep exit then
                imode
                if      splitline
                else    ?lastline
                        if      stripbl's linebuf c@ =: screenchar
                                SplitLine
                        then
                then    on> changed ;

' <nln> is <nlnx>

: nln           ( f1 --- f1 )   \ next line function
                                \ inserts line if in insert mode.
                ?browse
                if      sdln
                else    <nln>   sdln
                        lmrgn             =: screenchar
                        lmrgn linelen max =: linelen
                        ch/l linebuf c!
                then    scrshow ;

: nodisp-nln    ( --- ) \ next line function
                        \ inserts line if in insert mode.
                <nln>   <sdln> off> screenchar ch/l linebuf c! ;

: csaveon       on> csaveflg ;

: csaveoff      off> csaveflg ;

: csave         ( c1 --- )
                csaveflg
                if      fdbuf c@ linebuf.len 2- >
                        if      fdbuf count >r dup 1+ swap r> cmove
                                -1 fdbuf c+!
                        then    fdbuf count + c!
                                1 fdbuf c+!
                else    drop
                then    ;

: <fdel>        ( --- )
                screenchar dup linebuf + 1+ dup c@ csave
                dup 1+ swap rot ch/l 1+ swap - cmove
                modified ?showfull drop decr> linelen ;

: ?lmargin      ( --- )
                screenchar 0=
                if      lmrgn =: screenchar then ;

: ?right        ( --- )
                wrapped
                if      screenchar wraploc 1- <
                        if      rchr ?lmargin
                        else    screenchar wraploc -
                                lmrgn + 1+ =: screenchar
                                sdln
                        then    scrshow
                else    rchr    ?lmargin
                then    ;

: del<>bl's     ( --- )         \ delete non blanks
                begin   chrptr c@ bl <>
                while   <fdel>
                repeat  ;

: delbl's       ( --- )         \ delete blanks
                ch/l screenchar
                ?do      chrptr c@ bl <> ?leave <fdel>
                loop    ;

: AppendLine    ( --- )         \ append this line to previous.
                ?firstline if beep exit then
                imode
        if      stripbl's split.buf linebuf.len blank
                linebuf split.buf over c@ dup>r 1+ cmove
                curline 1- #lineseg 0 c@l r> + ch/l 1- >
                if      beep getline off> screenchar
                else    ldel suln stripbl's
                        split.buf count linebuf count dup if 1+ then
                        dup>r + swap cmove  modified split.buf c@ r@ +
                        ch/l 10 - min dup 10 + linebuf c! =: linelen
                        r> @> rmargin 1- min =: screenchar putline
                        screenchar linelen 1- min 0MAX =: screenchar
                then
        else    suln stripbl's linebuf c@ =: screenchar
        then    getline sdisplay ;

: bdel          ( --- )         \ back delete
                ?browse
        if      suln sendl
        else    screenchar 0=
                if      AppendLine scrshow
                else    imode
                        if      screenchar dup linebuf + 1+ dup 1-
                                rot ch/l 1+ swap - cmove
                                decr> screenchar
                                linelen 1- screenchar max linelen min
                                =: linelen
                        else    decr> screenchar
                                bl chrptr c! modified putline getline
                        then    sdisplay screenchar lmrgn min =: lmrgn
                then    modified
                ?showfull drop ?leftshow
        then    ;

defer ?wrap     ' noop is ?wrap

: schr          ( c1 --- )    \ insert sequential char in line.
                ?browse   if drop exit then
                ?showfull if drop exit then
                screenchar linelen max =: linelen
                imode
        if      screenchar linebuf 1+ + dup 1+
                linelen screenchar - 0MAX cmove> incr> linelen
        then    dup screenchar linebuf 1+ + c!  bl <>
                if      linelen screenchar 1+ max =: linelen
                then    sdisplay modified
                ?wrap   ?right  ;

: wudel         ( --- )                 \ word undelete
                ?browse ?exit                           \ leave if browsing
                fdbuf c@ 0= ?exit                       \ leave if empty
                131 save!> rmargin                      \ disable word wrap
                true save!> imode                       \ save insert mode
                screenchar              ( -- n1 )       \ current cursor pos
                fdbuf count bounds
                ?do     -1 fdbuf c+!
                        fdbuf count + c@                \ get last character
                        ?dup 0= ?leave
                        schr
                        dup !> screenchar               \ reset cursor pos
                loop    !> screenchar                   \ restore cursor pos
                restore> imode                          \ restore insert mode
                restore> rmargin                        \ restore right margn
                ?wrap ;                                 \ maybe wrap if needed

: @word@cur     ( -- a1 )
                save> screenchar        \ save current cursor position
                <<space>                \ if space found, then bump forward 1
                linebuf 1+ + c@
                dup bl =                \ did we find a space,
                swap hyperchar = or     \ or the hyper character?
                if      incr> screenchar
                then
                screenchar              \ cursor position
                >space                  \ find next space
                screenchar              \ get new cursor position ( old new )
                swap =: screenchar      \ restore cursor position ( new )
                screenchar - 0max       \ length of word under cursor saved
                linebuf 1+ screenchar + \ source
                swap here place
\                r> here c!
\                here count cmove
                restore> screenchar
                here ;

: .nofound      ( --- )
                savecursor
                savescr
                cursor-off
                20 3 60 5 box&fill
                ."  No text has been found.."
                1 seconds
                restscr
                restcursor ;

: #linelook     ( n1 --- f1 )   \ look through line n1
                >r slook.buf count r> #lineseg =: sseg
                1 @> sseg 0 c@l screenchar /string
                search tuck
                if      +!> screenchar
                else    drop
                then    ;

0 value looked

: ?exp_position ( f1 -- f1 )
                dup ?exp_tabs and       \ found and expanding tabs
                if      slook.buf count linebuf count search
                        if      dup =: screenchar
                        then    drop
                then    ;

: look.till     ( --- f1 )
                off> screenchar
                putline
                cursor-off
                0               \ Leave false bool in case we don't find it.
                lastline 1+ curline 1+ over min
                ?do     slook.buf count i #lineseg =: sseg
                        1 @> sseg 0 c@l search
                        if      =: screenchar
                                i to.line 0=    \ change false bool to true
                                leave           \ and leave
                        else    drop
                        then
                        i 127 and 0=
                        if      lincol statusline at >attrib1
                                I %read-from +  1+ 1 .r ." /"
                                lastline %read-from + 1+ 3 .l >norm
                                key? ?leave
                        then
                loop    ?cs: =: sseg
                getline ?exp_position
                emptykbd ?cursor ;

: look.back     ( --- f1 )
                off> screenchar putline
                cursor-off
                0               \ Leave false bool in case we don't find it.
                0 curline 1- 0MAX
                ?do     i #linelook
                        if      i backto.line 0=  \ change false bool to true
                                leave             \ and leave
                        then
                        i 127 and 0=
                        if      lincol statusline at
                                I 1+ 4 >attrib1 .l >norm
                                key? ?leave
                        then
            -1 +loop    ?cs: =: sseg
                getline ?exp_position
                emptykbd ?cursor ;

: <slooker>     ( --- )
                off> looked
                ?lastline ?exit
                slook.buf c@ 0=
                if      rwrd    exit    \ just step to next word
                then    putline getline
                        curline >r r@ #linelook 0=
                        ?cs: =: sseg
                if      look.till dup =: lookflg 0=
                        if      .nofound r@ backto.line
                        else    on> looked then
                else    on> looked
                then    r>drop ;

: slooker       ( --- )
                off> looked
                ?lastline ?exit
                ?capslock 0= save!> caps
                <slooker>
                restore> caps
                screenline 10 <
                if      screenline 1+ curline first.textline +
                        min =: screenline
                then    ;

: slookbk       ( --- )
                off> looked
                ?capslock 0= save!> caps
                curline >r
                look.back dup =: lookflg 0=
                if      .nofound r@ to.line
                else    on> looked
                then    r>drop
                restore> caps ;

: sloob         ( --- ) \ search again backwards
                slookbk scrshow clipdown ;

: slooa         ( --- ) \ search again forward
                incr> screenchar slooker scrshow sdisplay ;

: tobslash      ( -- )  \ to the next back slash following
                ?shiftkey
                if      48 !> screenchar exit
                then
                save> slook.buf         \ save first two chars of search buf
                " \" slook.buf place    \ change to a '\' character
                slooa                   \ look for the character
                restore> slook.buf ;    \ restore first two chars of buffer

: sloon         ( --- )
                savescr
                15 6 64 11 box&fill
                ."  \r Text to look for: \0   <Enter>=accept ESC=cancel"
                bcr
                bcr  ."    Press Alt-A to enter a special character"
                bcr  ."  Press CAPSLOCK ON for a CaSe SENSITIVE search"
                off> stripping_bl's     \ don't string trailing blanks
                                        \ from search string.
                on> autoclear
                >attrib1
                17 8 slook.buf 29 lineeditor       ( --- f1 )
                >norm
                if      cursor-off
                        17 9 at ." \s13\1 Looking ...."
                        63 @> #out - spaces
                        slooa cursor-on
                then    restscr scrshow ;

: sloow         ( -- )          \ search for word under cursor
                @word@cur count 29 min slook.buf place
                sloon ;

create rep.buf   32 allot
       rep.buf  32 erase

0 value repset

: <srepa>       ( --- )
                looked repset and
                if      true save!> imode
                        slook.buf c@ 0
                        ?do     <fdel>
                                modified putline getline
                        loop
                        rep.buf count bounds
                        ?do     i c@ schr
                        loop    off> looked
                        restore> imode
                else    .nofound
                then    scrshow ;

: srepa         ( --- )
                ?browse ?exit
                <srepa> slooa   ;

: srepn         ( --- )
                ?browse ?exit
                off> repset
                looked 0=
                if      .nofound
                else    savescr
                        14 6 70 10 box&fill
        ."  \r Replace found text with: \0  <Enter>=accept ESC=cancel"
                        bcr
                        bcr ." \tPress Alt-A to enter a special character"
                        off> stripping_bl's     \ don't strip trailing balnks
                                                \ from replace string
                        on> autoclear
                        >attrib1
                        16 8 rep.buf 29 lineeditor       ( --- f1 )
                        >norm
                        if      on> repset srepa
                        then
                        restscr
                then    scrshow ;

: repall        ( --- )
                ?browse ?exit
                first.textcol statusline at
                ." \4 Replacing \`"
                slook.buf count type
                ." \` with \`"
                rep.buf count type
                ." \` Press ESC to cancel" >attrib4 edeeol >norm
                looked if <srepa> then
                begin   slooa   looked
                        key?    if key 27 <> and then
                while   <srepa>
                repeat  ;

: already_exists?       ( --- f1 )      \ does filename in ed2hndl exist?
                ed2hndl hopen 0=        \ if so, then prompt for overwrite.
                if      ed2hndl hclose drop
                        cursor-off
                        10 11 at
                        ." \r ALREADY EXISTS, overwrite it? Y/N [N] "
                        key bl or 'y' <> dup
                        if      ." \rAborting...\:05"
                                scrshow
                        else    10 11 at 61 spaces
                        then    cursor-on
                else    false
                then    ;

: wr->fl        ( --- )
                savescr
                 8 6 71 12 box&fill
        ."  \r Write the file in memory to: \0    <Enter>=accept ESC=cancel"
                ed1hndl pad over c@ 1+ cmove
                on> autoclear
                >attrib1
                10 9 pad 59 lineeditor       ( --- f1 )
                >norm
                if      pad
                        dup ed2hndl $>handle
                        ed2hndl pathset drop
                        already_exists?                 \ overwrite existing?
                        if      drop exit               \ if not then exit
                        then
                        ed1hndl $>handle
                        ed1hndl pathset drop
                        on> newfl on> changed
                        save> screenchar
                        save> screenline
                        curline >r
                        <shom>
                        10 11 at ." Saving As File..."
                        ?enoughdisk
                        if      put
                                off> changed on> updated
                                ." .DONE \:05"
                        else    showstat
                        then
                        begin   curline r@ <>
                        while   curline+
                        repeat r>drop
                        restore> screenline
                        restore> screenchar
                        getline
                then    restscr on> ?border scrshow ;

: <joinln>      ( --- )
                132 save!> rmargin              \ guarantee NO WRAP
                   '.' schr                     \ add an extra char
                restore> rmargin                \ restore right margin
                0 save!> screenchar
                   linelen dup 132 < >r >r      \ line < 132 chars long
                   sdln
                   linelen r> + 200 < r> and    \ and total chars < 200
                   if      bdel
                   else    suln
                   then
                restore> screenchar
                bdel ;                          \ delete extra char

: ?addbl        ( --- )         \ add a blank if char before cursor is NOT
                                \ a blank, and SCREENCHAR is NOT zero.
                screenchar ?dup 0= ?exit        \ leave if beginning of line
                1- linebuf 1+ + c@ bl <>        \ or preceeded by a blank
                if      bl schr
                then    ;

: joinln        ( --- )
                ?browse ?exit
                true save!> imode
                0 save!> screenchar
                    sendl ?addbl <joinln> delbl's
                    modified putline getline
                restore> screenchar
                restore> imode
                scrshow ;

: itgl          ( --- )         \ insert mode toggle
                ?browse ?exit
                imode 0= =: imode ?cursor ;

: fdel          ( --- )         \ forward delete
                ?browse ?exit
                screenchar linelen >=
                if      ?addbl <joinln> delbl's
                else    <fdel>
                then
                modified putline getline
                ?showfull drop sdisplay ;

: wdel          ( --- )
                ?browse ?exit
                screenchar linelen >=
                if      ?addbl <joinln>         \ unwrap line
                        chrptr c@ bl =
                        if      delbl's
                        then
                else    chrptr c@ bl <>
                        if      csaveon
                                0 csave         \ prepend null delimiter
                                del<>bl's       \ delete non blank
                                <fdel>          \ delete one blank
                                csaveoff
                                delbl's         \ and delete blanks
                        else    csaveoff
                                delbl's
                        then                    \ for possible undelete
                then
                modified putline getline
                ?showfull drop sdisplay ( scrshow ) ;

: mark-clear    ( -- )
                off> marking
                off> markstrt
                off> markfst
                off> markend
                off> markdone ;

: mark-on/off   ( --- )
                markdone
                if      mark-clear
                        cursor-off
                        25 6 51 8 box&fill
                        ." \s01\r ** Mark is CLEARED ** \:07"
                        cursor-on
                else    marking 0=
                        if      on> marking
                                curline    =: markstrt
                                curline    =: markend
                                curline    =: markfst
                                screenchar =: markchar
                        else    curline markfst >
                                if      markfst    =: markstrt
                                        curline    =: markend
                                else    markfst    =: markend
                                        curline    =: markstrt
                                        screenchar =: markchar
                                then    on> markdone
                        then
                then    scrshow ;

: %?mark-plus   ( -- )
                marking markdone 0= and
                if      curline markfst >
                        if      markfst =: markstrt
                                curline =: markend
                        else    markfst =: markend
                                curline =: markstrt
                        then    scrshow
                then    ;

' %?mark-plus is ?mark-plus

: smrk          ( --- )         \ mark line for get
                mark-on/off ;

: dnln         ( --- ) sdln sdisplay emptykbd ;

: upln          ( --- ) suln sdisplay emptykbd ;

: >screenline   ( n1 -- )       \ goto screenline number n1
                dup>r   screenline <
                if      begin   ?firstline 0= screenline r@ > and
                        while   upln repeat
                else    begin   ?lastline  0= screenline r@ < and
                        while   dnln repeat
                then    r>drop ;

: tscrn         ( --- )         \ goto top of screen
                first.textline >screenline ;

: bscrn         ( --- )         \ goto bottom of screen
                last.textline >screenline ;

: tmscrn        ( --- )         \ goto top middle of screen
                first.textline 7 + >screenline ;

: bmscrn        ( --- )         \ goto bottom middle of screen
                last.textline 7 - >screenline ;

: scldn        ( --- )  screenline last.textline <>
                if      decr> screenline
                        sdln scrshow
                else    sdln
                then    emptykbd ;

: sclup         ( --- ) screenline first.textline <>
                if      incr> screenline
                        suln scrshow
                else    suln
                then    emptykbd ;

: bhyper        ( --- )
                mxlln save!> rmargin
                false save!> caps
                off> looked
                slook.buf @ >r
                hyperchar slook.buf 1+ c! 1 slook.buf c!
                curline >r
                look.back dup =: lookflg 0=
                if      .nofound r@ to.line
                else    on> looked
                then    curline r> - +!> screenline
                screenline first.textline <
                if      last.textline 6 -
                        curline first.textline + min =: screenline
                then
                r> slook.buf !
                restore> caps
                restore> rmargin scrshow sdisplay showcur ;

: nhyper        ( --- )         \ tab expansion word
                slook.buf @ >r
                hyperchar slook.buf 1+ c! 1 slook.buf c!
                mxlln save!> rmargin
                false save!> caps
                incr> screenchar
                curline >r
                <slooker>
                curline r> - +!> screenline     \ keep screen stable as long
                                                \ as possible
                screenline last.textline >=     \ then center on screen
                if      last.textline 6 -
                        curline first.textline + min =: screenline
                then
                restore> caps
                restore> rmargin
                r> slook.buf ! scrshow ;

: sbtab         ( --- )         \ tab left on screen
                ?browse
        if      bhyper
        else    lchr screenchar @> tabsize mod 0 ?do lchr loop
                screenchar lmrgn min =: lmrgn
        then    ;

: stab          ( --- )         \ tab right on screen
                ?browse
        if      nhyper
        else    @> tabsize screenchar @> tabsize mod -
                imode
                if      0
                       ?do      bl schr ?full
                                screenchar lmrgn = or ?leave
                        loop
                else    +!> screenchar
                then    screenchar @> rmargin 1- >=
                if      off> screenchar sdln
                then    linebuf 1+ screenchar bl skip nip 0=
                if      screenchar @> rmargin 6 - min =: lmrgn
                then    scrshow
        then    ;

: <lundel>      ( --- )         \ undo line deletes
                ldel.cnt 0= if beep exit then
                true save!> imode
                off> lmrgn
                off> screenchar
                <nln> ldel>linebuf
                modified putline
                getline
                restore> imode ;

: .nomark       ( --- )         \ inform user no mark has been set
                savescr cursor-off
                ['] noop save!> dobutton
                20 6 58 9 box&fill
                     ."  No MARK has been set, use F3 first."
                bcr ."  Press a \r KEY \0 to continue editing."
                beep key drop
                restore> dobutton
                cursor-on restscr ;

: lundel        ( --- )         \ undo line deletes
                ?browse ?exit
                <lundel> scrshow ;

: sgetl         ( --- )
                ?browse ?exit
                markstrt lastline 2- > ?exit
                marking 0= ?showfull or ?maxlines or if .nomark exit  then
                true save!> imode
                on> changed
                off> screenchar
                nln suln
                restore> imode
                markstrt curline >= if incr> markstrt then
                linebuf linebuf.len blank
                markstrt #lineseginfo 2- >r ?cs: linebuf 1+
                r> ch/l 2+ min cmovel ch/l linebuf c!
                modified putline getline sdln
                incr> markstrt
                markend markstrt max =: markend
                scrshow ;

: spltln        ( --- )
                ?browse ?exit
                true save!> imode
                      save> screenchar
                nln suln
                restore> screenchar
                restore> imode scrshow ;

: showscreen    ( --- )
                showstat scrshow ?cursor ;

                \ allow entry of any keyboard character
: ^cc           ( --- )
                ?browse ?exit
                window.left 0MAX statusline at
                ." \2  Enter a key to insert "
                showcur key schr ;

: lmset         ( --- )
                screenchar =: lmrgn
                savescr cursor-off
                22 6 58 8 box&fill
                ."  Left Margin set to column " screenchar .
                5 tenths restscr cursor-on showcur ;

: tabset        ( --- )
                putline
                screenchar 1 max dup =: tabsize =: etabsize
                savescr cursor-off
                22 6 58 8 box&fill
                ."  Tabs set column increment " @> tabsize .
                5 tenths restscr
                getline cursor-on showcur scrshow ;

forth definitions

