\ POINTER.SEQ   External memory allocation and management   by Tom Zimmer

\ What I need in F-PC is a way to allocate memory from a physical memory
\ area, while providing a runtime way to assure no fracturing of physical
\ memory occurs.

FILES DEFINITIONS

VARIABLE POINTER.SEQ

FORTH DEFINITIONS

CODE PTR_OK?EXIT ( a1 -- a1 | seg )     \ if contents of a1 is non zero, EXIT
                POP BX                  \ with contents on stack, else
                MOV CX, 0 [BX]          \ continue with a1 on stack
          CX<>0 IF      PUSH CX         \ @A1<>0, so EXIT with @A1 on stack
                        XCHG RP, SP
                        POP IP
                        POP ES
                        XCHG RP, SP
                        NEXT
                THEN                    \ @A1=0, so NEXT with A1 on stack
                PUSH BX
                NEXT    END-CODE

\ BODY          +0             +2      +4            +6
\               [ phy_pointer ][ link ][ size_bytes ][.....

: POINTER       ( dbl_bytes | name -- )         \ make a pointer "name"
                ( -- paragraph )                \ it's location
                160 0 DMAX                      \ at least 160 bytes
                CREATE 0 ,                      \ initialize to unallocated
                HERE  PHEAD @ ,  PHEAD !        \ link into chain
                DPARAGRAPH ,                    \ lay in size in paragraphs
                PHEAD @ ,                       \ POINTER CHAIN verify word
                DOES> PTR_OK?EXIT       \ ***** EXITS HERE IF ALLOCATED *****
                >R ?CS: R@ 4 + @ 0 #PARS @ 0 D+ $FFFF 0 DMIN DROP
                SETBLOCK 0=
                IF      ?CS: #PARS @ + R@ ! \ install physical seg
                                R@ 4 + @ #PARS +!   \ adj total paragraphs
                THEN R> @ ;                     \ return physical paragraph

\                DOES> DUP>R @ 0=
\                IF      ?CS: R@ 4 + @ 0 #PARS @ 0 D+ $FFFF 0 DMIN DROP
\                        SETBLOCK 0=
\                        IF      ?CS: #PARS @ + R@ ! \ install physical seg
\                                R@ 4 + @ #PARS +!   \ adj total paragraphs
\                        THEN
\                THEN    R> @ ;                  \ return physical paragraph

 RESOLVES <POINTER>             \ resolve forward reference to POINTER

: ?VALID_POINTER ( plink -- plink )     \ check for valid pointer sturcture
                 DUP 4 + @ OVER <>
                 IF      2- BODY> DUP CR HEX U. YDP @
                         IF     DUP >NAME .ID
                         THEN   DROP
                         ." is NOT a valid POINTER structure!"
                         YDP @ IF ABORT THEN 0 0 BDOS
                 THEN    ;

: %UNPOINTER    ( cfa -- )      \ deallocate pointer given the cfa
                >BODY DUP>R @ 0<>                       \ only if non-zero
                IF      R@ 2+ ?VALID_POINTER DROP
                        R@ @ R@ 4 + @ BOUNDS OVER
                        #PARS @ SWAP ?CS: - - CMOVE-PARS
                        PHEAD @
                        BEGIN   DUP
                        WHILE   ?VALID_POINTER
                                DUP 2- @ R@ @ U>        \ if pointer is above
                                IF      R@ 4 + @ NEGATE OVER 2- +!
                                                        \ adjust phy position
                                THEN
                                @
                        REPEAT  DROP                    \ discard phead end
                        0 R@ !                          \ clear this pointer
                        R@ 4 + @ NEGATE #PARS +!        \ reduce total #pars
                        ?CS: #PARS @ SETBLOCK DROP      \ adjust memory usage
                THEN    R>DROP ;

: UNPOINTER>    ( name -- )     \ deallocate a pointer name following
                ' STATE @
                IF      COMPILE (LIT) X, COMPILE %UNPOINTER
                ELSE    %UNPOINTER
                THEN    ; IMMEDIATE

: 0POINTERS     ( -- )                  \ initialize the FAR arrays
                PHEAD @                         \ beginning of pointer chain
                BEGIN   DUP                     \ while not a list end
                WHILE   ?VALID_POINTER
                        0 OVER 2- !             \ clear pointer
                        @                       \ indirect to next ptr
                REPEAT  DROP
                0 #PARS ! ;                     \ clear number of paragraphs
                                                \ used

: %SIZEOF!      ( dbl_bytes cfa -- )    \ set the size of pointer "cfa"
                >R DPARAGRAPH R> >BODY 2+ ?VALID_POINTER 2+ ! ;

: SIZEOF!>      ( dbl_bytes | name -- ) \ set size of the following pointer
                ' STATE @
                IF      COMPILE (LIT) X, COMPILE %SIZEOF!
                ELSE    %SIZEOF!
                THEN    ; IMMEDIATE

: %SIZEOF@      ( cfa -- dbl_bytes )    \ get the size of pointer "cfa"
                >BODY 2+ ?VALID_POINTER 2+ @ 16 *D ;

: SIZEOF@>      ( -<name>- dbl_bytes )  \ get size of the following pointer
                ' STATE @
                IF      COMPILE (LIT) X, COMPILE %SIZEOF@
                ELSE    %SIZEOF@
                THEN    ; IMMEDIATE

