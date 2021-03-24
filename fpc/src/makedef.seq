\ MAKEDEF.SEQ   Make a : (colon) definition into a DEFERed word

\ **********************************************************************
\ Converts "name", a normal ':' definition into a defered word, and
\ creates a new word "name2" as a ':' definition with the old function
\ of "name". "name2" is also installed into the defered word "name".
\
\       Used in the form:  MAKEDEFER <existing:def> <new:def> Return
\
\ **********************************************************************

: :def?         ( a1 -- )       \ verify that a1=cfa is a colon definition
                @rel>abs                \ -- a2
                ['] hex @rel>abs <>     \ -- f1
                abort" MUST be a colon definition!" ;

: makedefer     ( | name name2 -- )     \ make name into a defered word
                                        \ make name2 the function that used
                                        \ to be name
                ' dup :def?             \ -- a1
                dup >body @ >r          \ -- a1         r: rseg
                here defer              \ -- a1 a2      r: rseg
                2dup @rel>abs over >body - swap 1+ !
                swap 2dup >body !       \ -- a2 a1      r: rseg
                $E8 swap c!
                $E9 over c!
                ['] hex @rel>abs over >body - over 1+ !
                r> swap >body !         \ --
                ;

\ **********************************************************************
\ Switch the bodies of the two ':' definitions following SWITCH
\ **********************************************************************

code xchange    ( a1 a2 -- )    \ exchange the contents of a1 & a2
                pop bx
                pop di
                mov ax, 0 [bx]
                mov cx, 0 [di]
                mov 0 [bx], cx
                mov 0 [di], ax
                next
                end-code

: switch_bodies ( a1 a2 -- )    \ switch bodies of two colon definitions
                                \ a1 & a2 are code field addresses
                dup :def? >body swap
                dup :def? >body xchange ;

: switch        ( | name name2 -- )
                ' ' switch_bodies ;

