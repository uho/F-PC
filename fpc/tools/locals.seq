\ LOCALS.SEQ    Local variables for F88         by Tom Zimmer

ANEW LOCALVARS

comment:

        Adds LOCAL variables to F88, usage is as follows:

        : localswap     ( n1 n2 --- n2 n1 )
                        2 locals                \ we need 2 local variables
                        locala ! localb !
                        locala @ localb @ ;

        Local variables LOCALA through LOCALD are provided. Local
        variables are WORD in size, if you want to use a double word
        local variable, use LOCALB or LOCALD, and specify 2 or 4 LOCALS

        Local variables are automatically deallocated at the end of the
        definition. You can mix the use of local variables with the
        return stack operators, but be aware that local variabls place
        an extra function on the return stack to clean up the local
        variables on definition exit, so you CANNOT do things like
        " R> DROP EXIT" to pop up one level..This would be bad practice
        in any case since F88 puts two things on the return stack for
        each nest level.

comment;

create lstack 512 allot         \ local variable stack

variable lstkptr                \ local stack pointer
  lstack lstkptr !

code (0locals)  ( --- )
                mov bx, lstkptr
                mov ax, 0 [bx]
                sub lstkptr ax
                jmp ' exit
                end-code

: 0locals       ( --- )
                exit            \ protect against accidental execution
                (0locals)
                ;

code locals     ( n -- )
                pop ax
                shl ax, # 1
                add lstkptr ax                  \ allocate the space
                add ax, # 2                     \ +2 for count word
                inc lstkptr     inc lstkptr
                mov bx, lstkptr
                mov 0 [bx], ax                  \ save count in local stack
                mov ax, # ' 0locals >body @
                add ax, xseg                    \ convert to absolute
                dec rp          dec rp
                mov 0 [rp], ax                  \ push 0LOCALS
                mov ax, # 2
                dec rp          dec rp
                mov 0 [rp], ax                  \ push 2 offset, past exit
                next            end-code

code locala     ( --- a1 )
                mov ax, lstkptr
                sub ax, # 2     1push           end-code

code localb     ( --- a1 )
                mov ax, lstkptr
                sub ax, # 4     1push           end-code

code localc     ( --- a1 )
                mov ax, lstkptr
                sub ax, # 6     1push           end-code

code locald     ( --- a1 )
                mov ax, lstkptr
                sub ax, # 8     1push           end-code

\s      Sample usage of local variables.

: findXinString ( string --- n1 )     \ Search for the first occurance
                1 locals locala on    \ of the "x" character in string
                bl word count 0       \ following tt, return n1 offset to "x".
               ?do      dup i + c@ ascii x =
                        if      i locala ! leave
                        then
                loop    drop locala @ ;


