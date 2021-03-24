\ VIEW.SEQ       Modified to work with nested load files  by Robert Patten
\                From  Viewing code for ZF.               by Tom Zimmer

only forth also definitions  \  hidden also

: NAME>PAD      ( A1 --- PAD )
                >r r@ ys: ?cs: pad r> yc@ 31 and 1+ cmovel  \ move name
                pad c@ 31 and pad c!                        \ clip count
                pad count + 1- dup c@ 127 and swap c!       \ mask last ch
                PAD     ;

\ hidden definitions
\ : files_set     ( --- )      NOT NEEDED DELEATED REP
\ : 1file         ( --- false | nfa )   NOT NEEDED DELEATED   REP
\ 0 value lastname   ( rep )
\ 0 value maxcfa     REP

FORTH DEFINITIONS

: viewfile   ( cfa | cfa+1 = a1 maxCFA ) \ maxcfa is zero if not found
\ Locate words source file and retuen file name at pad and it's cfa.
\ if cfa is a veriable in files vocabulary then cfa will return it's
\ load file.
        \ setup defalt file string. This file should be first file loaded.
        DUP  ['] FILES U<= IF
        [ also files ' kernel1.seq >name previous ] literal name>pad drop
        ELSE 0 PAD !  THEN    0  swap
                \ scan vocabulary threads via index
                ['] files >body dup #threads 2* + swap do
                I @  swap ( cfa ) >r         \  thread-head
                begin  2dup u<               \ knot in thread
                while dup l>name             \ nfa of files variable
                           name>             \ start of file's load area
                           dup >body @ 1-    \ end of file's load area
                           r@ u> swap r@  u< and  \
                       \ true: word within file's load area
                       if  nip dup           \ true better choice File
                       then  y@              \ next knot in thread
                repeat drop r> ( cfa ) 2 +loop drop
           dup if l>name dup name>   swap name>pad else pad  then swap ;

\ : ctrl          ( -- f ) 0  $417 c@l 4 and ;

0 value loadedfrom

: >viewfile     ( cfa --- offset a1 )
\ Locate word's source offset and file string a1 form code field address.
        dup>r 1+ viewfile
        LOADEDFROM                              \ if LOADEDFROM is on
        if      off> LOADEDFROM                 \ clear LOADEDFROM
                r>drop >r drop r@ viewfile      \ find where word was loaded
        then    r@ =
        if      0
        else    r@ >view y@
        then    swap r>drop ;

: LOAD          ( n1 --- )      \ n1 is the line number to load from
                depth 1 < if 1 then     \ default to line 1
                ?fileopen
                dup>r >line             \ move to line n1
                cr ." Loading.."
                <load>
                r> =: loadline ;

' fload alias include           \ make an alias that does the same thing as
                                \ FLOAD.

\u f-pc.seq     ' f-pc.seq  constant 'f-pc.seq  \ first file loded on kernel
\u f-pch.seq    ' f-pch.seq constant 'f-pc.seq  \ 05/28/90 21:36:03.89 tjz

\- 'f-pc.seq    \S      \ Don't load more if 'F-PC.SEQ is not defined by now!

\ Change files variables loaded by meta86.seq
\ not needed after first execution
: files_set     ( --- )
                ['] files >body HERE 1000 + #THREADS 2* CMOVE ;

: 1file         ( --- false | nfa )
                HERE 1000 + #THREADS LARGEST DUP
                if      DUP L>NAME >r Y@ SWAP ! r>
                else    nip
                then    ;

files

: ChangeFilesVariables  ( -- )
\ Change files variables in the kernel to support VIEWFILE.
        [ files ] kernel1.seq @ ?exit  \ alredy changed
        files_set
        \ ignore files variables above kernel
        begin 1file [ 'f-pc.seq >name ] literal u<= until
        'f-pc.seq                       \ limit of kernel
        600  654        \  line# in meta86.seq of last fload \ 05/25/90 tjz
        do 1file dup if       \ valid kernel files variable
                cr I .s drop  dup .id \ display limit cfa loadline
                name> tuck  >body !   \ mark limts of load area
                i over >view y!       \ mark source file
                else drop leave then  -1 +loop drop ;

ChangeFilesVariables
\u meta86.seq  files  'f-pc.seq meta86.seq !
forget files_set  \ They have done their job.
forth

