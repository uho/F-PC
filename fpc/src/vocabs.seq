\ VOCABS.SEQ    ONLY ALSO vocabulary operators.

CONTEXT DUP @ SWAP 2+ !   ( Make FORTH also )

VOCABULARY ROOT   ROOT DEFINITIONS

: ALSO          ( -- )
                CONTEXT DUP 2+ #VOCS 2- 2* CMOVE>   ;

: ONLY          ( -- )
                ['] ROOT >BODY  CONTEXT #VOCS 1- 2* 2DUP ERASE + !
                ROOT ;

: SEAL          ( -- )
                ' >BODY   CONTEXT #VOCS 2* ERASE   CONTEXT !   ;

: PREVIOUS      ( -- )
                CONTEXT DUP 2+ SWAP #VOCS 2- 2* CMOVE
                CONTEXT #VOCS 2- 2* + OFF   ;

: FORTH         ( --- ) FORTH ;
: DEFINITIONS   ( --- ) DEFINITIONS   ;

: ORDER         ( -- )
                CR ." Context: " CONTEXT #VOCS 0 DO DUP @ ?DUP
                IF  BODY> >NAME .ID THEN   2+ LOOP DROP
                CR ." Current: " CURRENT @ BODY> >NAME .ID  ;

: VOCS          ( -- )
                CR VOC-LINK @ BEGIN   DUP #THREADS 2* -
                BODY> >NAME .ID ?CR @ DUP 0= UNTIL   DROP   ;

ONLY FORTH ALSO DEFINITIONS
VOCABULARY HIDDEN               \ Some vocabularies we will need.
VOCABULARY BUG

