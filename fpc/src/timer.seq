\ TIMER.SEQ      Time measurment words for F-PC.

: GETDATE       ( --- Y MD ) 0 0 42 OS2 DROP ;

: SETDATE       ( NM Y --- ) SWAP 43 OS2 NIP NIP 255 =
                IF      CR ." Invalid DATE " THEN ;

: <GETTIME>     ( --- HM Sh ) 0 0 44 OS2 DROP ;

DEFER GETTIME   ' <GETTIME> IS GETTIME

: SETTIME       ( HM Sh --- ) SWAP 45 OS2 NIP NIP 255 =
                IF      CR ." Invalid TIME " THEN ;

CREATE DTBUF 16 ALLOT

: ##+           ( N1 --- )              \ two low digits of n1 to DATEBUF.
                0 <# # # #> TUCK DTBUF COUNT + SWAP CMOVE
                DTBUF C+! ;

: "+            ( A1 N1 --- )
                TUCK DTBUF COUNT + SWAP CMOVE DTBUF C+! ;

: BUILD-HM      ( N1 --- )
                SPLIT ##+ " :" "+ ##+ ;

: BUILD-SH      ( N1 --- )
                " :" "+ SPLIT ##+ " ." "+ ##+ ;

: BUILD-TIME    ( D1 --- )
                SWAP BUILD-HM BUILD-SH ;

: FORM-TIME     ( D1 --- a1 )
                BASE @ >R DECIMAL
                DTBUF OFF
                BUILD-TIME
                R> BASE ! DTBUF ;

: .TIME         GETTIME FORM-TIME COUNT TYPE ;

: <M/D/Y>       ( D1 --- )
                SPLIT      ##+ " /" "+ ##+ " /" "+ ##+ ;

: <Y-M-D>       ( D1 --- )
                SPLIT ROT  ##+ " -" "+ ##+ " -" "+ ##+ ;

: <D.M.Y>       ( D1 --- )
                SPLIT SWAP ##+ " ." "+ ##+ " ." "+ ##+ ;

DEFER BUILD-DATE   ' <M/D/Y> IS BUILD-DATE

: M/D/Y         ['] <M/D/Y> IS BUILD-DATE ;

: Y-M-D         ['] <Y-M-D> IS BUILD-DATE ;

: D.M.Y         ['] <D.M.Y> IS BUILD-DATE ;

: FORM-DATE     ( D1 --- A1 )
                BASE @ >R DECIMAL
                DTBUF OFF
                BUILD-DATE
                R> BASE ! DTBUF ;

: .DATE         GETDATE FORM-DATE COUNT TYPE ;

VARIABLE STIME 0 ,
VARIABLE TTIME 0 ,

: T>B           SPLIT 100 * + SWAP 0 SWAP
                SPLIT >R 6000 *D D+ R> 1000 * 360 *D D+ ;

: B>T           0 100 UM/MOD >R 100 UM/MOD SWAP TTIME C!
                             R>  60 UM/MOD SWAP TTIME 1+ C!
                                 60   /MOD      TTIME 3 + C!
                                                TTIME 2+ C! ;

: TIME-RESET    GETTIME T>B STIME 2! ;  \ RESET TIMER

: TIME-ELAPSED  GETTIME T>B STIME 2@ D- ; ( - D1 ) \ BINARY

: B>SEC         ( D1 - N1 )      \ CONVERT DOUBLE BINARY TO
                0 100 UM/MOD DROP   \ SECONDS, OVERFLOW AT 18 HRS
                  100 UM/MOD NIP ;

: <.ELAPSED>    TIME-ELAPSED B>T TTIME 2@ FORM-TIME COUNT TYPE ;

: .ELAPSED      CR ." Elapsed time   =  " <.ELAPSED> ;

: TIMER         TIME-RESET INTERPRET .ELAPSED ;

