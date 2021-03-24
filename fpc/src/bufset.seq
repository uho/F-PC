\ BUFSET.SEQ    Buffer set for system speed.

32000 VALUE IBLIMIT             \ upper limit of max size IBLEN can be

VARIABLE FUDGE   84 FUDGE !     \ 84 = 6 mhz AT clone

: MS   ( n -- )
                0
                ?do     fudge @ 0
                        ?do     pause
                        loop
                loop  ;

: bufsize-init  ( --- )
                ['] 1+ is pause-func    \ setup to count iterations
                0 1 tenths              \ 1 tenth delay to calibrate time edge
                drop                    \ and discard first try results.
                4                       \ start with a small offset for 8088's
                1 tenths                \ Time it again, this time for a
                                        \ FULL 1/10 of a second.
                ['] noop is pause-func  \ Clear the pause function.
                dup 90 *D               \ Use double number to allow math
                                        \ range for 25 and 33 mhz machines.
                iblimit 0 dmin drop     \ Limit result to IBLIMIT bytes.
                dup =: iblen            \ set buffer length value
                    =: ibfull           \ full size of available buffer space
                1000 1120 */ fudge !    \ calibrate MS to system clock.
                defers initstuff ;      \ continue with initialization

  ' bufsize-init is initstuff

