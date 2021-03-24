\ INTERP.SEQ    Interpret a string                      by Martin Tracy

comment:

  Originally suggested to me by Martin Tracy, but all the names have
been changed to protect the guilty !!!

  Allows the definition of macros that will be interpreted at the run
time of the macro. The first character of the first word following
the macro name being defined is the delimiter for the macro as follows:

                macro test " .( this is a test)"

                macro <if  " < if "

                macro test \ .( this is a test)\

  The delimiter must naturally not occur within the macro.

  Another form provided is MACRO:, it is used as follows:

                macro: =if  = if ;

  In this example, MACRO: is terminated by the ";". the first occurance
of ";" in a string will terminate MACRO:, so ";" may NOT be used within
a MACRO: even as part of a word.

  All macros are created as immediate words, and as such they can be used
within a colon definition.

comment;

: eval          ( a1 n1 --- )
                dup
                save!> span     save!> #tib
                save!> 'tib   0 save!> >in
                run
                restore> >in    restore> 'tib
                restore> #tib   restore> span ;

: macro         ( name --- )    \ start a macro definitions
                (:) compile (") here x,
                bl word 1+ c@           \ determine the explicit delimiter
                word c@ 1+ allot
                compile eval state on [compile] ; immediate ;

: macro:        ( name --- )
                (:) compile (") here x,
                ascii ; word c@ 1+ allot        \ delimiter is a ";"
                compile eval state on [compile] ; immediate ;


