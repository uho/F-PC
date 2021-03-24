\ BLOCK.SEQ     Tom's Forth virtual block system.       by Tom Zimmer

comment:

  Here is an impementation of a virtual block system.  The constants below,
B/BUF, and #BUFFERS control the record or block size, and the number of
buffers the system uses.  These are defaulted to 1024 byte blocks, and
4 buffers.  A true LRU (least recently used) buffer allocation mechanism
is used, implemented as a bubble up buffer stack.  The least recently used
buffer is always on the bottom of the stack.  As buffers are used or
re-used, they are bubbled immediately up to the top of the stack, destined
to settle to the bottom of the stack if the same record is not accessed
again. This arraingment has been tested with up to 175 buffers and does not
degrade record access performance at or below this value on a 286 machine.

comment;

needs scan.seq

prefix          \ Prefix assembler

only forth also definitions

        \ if b/buf is already defined, then don't define B/BUF,
        \ #BUFFERS or BLOCKHANDLE


defined b/buf nip 0=
#if

        1024 constant b/buf             \ length of each block
           4 constant #buffers          \ number of virtual buffers
    ' seqhandle alias blockhandle       \ just use normal file stuff

#endif

variable blk                       \ current block number

only forth also hidden also definitions

variable cur_buffer#                    \ current buffer # of current block

#buffers 2* constant buflen

variable rec_array b/buf #buffers  * allot      \ an array of blocks
variable rec#s            buflen     allot      \ block # array
variable rec#updt         buflen     allot      \ Update flags
variable rec#use          buflen     allot      \ block bubbleup stack
variable rec#fil          buflen     allot      \ hcb for each block

                                        \ n1 = buffer number
                                        \ a1 = address of buffer
code buf#>bufaddr ( n1 --- a1 )         \ Calculate address a1 of buffer n1.
                pop ax
                mov bx, # b/buf
                mul bx
                add ax, # rec_array
                1push
                end-code

                                        \ n1 = buffer number
                                        \ a1 = buffer address
code >rec#s     ( n1 --- a1 )           \ return the buffer n1's record addr
                pop ax
                shl ax, # 1
                add ax, # rec#s
                1push
                end-code

                                        \ n1 = buffer number
                                        \ a1 = buffer address
code >rec#updt  ( n1 --- a1 )           \ return the buffer n1's update addr
                pop ax
                shl ax, # 1
                add ax, # rec#updt
                1push
                end-code

                                        \ n1 = buffer number
                                        \ a1 = buffer address
code >rec#fil   ( n1 --- a1 )           \ return the buffer n1's file addr
                pop ax
                shl ax, # 1
                add ax, # rec#fil
                1push
                end-code

                                        \ n1 = buffer number to check
                                        \ n2 = is file NOT the same as
                                        \      current, and is n1 <> zero
code chkfil     ( n1 --- n1 n2 )        \ verify file in bufer n1 is current
                pop ax
                push ax
                or ax, ax
            0= if
                        1push           \ can't possibly match, don't try
                        next            \ just push a zero
                then
                shl ax, # 1
                mov bx, # buflen
                sub bx, ax
                add bx, # rec#fil
                mov ax, 0 [bx]
                cmp ax, ' blockhandle >body \ compare blocks hcb with current hcb
             0= if                      \ if zero flag true then they match.
                    mov ax, # false     \ return a false to force a leave
                    1push               \ from BEGIN WHILE REPEAT loop
                then
                mov ax, # true          \ no match, so we need to try again
                1push                   \ return true so we will.
                end-code

: bubbleup      ( n1 --- )              \ move buffer # n1 to end of list
                >r rec#use #buffers r@ scanw dup 0=
                abort" Buffer# number not in buffer list"
                1- 2* >r dup 2+ swap r> cmove   \ move list down except first
                r> rec#use buflen + 2- ! ;      \ stuff first at end of list.

                                        \ n1 = block we are looking for
                                        \ n2 = buffer #
                                        \ f1 = do we have it?, true if we do
: ?gotrec       ( n1 --- <n2> f1 )      \ Do we have block n1 in memory?
                >r rec#s   #buffers
                begin   r@ scanw        \ look for the block
                        chkfil          \ verify the file hcb is the same
                while   2 -1 d+         \ else look for next possibility
                repeat  r> drop
                if      rec#s - 2/ true
                else    drop false
                then    ;

                                        \ n1 = block to positon to
: pos_block    ( n1 --- )               \ Set file pointer to block pos n1
                0 max b/buf *d blockhandle movepointer ;

                                        \ a1 = destination address of read
                                        \ n1 = block number to read
: read_block    ( a1 n1 --- )           \ read block n1 to address a1
                pos_block
                b/buf blockhandle hread b/buf <>
                abort" Error reading block" ;

                                        \ n1 = buffer number
                                        \ n2 = block number to write
: write_block  ( n1 n2 --- )            \ write block n1 to disk
                pos_block
                dup buf#>bufaddr
                b/buf rot >rec#fil @ hwrite b/buf <>
                abort" Error writing block, probably out of disk space." ;

only forth also forth definitions hidden also

                                        \ n1 = block #
                                        \ a1 = bufadr
: buffer        ( n1 --- a1 )           \ Assign least used buffer to rec n1
                rec#use @ >r                    \ find a buffer
                r@ bubbleup                     \ bump to highest priority
                r@ cur_buffer# !                \ set current buffer var
                r@ >rec#updt dup @              \ check update flag
                if      off                     \ clear update flag
                        r@ dup >rec#s @         \ get block #
                        write_block             \ write it
                else    drop                    \ discard, already cleared
                then    r@ >rec#s   !           \ set block #
                blockhandle r@ >rec#fil !           \ set the file hcb
                r> buf#>bufaddr ;               \ calc buffer addr

: empty-buffers ( --- )                 \ clean out the virtual buffers
                rec_array b/buf #buffers * erase
                rec#s    buflen -1 fill
                rec#updt buflen erase
                rec#fil  buflen erase
                rec#use  #buffers 0
                do      i over ! 2+     \ initialize the bubbleup stack
                loop    drop ;

: flush         ( --- )                 \ Write any updated buffers to disk
                #buffers 0
                do      -1 buffer drop
                loop    ;

: update        ( --- )                 \ mark the current block as updated
                cur_buffer# @ >rec#updt on ;

                                        \ n1 = block # to get
                                        \ a1 is address of block # n1
: block         ( n1 --- a1 )           \ Get block n1 into memory
                dup ?gotrec
                if      nip dup >r buf#>bufaddr
                        r@ cur_buffer# ! r> bubbleup
                else    blockhandle >hndle @ 0< abort" No file open"
                        dup buffer dup rot read_block
                then    ;

empty-buffers           \ Initialize the virtual memory arrays

: virtual_init  ( --- )
                defers initstuff
                empty-buffers ;

' virtual_init is initstuff

: .blocks       ( --- )
                cr              ." Record #  "
                rec#s buflen bounds
                do      i @ 5 .r ?cr
             2 +loop    cr      ." Update bit"
                rec#updt buflen bounds
                do      i @ 5 .r ?cr
             2 +loop    cr      ." File HCB  "
                rec#fil buflen bounds
                do      i @ 5 .r ?cr
             2 +loop    cr cr   ." Bubble stk"
                rec#use buflen bounds
                do      i @ 5 .r ?cr
             2 +loop    cr ;

\s              *** stop loading here ***

: tt            ( n1 --- )
                30 08 at
                block drop
                .blocks ;

: ty            ( n1 --- )
                0
                do      i tt update .blocks
                loop    ;

