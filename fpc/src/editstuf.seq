\ EDITSTUF.SEQ  Stuff needed by the editor              by Tom Zimmer

headers

only forth also definitions
vocabulary editor
editor definitions also hidden also

headerless

0.0 pointer baseseg     \ bottom of allocated segment space

0 value tsegb           \ text segment beginning for current file
0 value lseg            \ linelist save segment
0 value dseg            \ delete lines segment
0 value #edsegs         \ number of edit buffer segments
0 value maxsegs         \ maximum segments available
0 value hseg            \ hyper or help seg
0 value toff
0 value tend
0 value linesave        \ a place to save the screen line
0 value screenline      \ current screen line
0 value lastline        \ last valid line in file.
0 value rmmax           \ right margin max for current doc
0 value rmset?          \ are we setting the right margin?
0 value ?browse         \ are we currently in browse mode?
0 value winoff          \ window horizontal scrolling offset
0 value read-from       \ start reading file at READ-FROM line
0 value newfl           \ was new file created?
0 value edready         \ ready to edit, we have a file, and can enter
                        \ editor on it, with no problem.
0 value seding          \ are we in the SED editor
0 value backing-out     \ flag true if we are poping out of an edit
0 value splitwind?

0 value backingup
0 value renaming        \ are we keeping backup files?
 TRUE =: renaming
0 value scrline         \ target screen edit line

handle ed1hndl          \ the file we are editing
handle ed2hndl          \ a work handle

2variable currentsize   \ size of the current file on disk in
                        \ 128 byte sectors.

: .ed1hndl      ( --- )
                ed1hndl count type ;

\ For the largest window possible, set these values to those values shown
\ to the right in the comments.

    1      value first.textline         \  1
   23      value last.textline          \ 23
    1      value first.textcol          \  0
   78      value last.textcol           \ 80

: seginit       ( --- )
                defers initstuff
                0.0 currentsize 2!
                off> tsegb off> hseg
                off> lseg  off> dseg
                rows 2- =: last.textline
                cols 2- =: last.textcol ;

' seginit is initstuff

 2000      constant writelim    \ write buffer full limit
  132      constant mxlln       \ maximum line length
    0      value maxlines       \ maximum number of edit file lines
                                \ dynamically adjusted to memory
  100      value maxdline       \ number of saved deleted lines
                                \ adjusted down to 20 if low on memory

   20      constant maxh        \ maximum edit nest depth
b/hcb 8 +  constant b/hstk      \ bytes per hyper stack entry

\   0      value screenchar     \ ********** moved to SEQREAD.SEQ ******
    0      value curline
    0      value wseg           \ the write buffer segment
    0      value wblen          \ write buffer length
                                \ n1 = edit file line number

: memabort      ( n1 --- )
                8 = abort" Could not allocate memory for Editor" ;

: tbuf.init     ( --- )         \ Allocate the edit buffers.
        tsegb 0=
        if      unpointer> baseseg      \ release old allocation
\+ inbseg       inbseg drop             \ make sure lineread space allocated
                MAXBLOCK                \ How much memory is available?

\   We want to preserve enough memory to prevent the transient part of
\   COMMAND.COM from being over written by the editor. If we save 24k
\   or more, then DOS won't have to re-read COMMAND.COM back in when we
\   leave, which saves time when returning to DOS.

                2000 -  ( 32k )         \ enough for COMMAND.COM transient

                0max =: maxsegs         \ largest amount we will try to use.
                maxsegs ( u2/ dup u2/ + )
                =: maxlines             \ adjust lines to memory available
                maxsegs 3000 <          \ if avail. memory less than 48k
                if      20              \ only allow save of 20 deleted lines
                else    100 then =: maxdline
                maxsegs
                maxlines      2* paragraph 1+
                maxdline mxlln * paragraph 1+ +
                writelim 256 +   paragraph +
                maxh b/hstk *    paragraph +
                100 + <                 \ HAVE to HAVE at least 160 bytes or
                                        \ cmove-pars won't work properly,
                                        \ so give it 1.6k more
                if      8 memabort      \ force an error
                then
                maxsegs 16 *D sizeof!> baseseg  \ maximum edit buffer size
                baseseg 0= abort" Could not allocate memory for Editor"
                baseseg
                dup =: hseg     maxh b/hstk *    paragraph 1+ +
                dup =: lseg     maxlines      2* paragraph 1+ + \ ptr table
                dup =: dseg     maxdline mxlln * paragraph 1+ + \ delete buf
                dup =: wseg     writelim 256 +   paragraph +    \ write buf
                dup =: tsegb                                    \ text buffer
                baseseg - maxsegs swap - 10 -   =: #edsegs      \ edit size
        then    ;

0 value hdepth
0 value browselevel

: ed1>hstack    ( --- )         \ move the current edit handle to the
                                \ handle save stack
                hseg 0= ?exit
                hdepth maxh <
                if      ?cs: ed1hndl   hseg b/hstk hdepth *   b/hcb   cmovel
                        hseg hdepth b/hstk * b/hcb + 2>r
                        loadline @ 2r@     !L
                        screenchar 2r@ 2+  !L
                        ?browse    2r@ 4 + !L
                        linesave   2r> 6 + !L
                        incr> hdepth
                        incr> browselevel
                then    ;

: hstack>ed1    ( --- )         \ move the top handle from the handle
                                \ save stack back to the edit handle
                hseg 0= ?exit
                decr> browselevel
                hdepth 1- 0MAX =: hdepth
                hseg b/hstk hdepth *   ?cs: ed1hndl   b/hcb   cmovel
                hseg hdepth b/hstk * b/hcb +
                   2dup @L    loadline !
                2+ 2dup @L =: screenchar
                2+ 2dup @L =: ?browse
                2+      @L =: scrline ;

\ : hswap         ( --- )         \ Swap top two handle stack entries.
\               hdepth 2 < ?exit
\               hseg b/hstk hdepth 1- * hseg b/hstk hdepth    * b/hstk cmovel
\               hseg b/hstk hdepth 2- * hseg b/hstk hdepth 1- * b/hstk cmovel
\               hseg b/hstk hdepth    * hseg b/hstk hdepth 2- * b/hstk cmovel
\               ;

: hrotate       ( --- )         \ rotate the handle stack.
                hseg 0      hseg b/hstk hdepth * b/hstk cmovel
                hseg b/hstk hseg 0      b/hstk hdepth * cmovel ;

: ?hstack       ( --- f1 )      \ return true if room on hstack
                hdepth maxh < ;

defer edinit            ' tbuf.init is edinit   \ Allocate the editor space

: edcr          ( --- )         \ CR for the editor subscreen scroll.
                last.textline rows 4 - >
                splitwind? or
                if      ['] crlf is cr          \ unlink this word from CR
                        crlf                    \ do a real CR
                else    0 last.textline 2+ at
                        -line 0 rows 1- at
                then    ;

: edscroll-on   ( --- )
                ['] edcr is cr ;

defer edscroll          ' edscroll-on is edscroll       \ setup scrolling

forth definitions

headers

: done          ( --- )
                ['] crlf is cr ;

: nobackup      ( --- )
                off> backingup ;

' nobackup alias backupoff

: backupon      ( --- )
                on> backingup ;

headerless

backupon        \ default to auto backup of data

: shomem        ( -- )          \ display the computers memory map
                savecursor savescr
                dark cursor-off
                0 4 at .mem
                cr cr ."    \3 Press any key to continue "
                key drop
                restscr restcursor ;

forth definitions

