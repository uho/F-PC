\ SCANW.SEQ     Scan a list of words for a word         by Tom Zimmer

comment:

        Works like the normal SCAN, but scans a list of WORDS for a WORD.
        That is you must divide the space to be searched by 2 to obtain
        the length to search

comment;
                                        \ a1 = scan start address
                                        \ w1 = length to scan (words)
                                        \ w2 = word we are looking for
                                        \ a2 = address where w2 was found
                                        \ w3 = remaining search length (WORDS)
code  scanw     ( a1 w1 w2 --- a2 w3 )  \ Scan array a1 for word w2.
                pop ax          pop cx
          cx<>0 if
                    pop di          push es
                    mov es, sseg
                    repnz           scasw
                    pop es
                 0= if
                    inc cx
                    dec di          dec di
                 then
                    push di         push cx
                    next
            then                        \ zero search length exit
                push cx
                next
                end-code


\ Scan for char BACKWARDS starting at addr+len, back through len bytes
\ before addr, returning addr' and len' of char.

code -scan      ( addr len char -- addr' len' )
                pop ax          pop cx
          cx<>0 if
                   pop di
                   mov dx, es      mov es, sseg
                   std
                   repnz           scasb
                   cld
                   mov es, dx
                0= if
                   inc cx          inc di
                then
                   push di         push cx
                   next
             then
                push cx         next            end-code

\ Skip occurances of char BACKWARDS starting at addr, back through
\ addr-len, returning addr' and len' of char.

code -skip      ( addr len char -- addr' len' )
                pop ax          pop cx
          cx<>0 if
                pop di
                mov dx, es      mov es, sseg
                std
                repz            scasb
                cld
                mov es, dx
            0<> if
                inc cx          inc di
            then
                push di         push cx
                next
        then    push cx         next            end-code



