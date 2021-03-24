\ TIMESTUF.SEQ          More words associated with timing       Tom Zimmer

: SEC-ELAPSED   ( --- N1 )    \ RETURN ELAPSED SECONDS < 18 HRS
                TIME-ELAPSED B>SEC ; ( -- N1 )

: 10TH-ELAPSED  ( --- N1 )
                TIME-ELAPSED 0 100 UM/MOD DROP 10 UM/MOD NIP ;

DEFER PAUSE-FUNC        ' NOOP IS PAUSE-FUNC

: SECONDS       ( N1 --- )   \ PAUSE FOR N1 SECONDS
                STIME 2@ 2>R >R TIME-RESET
                BEGIN   PAUSE  PAUSE-FUNC
                        R@ SEC-ELAPSED > 0=
                UNTIL   R>DROP 2R> STIME 2! ;

: TENTHS        ( N1 --- )       \ PAUSE FOR N1 SECONDS
                STIME 2@ 2>R >R TIME-RESET
                BEGIN   PAUSE  PAUSE-FUNC
                        R@ 10TH-ELAPSED > 0=
                UNTIL   R>DROP 2R> STIME 2! ;

: MINUTES       ( N1 --- )   \ PAUSE FOR N1 MINUTES
                0MAX   0 ?DO   60 SECONDS      LOOP ;

: HOURS         ( N1 --- )       \ PAUSE FOR N1 HOURS
                0MAX   0 ?DO   60 MINUTES      LOOP ;

: 0COMPILER     ( --- )
                TOTALLINES OFF
                TIME-RESET ;

: .COMPSTAT     ( --- )
                cr .elapsed
                cr ." Total lines compiled  = " totallines @ 5 u.r
                cr ." Compiled lines/minute = "
                totallines @ time-elapsed over 32000 u> or
                if      ." Too much time has elapsed to calculate LPM"
                        2drop
                else    >R 60 10000 R@ */MOD
                        10 * SWAP DUP 10 >
                        IF      >R 100 100 100 r> r> SWAP */ */
                                10 /MOD  SWAP 4 > IF 1+ THEN +
                        ELSE    r>drop DROP
                        THEN    2DUP 100 / SWAP 100 / * 3200 >  \ 05/25/90 tjz
                        IF      10 / 1000 */ 10 *D 5 D.R
                        ELSE    1000 */ 5 u.R
                        THEN
                then    ;

: TILLKEY       ( N1 --- )      \ WAIT UP TO N1 SECONDS FOR A KEY THEN GO ON.
                KEY?    IF  DROP EXIT  THEN     \ LEAVE IF KEY PRESSED
                CR ."  Waiting, press SPACEBAR to continue.."
                0MAX 0
                ?DO     KEY?    ?LEAVE
                        1 SECONDS
                LOOP    KEY?
                IF      KEY 3 = ABORT" Quitting " THEN ;

