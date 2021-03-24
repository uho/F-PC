\ FORWARD.SEQ   Forward reference resolution words      by Tom Zimmer

\ Concept borrowed from UR/Forth

: F:            ( --- )         \ Create a forward reference
                defer   ;


: R:            ( name --- )    \ resolve the forward reference
                false save!> warning    \ no redefinition message
                >in @ defined
                if      >r >in ! here >r :
                        r> r> >is !
                else    ." No forward reference found on " count type
                        drop
                then    restore> warning ;

comment:

        FORWARD REFERENCE DEFINITION AND RESOLUTION EXAMPLE

        F: TEST                         \ Define a forward reference

        : TEST1 ... TEST ... ;          \ Use forward referenced word.

        : ... ;                         \ Other definitons
        : ... ;

        R: TEST ... ;                   \ Resolve forward reference.

comment;


