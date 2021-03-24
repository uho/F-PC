\ FUNKEY.SEQ    Function key functions                  by Tom Zimmer

?dark
.comment:
  This file allows assigning functions to the keys F1 through F10.

  The following sequence will assign the F10 key to perform
the Forth function "MYFUNC"

         10 FN! MYFUNC <enter>

  The word .FN will display a list of the current function key
assignments.

  The words FUNON and FUNOFF will enable and disable the function keys
temporarily when their operation is not desired.

    !!!!!!!!!!!!!!!!!!!!!!!!! WARNING !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

    Function keys WILL interfere with the editor, or any program that
    uses function keys for other things. BE SURE to do a FUNOFF before
    starting the editor.

comment;


create f#funcs ' noop dup , dup , dup , dup , dup ,
                      dup , dup , dup , dup ,     ,

variable funflg         funflg off

: funon         funflg on ;
: funoff        funflg off ;

: funkey        ( --- c1 )
                funflg @
                if      funflg off
                        begin   defers key dup 187 dup 9 + between
                                if      dup 187 - 2* f#funcs + @ dup
                                        ['] noop =
                                        if      drop        true
                                        else    nip execute 0
                                        then
                                else    true
                                then
                        until   funflg on
                else    defers key
                then    ;

' funkey is key

: fn!           ( n1 | name --- )
                1 max 10 min 1- 2* f#funcs + ' swap ! ;

: .fn           ( --- )
                cr ." Function key assignments, Functions are "
                funflg @ if ." ENABLED" else ." DISABLED" then
                f#funcs 10 0
                do      dup cr i 1+
                        0 <# #s 'F' hold #> 8 over - spaces type ."  = "
                        i 2* + @ >name .id
                loop    drop ;

: myfunc        ( -- )
                cr ." Executed MYFUNC!" ;

10 fn! myfunc

funon           \ Enable function keys

  