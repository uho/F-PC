\ EMMPTR.SEQ      Expanded memory POINTER support       by Tom Zimmer

\- EMMHEAD VARIABLE EMMHEAD     \ expanded memory pointer chain
                                \ normally defined in UTILS.SEQ

0 EMMHEAD !                     \ end of chain is NULL

: EMM-POINTER   ( dbl_bytes | name -- )         \ make a pointer "name"
                ( -- paragraph )                \ it's location
                16384. DMAX                     \ at least 16k bytes
                2dup 65536. d> abort" EMM-POINTERS limited to 64k bytes"
 ( +0)          CREATE 0 ,                      \ initialize to unallocated
 ( +2)          HERE  EMMHEAD @ ,  EMMHEAD !    \ link into chain
 ( +4)          DPARAGRAPH ,                    \ lay in size in paragraphs
 ( +6)          -2 ,                            \ expanded memory handle
 ( +8)          0 ,                             \ emm pages
                DOES>
                emm-status @ abort" No expanded memory available!"
                dup 6 + @ cur_emm_hndl @ =
                if      drop ?emm: exit         \ leave, no mapping needed
                then
                dup>r @ 0=
                IF      R@ 4 + @ u2/ $200 / 1+          \ 16k pages needed?
                        dup r@ 8 + !                    \ save number of pages
                        emm-alloc-pages R@ 6 + !        \ alloc pages set hndl
                        emm-status @                    \ did we get an error?
                        abort" Ran out of expanded memory!"
                THEN    r@ 6 + @                        \ emm handle
                r@ 8 + @ 4 min 0                        \ map the pages
                ?DO     dup i i rot emm-map-pages       \ get page
                        emm-status @ ?leave             \ leave on error
                LOOP    cur_emm_hndl !                  \ set as current hndl
                ?emm: dup r> ! ;                        \ physical paragraph

: EMM-POINTERINIT  ( -- )       \ initialize the EMM-POINTER chain
                defers initstuff
                -1 cur_emm_hndl !               \ clear the current pointer
                emmhead @
                begin   ?dup
                while   dup 2- off              \ clear pointer par# to zero
                        -2 over 4 + !           \ emm handle to -2
                        @                       \ next pointer in chain
                repeat  ;

' EMM-POINTERINIT IS INITSTUFF

: EMM-BYE-FUNC  ( -- )          \ release any expanded memory pointers
                defers byefunc                  \ chain bye funcs
                emmhead @                       \ starting at head
                begin   ?dup                    \ while not empty
                while   dup 4 + @ dup 0>        \ pointers emmhandle
                        if      dup             \ if handle is valid > 0
                                emm-dealloc-pages       \ release EMM
                        then    drop
                        @                       \ next pointer in chain
                repeat  ;

' EMM-BYE-FUNC IS BYEFUNC


