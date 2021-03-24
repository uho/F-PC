\ LARGEST.SEQ   Return the largest element of an array of words

code largest    ( a1 n1 --- a2 n2 )
                pop cx          \ count of array to search
                pop bx          \ starting address of array
                mov ax, # 0     \ starting highest value = 0
                mov dx, bx      \ highest value addr = start address
          here  cmp 0 [bx], ax
             u> if      mov dx, bx
                        mov ax, 0 [bx]
                then
                add bx, # 2     \ bump to next element
                loop
                2push           end-code

