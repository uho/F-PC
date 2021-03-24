\ PATHSET.SEQ   Words used to set the path of a file.   by Tom Zimmer

   0 value flhndl            \ plugged later

: ?drive.extract ( handle --- drive-value )
                dup >nam 1+ c@ ':' =
                if      dup>r >nam c@ bl or 96 -
                        r@ count 2- >r dup 2+ swap r> cmove
                        r@ c@ 2- r@ c! r> count + off
                else    drop 0 25 bdos 1+ then    ;

: ?drive.prepend ( drive-value handle --- )
                over 0=
                if      2drop
                else    dup>r count >r dup 2+ r> cmove>
                        64 + r@ >nam c! ':' r@ >nam 1+ c!
                        r@ c@ 2+ r> c!
                then    ;

\- pathhndl     handle pathhndl

: prepend.path  ( handle --- f1 )
                dup c@ 0= if drop true exit then    \ leave if no name
                pathhndl clr-hcb
                dup =: flhndl ?drive.extract >r
                flhndl >nam c@ '\' <> dup           \ leave if got path
        if      drop
                '\' pathhndl 1+ c! 64 pathhndl c!
                pathhndl 2+ r@ pdos dup 0=
                if      drop
                        pathhndl 1+ 64 0   \ determine path length
                        do      dup i + c@ 0= if i pathhndl c! leave then
                        loop    drop pathhndl c@ 1 >
                        if      '\' pathhndl count + c!
                                pathhndl c@ 1+ pathhndl c!
                        then    flhndl c@ pathhndl c@ + 62 > dup 0=
                        if      drop
                                flhndl 1+ pathhndl c@ over + flhndl c@ cmove>
                                pathhndl count flhndl 1+ swap cmove
                                pathhndl c@ flhndl c@ + flhndl c!
                                false
                        then
                then
        then    r> flhndl ?drive.prepend 0 flhndl count + c! ;

' PREPEND.PATH IS PATHSET

