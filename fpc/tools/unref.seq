\\ UNREF.SEQ             Locate Unreferenced Words       by Tom Zimmer

  A utility to assist in finding those words in your application that are
not ever referenced by any other word in the application (Dead CODE).

  Load this file at the very beginning of your application, then continue
to load the rest of the application. After the complete application
compile finishes, then type .UNREF to see a list of unreferenced words,
or .USAGE to see the usage counts of all referenced words in your
application.

  TURNKEYed applications will automatically display the unreferenced
words before saving the turnkeyed application if UNREF.SEQ is loaded as
part of the application.

  When you have finished checking your application for unreferenced
words, remove UNREF.SEQ from your load file.

NOTE:
        You must use .UNREF and .USAGE before leaving Forth after the
        compile finishes. All of the statistics are kept in a 64k
        array in memory that gets discarded once you leave back to DOS.

{

\+ unedit       unedit          \ clear edit buffer if present and used

code c+!L       ( byte seg adr )
                pop bx          pop ds          pop ax
                add 0 [bx], al  mov bx, cs      mov ds, bx
                next            end-code

65536. pointer refed            \ an array to hold the reference counts

: 0references   ( -- )          \ null out all 64k of REFED array
                refed $0000 $8000 0 lfill
                refed $8000 $8000 0 lfill ;
0references                     \ preclear the references array

: .unref        ( -- )          \ find unreferenced words in filename
                refed save!> sseg
                cr
                $0000 $8000
                begin   0 skip dup
                while   over refed swap c@L 1 =         \ unused?
                        if      over >name .id ?cr
                        then    1 /string
                repeat  2drop
                $8000 $8000
                begin   0 skip dup
                while   over refed swap c@L 1 =         \ unused?
                        if      over >name .id ?cr
                        then    1 /string
                repeat  2drop
                cr
                restore> sseg ;

: .usage        ( -- )          \ find unreferenced words in filename
                refed save!> sseg
                cr
                $0000 $8000
                begin   0 skip dup
                while   over refed swap c@L
                        cr 2/ 3 .r space
                        over >name .id
                        1 /string
                repeat  2drop
                $8000 $8000
                begin   0 skip dup
                while   over refed swap c@L
                        cr 2/ 3 .r space
                        over >name .id
                        1 /string
                repeat  2drop
                cr
                restore> sseg ;

: turnkey       ( -<name>- )    \ turnkey an application,
                                \ but first show unreferenced words
                cls .unref
                cr ." Press a key" key drop
                turnkey ;

: anew          ( -<name>- )    \ discard the word following
                bl word drop ;

: ref_header    ( -- )          \ init the reference count of a defined word
                defers header           \ make the header
                1 refed here c!L ;      \ and set count to unused=1

' ref_header is header          \ link into HEADER

: ref_find      ( -- )          \ bump the reference count of a word
                defers find dup                 \ if word was found
                if      over 2 refed rot c+!L   \ bump usage count
                then    ;

' ref_find is find              \ link into FIND
' ref_find drop                 \ look it up at least twice

