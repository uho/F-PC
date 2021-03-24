\\ FLTBUG.SEQ      Floating Point additions to DEBUG      by Bob Smith

{

\ **************************************************************************
\ Load the disassembler, we need it in the debugger
\ **************************************************************************

DEFINED ?H.S NIP
#IF     \S
#THEN

        NEEDS FASSEM.SEQ
        NEEDS FLTAUX.SEQ

WARNING OFF

: .FPSTACK ( -- )
        (wait) showbase $10 =
        IF      h.npustk
        ELSE    .npustk
        THEN ;

WARNING ON

: CHGSIG   ( n -- )
        sigdigits +!
        sigdigits @ 1 umax 17 umin
        sigdigits ! ;

also

: float-commands ( c1 f1 -- c1 f2 )
        [ bug ]
        defers extra-commands
        over upc 'I' = IF       cr ." FPCW:  " fpcw> h.4
                                ."    FPSW:  " fpsw> h.4 0=     THEN
        over '.'     = IF       >r >r dbg.s r> r>
                                cr .fpstack            0=       THEN
        over '<'     = IF       -1 chgsig cr .fpstack  0=       THEN
        over '>'     = IF       1  chgsig cr .fpstack  0=       THEN
        over     '?' = IF       cr
  ." <+flt-sig, >-flt-sig, I-fctl+stat, .-flt-stk: "
                                cr                     0=       THEN
        ;


' float-commands is extra-commands

previous

cr cr
.( The following keys will be activated when using DEBUG or DBG: ) cr cr
.(       .    Print the contents of the floating point 80x87 stack ) cr cr
.(       I    Display 80x87 Control word and Status word Information ) cr cr
.(       >    Increase number of digits printed for 80x87 stack ) cr cr
.(       <    Decrease number of digits printed for 80x87 stack ) cr cr

