\\ RS232IB.SEQ     A BUFFERED CHIP LEVEL RS-232 driver for 8250 chip

  This code supports both RS232 ports, but ONLY ONE AT A TIME !!  You
can select either COM1, or COM2 for serial operation. Input is buffered
with a 256 character buffer, so you shouldn't have trouble with receive
overrun.

  A simple terminal program is included as an example of usage. If you
use TCOM to target compile this file, you will get a VERY SIMPLE terminal
program that runs in FULL DUPLEX.

  This file can be compiled on either F-PC, or TCOM. If you compile it
on F-PC, then the installed interrupt vectors will automatically be
un-installed when you leave F-PC.

****************************** WARNING ******************************

          IF YOU USE TCOM TO COMPILE THIS FILE THEN YOU WILL
        NEED TO USE ?REST_COM1: OR ?REST_COM2: TO RESTORE WHICH
        EVER COMM PORT INTERRUPTS YOU WERE USING IN THE
        APPLICATION BEFORE LEAVING THE PROGRAM OR YOU WILL
        SURELY **CRASH** LATER!!!!

**********************************************************************

{

decimal

\ ***************************************************************************
\ the next few lines define immediate words that allow definitions to
\ select what will be compiled from source lines for either F-PC or TCOM.
\
\ \ DEFINED TARGET-INIT NIP 0= #IF  \ Test for NOT target compiling
\ \
\ \ ' noop alias \F immediate       \ create \F as a NOOP while in F-PC
\ \ ' \    alias \T immediate       \ create \T as "\" while in F-PC
\ \
\ \ #ELSE
\ \
\ \ ' \    alias \F immediate       \ create \F as "\" while TCOMing
\ \ ' noop alias \T immediate       \ create \T as a NOOP while TCOMing
\ \
\ \ #ENDIF

\f code int3       ( -- )
\f                 int 3
\f                 next            end-code

\ ***************************************************************************

variable rs232_base     \ holds physical address where rs232 boards reside.
                        \ $40:00 is where first  board resides,
                        \ $40:02 is where second board resides if present.
$40 rs232_base !

$20 constant EOI        \ End Of Interrupt
$20 constant ictla      \ 8259 interrupt controller #1

variable rs232_port     \ port value initialized by COM-INIT and used by
                        \ other com words.
$00 rs232_port !

\       COM port buffer configuration.

\       [ cnt ][ data up to COMLIMIT ... ]

256 constant bufsize

variable imask          \ 8259 interrupt mask bit variable
                        \ COM1: = $10, COM2: = $08
variable comEOI         \ 8259 End Of Interrupt, for specific interrupt
                        \ INT4 (com1) = $64, INT3 (com2) = $63
variable bufin          \ pointer for buffer input
variable bufout         \ pointer for buffer extract
variable intcnt

bufsize array combuf

variable interrupts     \ counter for the nubmer of interrupts received

0 value rate

: baud          ( n1 --- )   \ convert baud value to timer value "rate"
                115200. rot um/mod nip =: rate ;

\ Use in the form "9600 baud". Any of the standard baud rates can be used
\ up to 115200 baud. Non-standard baud rates can also be used, but
\ accuracy will suffer at non0standard baud rates above 2400 baud.


code com-init   ( parity+stops+bits  baud_val com_port --- )
\T              CLEAR_LABELS
\T              SAVE_BX
\T              xchg si, sp
                pop bx                          \ comm port # 0 or 1
                shl bx, # 1                     \ convert to word offset
                push ds                         \ preserve DS
                mov ds, rs232_base              \ setup ds to get board base
                mov dx, 0 [bx]                  \ get port address of board
                                                \ from $40:$00 or $40:$02.
                                                \ DX = BASE + 0
                pop ds                          \ restore DS
                mov rs232_port dx               \ save port # in value
                add dx, # 3                     \ DX = BASE + 3
                mov al, # $80                   \ [DLAB]
                out dx, al
                sub dx, # 2                     \ DX = BASE + 1
                pop ax                          \ baud timer value
                xchg al, ah                     \ get high part into AL
                out dx, al                      \ set high part
                dec dx                          \ DX = BASE + 0
                jmp 0 $                         \ wait a few clock cycles
           0 $: xchg al, ah                     \ low part into AL
                out dx, al                      \ set low part
                add dx, # 3                     \ adj to control reg
                                                \ DX = BASE + 3
                pop ax                          \ pop parity, stops, & bits
                out dx, al                      \ set parity, stops, & bits
                jmp  1 $                        \ wait some more clocks
           1 $: sub dx, # 2                     \ DX = BASE + 1
\ interrupt enable stuff follows...
                in al, dx                       \ get current intterupt stat
                or al, # $01                    \ include DAV interrupt bit
                out dx, al                      \ interrupt enables off
                add dx, # $03                   \ DX = BASE + 4
                in al, dx                       \ PC bus interrupt control
                or al, # $08                    \ enable chip to bus
                out dx, al                      \ put back into register

                in al, # ictla 1+               \ get int mask reg from 8259
                jmp 3 $
           3 $: mov ah, imask                   \ mask bit in 8259
                                                \ $10 = IRQ4, $08 = IRQ3
                not ah                          \ compliment for AND to follow
                and al, ah                      \ clear com port INT bit
                out # ictla 1+ al               \ restore new mask

                mov intcnt # 0 word             \ zero out interrupt counter
                mov dx, rs232_port              \ get the comport we're using
                in al, dx                       \ read port to clear it
                jmp 2 $
           2 $: in al, dx                       \ read port to clear it again
\F              next
\T              XCHG SI, SP
\T              LOAD_BX
\T              RET
                end-code

code rs232_intoff ( -- )                \ interrupt clear stuff follows...
\T              CLEAR_LABELS
                \ about to SET port interrupt mask bits to disable interrupts
                cli
                in al, # ictla 1+               \ get int mask reg from 8259
                jmp 0 $
           0 $: or al, imask                    \ SET com port MASK INT bits
                                                \ $10 = IRQ4, $08 = IRQ3
                out # ictla 1+ al               \ restore new mask
                mov al, comEOI          \ Specific EOI for interrupt 4
                                        \ INT4=$64, INT3=$63
                out # ictla al
                sti
                mov dx, rs232_port
                inc dx                          \ DX = BASE + 1
                in al, dx                       \ get current intterupt stat
                mov ah, # $01                   \ DAV interrupt bit
                not ah                          \ compliment for and
                and al, ah                      \ clear DAV interrupt bit
                out dx, al                      \ interrupt enables off
                add dx, # $03                   \ DX = BASE + 4
                in al, dx                       \ PC bus interrupt control
                mov ah, # $08                   \ chip bus enable bit
                not ah                          \ we want to clear it
                and al, ah                      \ clear bus enable bit
                out dx, al                      \ put back into register
\F              next
\T              RET
                end-code


code com-out    ( c1 --- )
\T              CLEAR_LABELS
                mov dx, rs232_port              \ get the comport we're using
                mov di, dx                      \ copy into DI
                add dx, # 5
                mov cx, # $FFFF                 \ timeout value
           2 $: dec cx
                j0= 3 $                         \ leave if timed out
                in al, dx                       \ get status port
                and al, # $20
                cmp al, # $20                   \ transmit register empty
                j<> 2 $
           3 $: mov dx, di                      \ back to data port
\F              pop ax                          \ get actual data to send
\T              mov ax, bx
                out dx, al                      \ and then send the byte
\F              next
\T              LOAD_BX
\T              RET
                end-code

code com-in     ( --- c1 )
\T              CLEAR_LABELS
\T              SAVE_BX
                mov dx, rs232_port              \ get the comport we're using
                mov di, dx                      \ copy into DI
                add dx, # 5
                mov cx, # $FFFF                 \ time out value
           4 $: dec cx                          \ start time out
                j0= 5 $                         \ leave if timeout
                in al, dx                       \ get status port
                and al, # $01
                cmp al, # $01                   \ receive buffer full?
                j<> 4 $
           5 $: mov dx, di                      \ back to data port
                in al, dx                       \ get the byte
                sub ah, ah                      \ clear high byte
\F              1push
\T              MOV BX, AX
\T              RET
                end-code

code com-stat   ( --- c1 )
\T              SAVE_BX
                mov dx, rs232_port              \ get the comport we're using
                mov di, dx                      \ copy into DI
                add dx, # 5
                in al, dx                       \ get status port
                sub ah, ah                      \ clear high byte
\F              1push
\T              MOV BX, AX
\T              RET
                end-code

\T HERE-T CONSTANT COM_INT

\T CODE %%com_int  ( --- )         \ COM port interrupt handler
\T              CLEAR_LABELS
\F LABEL com_int
                push es         \ save all of the registers
                push ds
                push dx
                push si
                push di
                push cx
                push bx
                push ax
                mov ax, cs                      \ setup DS: = CS:
\T              add ax, cs: $103 \ address contains offset to base of DS: seg
                mov ds, ax
                inc interrupts word
\ test for a waiting char, and get it into AL
                mov dx, rs232_port              \ get the comport we're using
                mov di, dx                      \ copy into DI
           2 $: add dx, # 5
                in al, dx                       \ get status port
                and al, # $01
                cmp al, # $01                   \ receive buffer full?
                j<> 1 $                         \ ignore if not valid
                mov dx, di                      \ back to data port
                in al, dx                       \ get the byte
\ save away the character and bump buffer counter
                mov bl, bufin                   \ get buffer input pointer
                cmp bl, bufout                  \ is buffer full?
                j=  1 $                         \ if full discard char
                sub bh, bh                      \ clear high part of BX
                add bx, # combuf                \ add base of COMBUF
                mov 0 [bx], al                  \ save the character
                inc bufin byte                  \ bump count
                sub ax, ax
                j 2 $                           \ may need to get additional
\ restore everything and return from interrupt
           1 $: mov al, # EOI
                out # ictla al
                inc intcnt word                 \ bump interrupt counter
                pop ax          \ restore all of the registers
                pop bx
                pop cx
                pop di
                pop si
                pop dx
                pop ds
                pop es
                iret            end-code

variable com1_int#      $0C com1_int# !
variable com2_int#      $0B com2_int# !

0 value commport
2variable comsave1              \ a place to save the old interrupt vector 4
2variable comsave2              \ a place to save the old interrupt vector 3

code rest_com1: ( --- )                 \ restores COM1: interrupt vector
                push ds
                mov dx, comsave1
                mov ax, # $2500
                add ax, com1_int#
                mov ds, comsave1 2+
                int $21
                pop ds
\F              next
\T              RET
                end-code

code rest_com2: ( --- )                 \ restores COM2: interrupt vector
                push ds
                mov dx, comsave2
                mov ax, # $2500
                add ax, com2_int#
                mov ds, comsave2 2+
                int $21
                pop ds
\F              next
\T              RET
                end-code

: ?rest_com1:   ( --- )
\F              defers byefunc
                comsave1 2@ or           \ don't restore vector thats not set
                if      rs232_intoff
                        rest_com1:
                        0.0 comsave1 2!
                then    ;

\F ' ?rest_com1: is byefunc

: ?rest_com2:   ( --- )
\F              defers byefunc
                comsave2 2@ or           \ don't restore vector thats not set
                if      rs232_intoff
                        rest_com2:
                        0.0 comsave2 2!
                then    ;

\F ' ?rest_com2: is byefunc

code set_com1:  ( --- )                 \ Set the COM1: interrupt vector
                push ds
                push es
                mov dx, # com_int
                mov ax, # $2500
                add ax, com1_int#       \ COM1: interrupt vector = 04
                mov cx, cs
                mov ds, cx
                int $21
                pop es
                pop ds
\F              next
\T              RET
                end-code

code set_com2:  ( --- )                 \ Set the COM2: interrupt vector
                push ds
                push es
                mov dx, # com_int
                mov ax, # $2500
                add ax, com2_int#       \ COM2: interrupt vector = 03
                mov cx, cs
                mov ds, cx
                int $21
                pop es
                pop ds
\F              next
\T              RET
                end-code

code save_com1: ( --- )                 \ Save the COM1: interrupt vector IRQ4
\T              PUSH BX
                push es
                mov ax, # $3500
                add ax, com1_int#       \ get the interrupt vector for com1:
                int $21
                mov comsave1 bx
                mov comsave1 2+ es       \ save old vector
                pop es
\F              next
\T              POP BX
\T              RET
                end-code

code save_com2: ( --- )                 \ Save the COM2: interrupt vector IRQ3
\T              PUSH BX
                push es
                mov ax, # $3500
                add ax, com2_int#       \ get the interrupt vector for com2:
                int $21
                mov comsave2 bx
                mov comsave2 2+ es       \ save old vector
                pop es
\F              next
\T              POP BX
\T              RET
                end-code

: com-cnt       ( -- count )  \  get how many bytes are in the buffer
                bufin c@  bufout c@ -  1-  $ff and  ;


: com-get       ( -- char )   \  get a character from the buffer
                com-cnt  0<>                    \ is buffer empty?
                                                \ if not...
                if      bufout c@ 1+ 255 and
                        combuf +   c@           \ get the byte
                        1 bufout +!             \ and increment the pointer
                else    0                       \ return junk (0) if empty
                then  ;


: clr-buf       ( -- )   \ empty the buffer
                1 bufin !   0 bufout !  combuf bufsize erase ;

: .buf          ( --- )     \ display the buffer contents
                base @ hex
                cr ."  next byte " bufout c@ 1+ $ff and combuf + u.
                5 spaces ." last byte " bufin c@ 1- $ff and combuf + u.
                base !     5 spaces
                com-cnt dup 0= if ." empty" else dup . ." bytes,  " then
                    bufsize 1- = if ." full" then
                combuf bufsize dump ;


0 constant nopar   8 constant oddpar  $18 constant evenpar \ parity control
0 constant 1stop   4 constant 2stop                        \ stop bit control
2 constant 7bit    3 constant 8bit                         \ length control

nopar 1stop 8bit + + value parity&bits \ default no parity, 8 bit, 2 stops

: com2:         ( --- )   \ initialize the communications port stuff
                2 =: commport
                $08 imask !
                $63 comEOI !
                cr ." Comm Port #2 selected" cr
                clr-buf
                comsave2 2@ d0=
                if      save_com2:      \ save the current vector into comsave
                        set_com2:       \ set our com1 interrupt vector
                        parity&bits rate 1 com-init      \ init the port 2
                then    ;

: com1:         ( -- )
                1 =: commport
                $10 imask !
                $64 comEOI !
                cr ." Comm Port #1 selected" cr
                clr-buf
                comsave1 2@ d0=
                if      save_com1:
                        set_com1:       \ set our com1 interrupt vector
                        parity&bits rate 0 com-init     \ init the port 1
                then    ;

: terminal      ( -- )
                DECIMAL                         \ always select decimal
\T              MARGIN_INIT
\T              DOSIO_INIT                      \ init EMIT, TYPE & SPACES
                9600 baud                       \ initialize the ports speed
                COM1:
                begin   key?
                        if      key dup $1B =
                                if      drop
                                        ?rest_com1:
                                        cr true abort" Quitting"
                                then    com-out
                        then
                        com-cnt
                        if      com-get emit
                        then
                again   ;

\T \S           STOP HERE IF TARGET COMPILING

: ser_typel     ( seg a1 n1 -- )
                dup>r
                bounds
                ?do     dup i c@L com-out
                loop    drop r> #out +! ;

: ser_out       ( c1 -- )
                com-out #out incr ;

: ser_key       ( -- c1 )
                begin   com-cnt   until
                com-get ;

: toserial      ( -- )  \ re-direct Forth I/O to the serial port COM1:
                slow
                ['] (expect) is expect
                statoff
                9600 baud
                com1:
                ['] ser_typel is typel
                ['] ser_out   is emit
                ['] com-cnt   is key?
                ['] ser_key   is key ;

}

