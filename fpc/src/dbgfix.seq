\ DBGFIX.SEQ    make INLINE NEXT kernel debugable.      by Tom Zimmer

comment:

  This handy little utility allows me to build the F-PC kernel with INLINE
NEXT, for performance, and at will convert the kernel back to JMP NEXT for
use when DEBUGging. It is a one way street however so once the conversion
is made, the system stays debugable until you restart F-PC.  The word
DEBUGABLE is called by the debugger, to switch the kernel to a debugable
form, just as you start the debug process. On an XT class machine this may
take as much as a second, but is only done once the first time you use the
debugger.

  This utility works by patching each occurance of the unique sequence
"26 AD FF E0" to JMP NEXT.

comment;

only forth also hidden definitions also

variable dbgbl                  \ is Forth curently debugable?

: findinline    ( a1 --- a2 f1 )
                dup up @ >
                if      false exit
                then
        begin   1+ up @ over - 0MAX $26 scan dup
                if      drop dup 1+ @ $FFAD = over 3 + c@ $E0 = and ?dup
                else    true
                then
        until   ;

code fixinline  ( a1 --- a1 )           \ a1 is the address of an inline next.
                pop bx          push bx
                mov 0 [bx], # $E9 byte
                mov ax, # >next
                sub ax, bx
                sub ax, # 3
                mov 1 [bx], ax
                next            end-code

only forth also definitions hidden also

: debugable     ( --- )
                false =: restnext           \ We DO NOT want next restored
                dbgbl @ 0=
                if      >next               \ find and replace each occurance
                        begin   findinline  \ of NEXT found with JMP NEXT
                        while   fixinline
                        repeat  drop
                        dbgbl on
                then    ;

only forth also definitions

