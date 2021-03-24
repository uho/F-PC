\ WYSE50.BLK    A terminal emulator for the WYSE 50     by Tom Zimmer

comment:

  A terminal driver for the WYSE model 50 terminal

  This allows wyse escape sequences to be EMITed, and they will be
interpreted properly.  In a limited manner, of course.  Only those
implimented will be performed.

comment;

VARIABLE ESCFLG         VARIABLE CVAL           VARIABLE RVAL
VARIABLE ACCUM          VARIABLE ATFLG

: ?AT           ( C1 --- )
                DUP ASCII a = ESCFLG @ AND
                IF      DROP ACCUM OFF ATFLG ON R> DROP
                THEN    ;

: ?#            ( C1 --- )
                DUP ASCII 9 <= OVER ASCII 0 >= AND ESCFLG @ AND
                IF      48 - ACCUM @ 10 * + ACCUM ! R> DROP
                THEN    ;

: ?ROW          ( C1 --- )
                DUP ASCII R = ESCFLG @ AND ATFLG @ AND
                IF      DROP ACCUM @ RVAL ! ACCUM OFF R> DROP
                THEN    ;

: ?COL          ( C1 --- )
                DUP ASCII C = ESCFLG @ AND
                IF      DROP ACCUM @ CVAL ! ACCUM OFF
                        CVAL @ 1- RVAL @ 1- IBM-AT
                        #LINE OFF #OUT OFF ATFLG OFF
                        ESCFLG OFF R> DROP
                THEN    ;

: ?-LINE        ( C1 --- )
                DUP ASCII R = ESCFLG @ AND
                IF      DROP ESCFLG OFF IBM--LINE R> DROP THEN ;

\ NOT supporting BLOT on this system for now. ************

\ : ?BLOT         ( C1 --- )
\                 DUP ASCII t = ESCFLG @ AND
\                 IF      DROP ESCFLG OFF 0 IBM-BLOT R> DROP
\                 THEN    ;

: ?EMIT         ( C1 --- )
                ESCFLG @ 0=
                IF      (EMIT)  R> DROP THEN ;

: <W50CR>       ( --- )
                13 (CONSOLE) 10 (CONSOLE)
                #OUT OFF
                #LINE  DUP @ 1+ 24 MIN SWAP ! ;

DEFER W50CR     ' <W50CR> IS W50CR

: ?DOCR         ( C1 --- )
                        \ Ignore Line feed
                DUP CONTROL J = IF DROP       R> DROP EXIT THEN
                        \ pass Carraige Return to CR
                DUP CONTROL M = IF DROP W50CR R> DROP    THEN ;

: ?ESC          ( C1 --- )
                DUP 27 = IF DROP ESCFLG ON R> DROP THEN ;

: ?DARK         ( C1 --- )
                DUP CONTROL Z = IF DROP IBM-DARK
                #LINE OFF #OUT OFF 0 0 IBM-AT R> DROP THEN ;

: W50-IBM.EMIT  ( C1 --- )
                ?ESC    ?DARK   ?DOCR   ?EMIT   ?AT     ?ROW
                ?COL    ?#    ( ?BLOT ) ?-LINE
                ESCFLG OFF ;

: W50-IBM       ['] W50-IBM.EMIT IS EMIT
                ['] (TYPE)       IS TYPE ;


