\\ SETJMP.SEQ       Add "C"-like setjmp to F-PC          by Tom Zimmer

  Add "C"-like error exit to F-PC. If an error occurs that needs to abort
up to a much higher execution level, this file provides a mechanism to
do it without having to include a test at each level above the current
level until the desired level is reached.

                                +0       +2       +4
        jump buffer format:     [rp_ptr] [rp_off] [sp]

        ****** this file can be loaded on either F-PC or TCOM ******

{

DEFINED :: NIP 0= #IF ' : alias :: #ENDIF       \ define if needed

:: jumpbuf      ( | <name> -- )         \ allow defining a jump buffer
                create 0 , 0 , 0 , does> ;

: setjmp        ( a1 -- f1 )            \ a place to come back from "longjmp"
                rp@ over !              \ save current return stack ptr
                r@  over 2+ !           \ and contents of return stack
                sp@ 2+ over 4 + !       \ and current data stack ptr
                drop false ;            \ discard buffer and return false

: longjmp       ( a1 -- f1 )            \ jump back to after "setjmp"
                dup @ 0= abort" No `setjmp' performed!"
                dup>r 2+ @ r@ @ !       \ fix return stack to follow "setjmp"
                r@ 4 + @ sp!            \ fix data stack, as at "setjmp"
                r> @ rp! true ;         \ set return stack to "setjmp"
                                        \ and return true flag

\S              **** don't load the rest of this file ****

\ ***************************************************************************
\ here is an example of how to use "jumpbuf", "setjmp", and "longjmp".
\ ***************************************************************************

jumpbuf jumptest

: test4         ( -- )
                cr ." about to longjump "
                jumptest longjmp        \ jump to right after "setjmp"
                                        \ with a true flag on stask
                ." failed" ;            \ shouldn't ever get here
: test3         ( -- ) test4 ;
: test2         ( -- ) test3 ;
: test          ( -- )
                jumptest setjmp         \ initialize a long jump
                if      ." true "       \ gets here if there was an error
                        ABORT
                else    ." false "      \ goes here on first execution
                then
                test2 ;
}
