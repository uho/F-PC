\ WFL.SEQ       Window file selection.                  by Tom Zimmer

ONLY FORTH ALSO HIDDEN DEFINITIONS ALSO

create dirspec$ ," *.*" b/hcb allot
    -1 dirspec$ >hndle !
       dirspec$ count + off

create defdirspec$ ," *.*" 20 allot

128 array wpad1
128 array wpad2

  500 constant maxdir

   0 value dirrow
   0 value #fls                 \ number of files present
   0 value curfl                \ current file number
   0 value foff
   0 value dirsonly
   0 value first_dirfile

   14 constant b/fnam           \ bytes per filename

b/fnam maxdir *D 64. d+         \ room for directory entries plus a couple
        pointer dirseg

: dirseg_release ( -- )
                unpointer> dirseg ;

                                \ a1 = counted string address
: >pathend      ( a1 --- a2 )   \ a2 = the address of the char beyond last \
                >pathend" drop ;

: >pathend-1    ( a1 --- a2 )
                dup c@ >r               \ save old length
                dup>r >pathend          \ find last backslash
                r@ - 2- 0MAX r@ c!     \ adjust to new count
                r@ >pathend             \ find previous backslash
                r> r> swap c! ;         \ restore old length

headerless

create rootdir '.' c, 0 c,   \ root is . & null
handle dirhndl

defer wflbutton ' noop is wflbutton

create itemstk 10 allot
       itemstk 10 erase

variable item#
         item# off

variable flitem
         flitem off

: setfl         ( n1 --- )
                flitem ! ;

\     Item stack used to hold Directory offsets in window while stepping
\     up and down the directory tree.

: 0istk         ( --- )             \ Clear the item stack
                itemstk 10 erase
                item# off ;

: >istk         ( n1 --- )          \ put an item on the item stack
                item# @ itemstk + c!
                item# @ 1+ 9 min item# ! ;

: istk>         ( --- n1 )          \ get an item from the item stack.
                item# @ 1- 0MAX dup item# !
                itemstk + dup c@ swap off ;

   4 constant forgx
   3 constant forgy             \ top of file window display
  18 constant dlen              \ directory window length

  16 constant dirattrib         \ directory file attribute
     variable aletter

code foff+      ( n1 --- n2 )
                pop ax
                add ax, ' foff >body
                1push
                end-code

headers

code >fadr      ( name# -- seg name_offset )
                pop ax
                mov bx, # b/fnam
                mul bx                  \ b/fnam *
                add ax, # 1             \ 1+
                mov dx, ' dirseg >body  \ dirseg
                2push
                end-code

: dir>pad       ( seg off --- a1 n1 )
                countl wpad1 placel wpad1 count ;
\                2dup c@l >r ?cs: wpad1 r@ 1+ cmovel wpad1 1+ r> ;

: .nam          ( n1 --- )
                >fadr dup>r dir>pad dup>r type
                12 r> - spaces
                dirseg r> 1- c@l dirattrib and
                if      #out @ 1- #line @ at
                        ." ì"
                then    ;

: name>buf      ( --- )         \ move name from dta to buffer
                #fls >fadr nip >r
                wpad1 30 + 12 2dup 0 scan nip -
                dup dirseg r@ c!l >r ?cs: swap dirseg r> r@ 1+ swap cmovel
                wpad1 21 + c@ dirseg r> 1- c!l ;

: $getdir+      ( a1 n1 -- )            \ get more files of spec a1, type n1
                >r
                dirhndl $>handle        \ get directory spec
                dirhndl pathset drop
                wpad1 SET-DTA
                dirhndl >nam r> find-first     \ find files of type n1
                begin   255 and 0= #fls maxdir > 0= and
                while   name>buf
                        dirsonly                \ directories ONLY
                        if      #fls >fadr 1- c@l dirattrib and
                                                \ and a directory
                                if      incr> #fls
                                then
                        else    #fls >fadr 1- c@l dirattrib and 0=
                                if      incr> #fls
                                then
                        then
                        find-next
                repeat  ;

: getdir_init   ( -- )          \ initialize for get directory
                off> curfl
                off> foff
                off> #fls
                off> first_dirfile ;

\ 02/07/92 13:37 tjz added to improve performance

15 array sortn1
15 array sortn2

\ 05/25/90 tjz added from Mike Christopher

: sortdir ( -- )          \ rearrange directory to ascending ascii order
        #fls 1 > if
               #fls 1-  first_dirfile over min ?do
               #fls i 1+ ?do
                 i >fadr 1- ?cs: sortn1 b/fnam cmovel
                 j >fadr 1- ?cs: sortn2 b/fnam cmovel
                 sortn1 2+ sortn2 2+ b/fnam 2- comp 0<
                 if
                    ?cs: sortn1 j >fadr 1- b/fnam cmovel
                    ?cs: sortn2 i >fadr 1- b/fnam cmovel
                 then
               loop
            loop
        then
        ;

: $getdir       ( a1 --- )      \ get filespec a1, plus directories
                getdir_init
                dirseg                          \ check for space available
        if      on> dirsonly                    \ ONLY directories
                dup count wpad2 place           \ save away
                wpad2 >pathend wpad2 1+ - wpad2 c!      \ clip length
                " *." wpad2 +place              \ append *.
                wpad2 count + off               \ null terminate
                wpad2 $10 $getdir+              \ get the directories
                off> dirsonly
                sortdir
                #fls !> first_dirfile           \ don't sort directories
                $20 $getdir+                    \ then get the files
        else    drop                            \ if no space, then leave
        then    ;

: getdir        ( --- )
                dirspec$ $getdir sortdir ;      \ 05/25/90 tjz

headerless

: (at.")        ( x1 y1 | text --- x1 y1+1 )
                2dup at
                2r@ 2dup c@l >r 1+ r@ typeL r> 1+ xeven r> + >r
                1+ ;

: at."          ( x1 y1 | text --- x1 y1+1 )
                compile (at.") x," ; immediate

: showkeys      ( --- )                 \ show some help
                forgx forgy 17 -1 d+
                at." ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿"
                at." ³ Hom ³  ³ PgUp ³"
                at." ³ ÄÄÄÄÅÄÄÄÅÄÄÄÄÄ ³"
                at." ³ End ³  ³ PgDn ³"
                at." ÃÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÁÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿"
                at." ³ A-Z = Next file starting with Letter ³"
                at." ³ ÄÙ = Select file or directory       ³"
                at." ³ Esc = Cancel file selection          ³"
                at." ³ Del = Delete selected file           ³"
                at." ³   \ = Type in a new Directory Spec.  ³"
                at." ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ" 2drop ;

: pathbox       ( --- )
                forgx forgy 17 10 d+ 2dup 52 2 d+ box&fill
                ."  Path = " ;

: showpath      ( --- )
                pathbox forgx forgy 26 11 d+ at
                dirspec$ dup pathset 0=
                if      count type
                else    ." Can't read path" drop
                then    ;

: showdir       ( --- )         \ display directory window
                savecursor
                forgx forgy 1- 2dup 15 dlen 1+ d+ box
                forgx 15 + forgy        at ." \r"
                forgx 15 + forgy dlen + at ." \r"
                dlen 0
        do      forgx forgy 1 i d+ at i foff+ #fls >=
                if      i 0= if      ." ...No Files..."
                             else    ."               " then
                else    curfl i foff+ =
                        if      i =: dirrow
                                >attrib1 ." þ" i foff+ .nam ." þ"
                                >norm
                        else             ."  " i foff+ .nam ."  "
                        then
                then
        loop    restcursor ;

: nfl           ( --- )         \ next file
                curfl #fls 1- 0MAX = if exit then
                curfl 1+ #fls 1- min 0MAX dup !> curfl
                dup #fls < swap dlen 1- - foff = and
                if      foff 1+ #fls 15 - 0MAX min !> foff
                then    ;

: pfl           ( --- )         \ previous file
                curfl 1- 0MAX dup !> curfl
                foff =
                if      foff 1- 0MAX !> foff
                then    ;

: ?lmatch       ( --- f1 )
                curfl >fadr 1+ c@l aletter c@ = ;

: gotofl        ( --- )
                flitem @ 0MAX
                curfl over >
                if      curfl swap do pfl loop
                else    curfl     ?do nfl loop
                then    ;

: 0fl           ( --- )         \ first file
                0 !> curfl
                0 !> foff ;

variable foffsave
variable curflsave

: find_letter   ( c1 --- c1 )       \ search for a file starting with c1
                upc dup aletter c! curfl >r
                curfl #fls 1- 0MAX =
                if      0fl
                else    nfl
                then
                begin   ?lmatch curfl #fls 1- 0MAX = or 0=
                while   nfl
                repeat  ?lmatch 0=
                if      0fl r@ 0
                        ?do     ?lmatch ?leave nfl
                        loop    ?lmatch
\ 05/25/90 tjz  fix per Mike Christopher
                        if  curflsave @ curfl - 1+ dlen >=
                                if
                                     foff foffsave ! curfl curflsave !
                                then
                        then
                else    foff foffsave ! curfl curflsave !
                then    ?lmatch 0= if beep then
                r>drop ;

: efl           ( --- )       \ goto end of file list
                begin nfl curfl #fls 1- 0MAX = until ;

: delfl         ( --- )                   \ delete the current file
        curfl >fadr dup>r 1- c@l dirattrib and
        forgx forgy 19 11 d+ 2dup at 50 spaces at
        if      ." Can't delete directory !\b\:10"
        else    ." Delete \`" dirseg r@ dir>pad type ." \` <- Y/N [N] "
                cursor-on key cursor-off bl or 'y' =
                if      dirspec$ >pathend dirspec$ 1+ - dup dirhndl c!
                        dirspec$ 1+ dirhndl 1+ rot cmove
                        dirseg r@ dir>pad >r dirhndl count + r@ cmove
                        r> dirhndl c+!
                        dirhndl count + off
                        dirhndl hdelete 5 =
                        if      ."  Access denied !\b\:10"
                        then
                then    curfl foff
                        getdir
                        !> foff !> curfl
        then    r>drop showpath ;

: ndir          ( --- )             \ Enter a NEW directory spec
                forgx forgy 17 14 d+ at
                ." Edit the Directory Spec, and press Enter. ESC=Cancel"
                cursor-on pathbox
                on> autoclear
                >attrib1
                forgx forgy 26 11 d+ dirspec$ 41 lineeditor drop
                >norm
                forgx forgy 17 14 d+ at 52 spaces
                dirspec$ count + 1- c@ dup '\' = swap ':' = or
                if      defdirspec$ count >r dirspec$ count + r@ cmove
                             r> dirspec$ c+!
                then    dirspec$ c@ 0=
                if      defdirspec$ dirspec$ over c@ 1+ cmove
                then    0 dirspec$ count + c!
                dirspec$ pathset drop
                cursor-off getdir showpath 0fl ;

: keytests      ( n1 --- )
                case
              false of  ( do nothing )          endof
( up arrow )    200 of  pfl                     endof
( down arrow )  208 of  nfl                     endof
( PgUp )        201 of  dlen 2/ 0 ?do pfl loop  endof
( PgDn )        209 of  dlen 2/ 0 ?do nfl loop  endof
( \ )            92 of  ndir                    endof
( Del )         211 of  delfl                   endof
( Home )        199 of  0fl                     endof
( End )         207 of  efl                     endof
( 0 to 9)               dup      '0' '9' between
( A to Z)               over upc 'A' 'Z' between or
                        if      find_letter drop
                        else    drop beep
                        then
                endcase ;

: ?setdir       ( c1 --- c2 f1 ) \ return bool false if new dir
            curfl >fadr 1- c@l dirattrib and            \ are we on a DIR
            #fls 0> and                                 \ and have anything
            if    drop
                  curfl >fadr dir>pad + off
                                                \ move DIR to PAD, nul term
                  wpad1 1+ @ rootdir @ =        \ is DIR the ROOT?
                  if    dirspec$ 2+ c@ ':' =    \ include drive?
                        if      '\' dirspec$ 3 + c! 3 dirspec$ c!
                                defdirspec$ count dirspec$ +place
                        else    defdirspec$ count dirspec$ place
                        then    dirspec$ count + off  \ nul term
                        begin   item# @ 1 >           \ Clear DIR stack
                        while   istk> drop
                        repeat  istk> setfl           \ set to ROOT
                  else  wpad1 1+ @ " .." drop @ =     \ pop one level?
                        if      \ Now we need to remove a Dir from DIRSPEC$.
                                \ first, preserve the filespec & extention
                                dirspec$ >pathend" 2>r
                                \ so step through DIRSPEC to next to the last
                                \ directory and shorten spec by one directory
                                dirspec$ >pathend-1 dirspec$ 1+ - dirspec$ c!
                                2r> dirspec$ +place     \ append extention
                                dirspec$ count + off    \ nul terminate
                                istk> setfl             \ pop DIR stack
                        else    \ Must be on a directory name other than
                                \ "." or ".." so step down to that directory
                                \ First, preserve the filespec extention
                                dirspec$ >pathend" wpad2 place
                                                        \ save in PAD+100
                                dirspec$ >pathend       \ set dirspec length
                                dirspec$ 1+ -   dirspec$ c!
                                wpad1 count     dirspec$ +place \ append DIR
                                " \"            dirspec$ +place \ append "\"
                                wpad2 count dirspec$ +place \ append name
                                dirspec$ count + off    \ null terinate
                                curfl >istk             \ save directory offset
                                0 setfl                 \ reset offset to zero
                        then
                  then              \ get new directory, and show the path
                  cursor-off getdir showpath gotofl
                  false false
            else  true
            then  ;

headers

FORTH DEFINITIONS

: <getfile> ( --- <a1> f1 )   \ return a1 filename addr and boolean
            rows forgy - 4 - =: dlen
            dirseg 0= if false exit then  \ if it didn't work, then leave
            ['] wflbutton save!> dobutton \ init mouse support
            savecursor
            savescr    \ save cursor and screen
            forgx 2- forgy 2- over 74 + rows 3 - box&fill
            forgx forgy 36 1 d+ at                 \ then my message
            ." \r Tom's Window File Selection Tool "
            forgx forgy 20 7 d+ at
            ." \2 Reading Directory Files... "
            cursor-off getdir        \ clear screen, and get dir
            0fl showkeys showpath         \ show the keys and dir path
            forgx forgy 17 16 d+ at               \ and som help information
            ." Use  to pick a file, or press the first letter of"
            forgx forgy 17 17 d+ at
            ." the file you want, then press Return to select it."
            begin   showdir 0 0 at        \ show the directory
                  key dup 13 = dup        \ wait for a key, if Enter
                  if      drop ?setdir ( c1 --- c2 f1 )     \ try to set dir
                  then
                  over  27 = or
                  over 196 = or 0=  \ else check for escape or F10
            while   keytests        \ if neither then try to find a file
            repeat  13 = dup        \ if it was Enter, then get the file name
                                    \ we are on and move it to PAD. Prepend
                                    \ the DIR spec.
            if    dirspec$ >pathend dirspec$ 1+ - >r
                  dirspec$ 1+ r> wpad1 place
                  curfl >fadr countl wpad1 +placel
                  wpad1 handle>ext c@ '.' <>      \ append '.' if no extension
                  if    '.' wpad1 count + c!
                        1 wpad1 c+!
                  then  wpad1 swap
            then
            restscr                     \ restore screen
            restcursor                  \ restore cursor position
            restore> dobutton
            #fls 0=
            if    dup
                  if    2drop false     \ discard addr even if found if
                                        \ no files in list
                  then
            then  dirseg_release ;      \ return boolean for file selected

' <getfile> is getfile          \ patch in window get file.


