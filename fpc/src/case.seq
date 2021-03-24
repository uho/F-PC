\ CASE.SEQ      Case statment                           by Bob Smith


: CASE          ( -- )
                COMPILE DOCASE
                <MARK 0 ;                                       IMMEDIATE


: ENDOF         ( -- )
                COMPILE DOENDOF >MARK SWAP >RESOLVE ;           IMMEDIATE

: OF            ( n1 n2 -- n1 )  ( n1 n1 -- )
                COMPILE (OF)  >MARK ;                           IMMEDIATE

: IF-OF         ( n1 n2 -- n1 )  ( f1 -- )
                COMPILE  ?BRANCH >MARK COMPILE DROP ;           IMMEDIATE

: NRESOLVE   ( 0 n1 n2 ... n -- )
                BEGIN   DUP
                        IF      >RESOLVE 0
                        ELSE    -1  THEN
                UNTIL   DROP ;

: ENDCASE       ( -- )
                COMPILE DOENDCASE       \ <- DOENDCASE does NOT do a DROP!!
                NRESOLVE DROP ;                                 IMMEDIATE

