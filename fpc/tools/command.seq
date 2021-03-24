\ COMMAND.SEQ   Allow Forth commands to be entered      by Tom Zimmer

comment:

  Allows Forth commands to be entered while within a program, without
destroying the current program environment.

  COMMAND will provide a Forth interpreter while in a program.

  Press Enter on an empty line to return when you are done entering
commands.

  Use RET to return if you enter a typing error in the command line.

comment;

: ret           ( --- )
                sp0 @ 80 + @ 12345 - abort" Not COMMAND nested."
                sp0 @ 160 + sp!
                SP0 ! rp0 ! rp! >IN !   DUP SPAN ! #TIB ! 'tib ! ;

: COMMAND       ( --- )
                'TIB @ SPAN @ >IN @ rp@ rp0 @ SP0 @
                sp@ dup rp0 ! dup rp! 160 - dup 'tib ! dup sp0 ! sp!
                12345 sp0 @ 80 + !
                BEGIN   CR .S ." ->" QUERY #TIB @
                WHILE   INTERPRET
                REPEAT  ret ;

