\ From Dr. Dobb's Jr., Sept., 1988
\ Translated to Forth by R. L. Smith

5 CONSTANT NLOOPS
FVARIABLE ARGUMENT
FVARIABLE RESULT

: TESTSQRT  ( -- )
        ." SQUARE ROOTS   "
        NLOOPS 0
        DO      ." ."  F0.0 ARGUMENT F!
                BEGIN
                        ARGUMENT F@ 1000.0E0 F> 0=
                WHILE
                        ARGUMENT F@ FSQRT RESULT F!
                        ARGUMENT F@ F1.0 F+ ARGUMENT F!
                REPEAT
        LOOP CR ;

: TESTLN   ( -- )
        ." LOGS   "
        NLOOPS 0
        DO      ." ."  0.1E0 ARGUMENT F!
                BEGIN
                        ARGUMENT F@ 1000.1E0 F> 0=
                WHILE
                        ARGUMENT F@ FLN RESULT F!
                        ARGUMENT F@ F1.0 F+ ARGUMENT F!
                REPEAT
        LOOP CR ;

: TESTEXP   ( -- )
        ." EXPONENTIALS   "
        NLOOPS 0
        DO      ." ." 10.0E0 ARGUMENT F!
                BEGIN
                        ARGUMENT F@ 10.0E0 F> 0=
                WHILE
                        ARGUMENT F@ FEXP RESULT F!
                        ARGUMENT F@ 0.01E0 F+ ARGUMENT F!
                REPEAT
        LOOP CR ;

: TESTARCTAN   ( -- )
        ." ARCTANS   "
        NLOOPS 0
        DO      ." ."   0.1E0 ARGUMENT F!
                BEGIN   ARGUMENT F@ 10.0E0 F> 0=
                WHILE
                        ARGUMENT F@ FATAN RESULT F!
                        ARGUMENT F@ 0.01E0 F+ ARGUMENT F!
                REPEAT
        LOOP CR ;

: TESTSIN    ( -- )
        ." SINES   "
        NLOOPS 0
        DO      ." ."   F0.0 ARGUMENT F!
                BEGIN   ARGUMENT F@ PI 2.0E0 F* F> 0=
                WHILE
                        ARGUMENT F@ FSIN RESULT F!
                        ARGUMENT F@ PI 360.0E0 F/ F+ ARGUMENT F!
                REPEAT
        LOOP CR ;

: FPMATH   ( -- )
        TESTSQRT TESTLN TESTEXP TESTARCTAN TESTSIN ;


