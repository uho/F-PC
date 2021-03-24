\ Partial LOAD between Editor MARKs     ( AMR 16.04.90 11:06)

\ Minor modifications by T. Zimmer, to install MLOAD as Function key F9.
\
\ Written and donated to F-PC by Alan M Robertson
\
\ Now you just mark some lines, leave the editor, and press F9 to load
\ the lines. This is I must say pretty nifty!!

\ 11/10/90 TJZ
\ Also modified F-PC systems <LOAD> word to add OTHER.CHECK to avoid
\ having to redefine <LOAD>. Saved some space, but not much, due to the
\ additional error handling needed to make sure OTHER.CHECK gets reset
\ in the event of a compile error.
\ This file is now part of the F-PC system.

headerless

defer efsave

: MERRFIX       ( -- )          \ handle memory load errors
                ['] noop is other.check         \ unlink OTHER.CHECK
                @> efsave is errfix             \ restore previous ERRFIX
                errfix ;                        \ perform old ERRFIX

: ?MARKEND.LINE ( - f )
                [ EDITOR ] MARKEND 2+ [ FORTH ] LOADLINE @ U> AND ;

: MLOAD         ( --- )      \ loads between Marks from the Editor
                [ EDITOR ] MARKSTRT  MARKEND  OVER + 1
                < IF   DROP seqhandle >hndle @ 0>=
                        if      ?leave_set
                                .NOMARK <ED>
                        then
                  ELSE 1+
                       ?fileopen
                        ['] ?markend.line is other.check
                                                \ check for at load range end
                        @> errfix   is efsave   \ save current ERRFIX
                        ['] merrfix is errfix   \ handle errors
                        dup>r >line             \ move to first marked-line
                        cr ." Loading.."
                        <LOAD>                  \ Load to last marked-line
                        cr
                        r> =: loadline
                        ['] noop is other.check \ release special check
                        @> efsave is errfix     \ restore previous ERRFIX
                        MARK-CLEAR
                  THEN  0 rows 1- at cr ;

hidden also

>keys1  \ select first key set

' mload 195 lkey!       \ make MLOAD be invoked by pressing F9

only forth also

headers

COMMENT:   Test lines to MARK and load with MLOAD
.( THIS )
.( IS )
.( A )  ( QWZ )
.( TEST )
COMMENT;


