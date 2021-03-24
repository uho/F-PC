\ SEDCODE.SEQ   The Assembly code needed for SED        by Tom Zimmer

editor definitions

code tl:        ( a1 -- lseg a1 )       \ add list segment base to stack
                pop ax                  \ under a1 in prep for long operation
                mov dx, ' lseg >body
                2push
                end-code

code tl+        ( a1 --- a2 )   \ a2 = a1 + 2, the list entry size in bytes
                pop ax          add ax, # 2
                1push           end-code

code tl-        ( a1 --- a2 )   \ a2 = a1 - 2, the list entry size in bytes
                pop ax          sub ax, # 2
                1push           end-code

code tl*        ( n1 -- n2 )    \ n2 = n1 * 2, the list entry size in bytes
                pop ax          shl ax, # 1
                1push           end-code

code getdiskfree        \ return free space from disk
                ( dv --- avail.clusters bytes/sec secs/cluster )
                mov ah, # 54
                pop dx
                int 33
                push bx
                push cx
                1push
                end-code

code >lineptr   ( n1 --- a1 )
                pop ax
                shl ax, # 1
                1push
                end-code

code lineptr   ( --- a1 )
                mov ax, ' curline >body
                shl ax, # 1             \ shift left by 2, for word size
                1push                   \ of list pointer table.
                end-code

code tl:@       ( a1 -- n1 )
                pop bx          mov ds, ' lseg >body
                mov ax, 0 [bx]
                mov bx, cs      mov ds, bx
                1push           end-code

code tl:!       ( n adr -- )
                pop bx          mov ds, ' lseg >body
                pop ax          mov 0 [bx], ax
                mov bx, cs      mov ds, bx
                NEXT            END-CODE

code tl:+!      ( n adr -- )
                pop bx          mov ds, ' lseg >body
                pop ax          add 0 [bx], ax
                mov bx, cs      mov ds, bx
                NEXT            END-CODE

code endtst?   ( --- f1 )
                mov bx, ' curline >body
                shl bx, # 1             \ shift left by 2, for word size
                sub bx, # 2
                mov ds, ' lseg >body
                mov ax, 0 [bx]
                mov bx, cs      mov ds, bx
                cmp ax, ' tend >body
           u>=  if      sub ax, ax
                        1push
                then
                mov ax, # true
                1push           end-code

code rmsave     ( a1 --- a1 c1 )        \ fetch contents of a1 and returns
                                        \ as c1 and accumulate max as RMMAX
                pop bx
                push bx
                sub ax, ax
                mov al, 0 [bx]
                push ax
                sub ax, # 2
                cmp ax, ' rmmax >body
             <= if      next
                then
                mov ' rmmax >body ax
                next            end-code

code #lineseg   ( n1 --- seg )        \ n1 = line, seg = line memory segment
                pop bx
                shl bx, # 1
                mov ds, ' lseg >body
                mov ax, 0 [bx]
                mov bx, cs
                mov ds, bx              \ restore DS
                1push           end-code

code clipline ( a1 n1 --- a2 n3 )
                pop ax                          \ AX = n1 = text length
                pop dx                          \ DX = a1 = text address
                mov bx, ' winoff >body          \ get window offset
                add dx, bx                      \ add to text address
                sub ax, bx                      \ subtract from text length
                cmp ax, # 0
             0< if      mov ax, # 0             \ limit to zero length
                then
                mov bx, ' last.textcol >body
                sub bx, ' first.textcol >body
                add bx, # 1
                cmp ax, bx                      \ no longer than text.width
              > if      mov ax, bx
                then
                2push           end-code

code window.left ( --- n1 )     \ absolute left edge of window
                mov ax, ' first.textcol >body
                sub ax, # 1
                1push           end-code

code window.right ( --- n1 )    \ absolute right edge of window.
                mov ax, ' last.textcol >body
                add ax, # 1
                1push           end-code

code text.width ( --- n1 )      \ width of text portion of window
                mov ax, ' last.textcol >body
                sub ax, ' first.textcol >body
                add ax, # 1
                1push           end-code

code len-accum  ( n1 --- n1 )   \ sum in byte length n1 to double file length
                mov ax, # 0     \ in double length variable CURRENTSIZE
                pop dx
                push dx
                mov bx, # currentsize
                add 2 [bx], dx
                adc 0 [bx], ax
                next            end-code

\ adjust the line pointer table lines n3 through n2 by n1 segs.

code adj_ptr_lines ( n1 n2 n3 --- )
                pop bx                  \ starting line number
                pop cx                  \ limit line number
                sub cx, bx              \ calc times through loop
                dec cx                  \ correct count
                shl bx, # 1             \ convert to word offset
                pop ax                  \ incrementer
                mov ds, ' lseg >body    \ segment where located
          1 $:  add 0 [bx], ax          \ add incrementer to pointer entry
                add bx, # 2             \ bump to next entry
                dec cx                  \ decrement counter
                j0>= 1 $                 \ are we done
                mov bx, cs
                mov ds, bx
                next            end-code

forth definitions

