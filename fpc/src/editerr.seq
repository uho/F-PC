\ EDITERR.SEQ   Automatically enter editor on compile error

defined ed nip 0= #if \s  #endif        \ if ED not defined, stop loading

only forth also definitions editor also

headerless

create msgsave  32 allot
create wordsave 32 allot

: (EDITERR)     ( a1 n1 --- )
                31 min msgsave place
                >in @ 1- here c@ - 0MAX =: screenchar
                here count 31 min wordsave place
                0 rows 1- at
                30 here c@ msgsave c@ + 2/ - spaces ." ллллл>  "
                wordsave count type 2 spaces
                msgsave count type ."   <ллллл  "
                edinit
                rows 3 - save!> last.textline
                ?leave_set
                <ed>
                restore> last.textline
                done quit    ;

headers

: autoediton    ( --- )
                ['] (editerr) is doerror ;

: autoeditoff   ( --- )
                ['] (doerror) is doerror ;

