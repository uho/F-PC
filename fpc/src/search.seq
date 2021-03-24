\ SEARCH.SEQ    String search routine.                  by Roger Bird

comment:
  This file may be removed, but it is used by WORDS.SEQ, and ENVIRON.SEQ,
which is itself used by EXEC.SEQ.

  The String manipulation primitives include string comparison and
searching. The string search implemented is used in the editor
to find the desired string.  The only unusual thing about it is
the presence of a variable called CAPS, which determines
whether or not to ignore the case of the subject and pattern
strings.  If case is ignored then A-Z = a-z.  The default is
ignore case.

        Much thanks to Roger Bird for this improved impilmentation
        of SEARCH, it is about 20 % faster than my previous improved
        version, and is 10 times faster in the case where the string
        being searched for starts with a space. It is also 140 bytes
        smaller than the old implimentation.

 11/04/91 15:00:33.61 tjz

  Modified SEARCH to perform case conversion properly, by using a
translation table rather than ORing each character with BLANK.

comment;

CODE SEARCH     ( sadr slen dadr dcnt -- offset found? )
  pop cx   pop di
  pop bx   pop ax
  cmp bx, # 0           \ if search length "slen" is 0 then return true
  0= if         push bx
                mov ax, # true
                1push
  then
  cmp cx, bx
  u< if         push cx
                mov ax, # false
                1push
  then
  push di
  push es   mov es, sseg   push si   push bp   mov si, ax
  dec bx dec cx
  mov dx, bx
        MOV BP, BX
        MOV BX, # ATBL
  begin
        CMP BP, CX
  u<= while
            begin       CMP CAPS # 0 word
                    0<> IF      mov AL, es: 0 [di+BP]
                                XLAT
                                MOV AH, AL
                                mov al,     0 [si+BP]
                                XLAT
                                CMP AH, AL
                        ELSE    mov al,     0 [si+BP]
                                CMP AL, es: 0 [di+BP]
                        THEN
        0= while  DEC BP
                0< if   pop bp   pop si   pop es
                        pop dx   sub di, dx
                        push di  mov ax, # $FFFF   1push
                then
      repeat
        inc di
        JCXZ 0 $        \ if CX has been decremented to zero, then all done
        dec cx  mov BP, dx
  repeat
0 $:
  pop bp      pop si       pop es
  pop dx      sub di, dx   push di
  sub ax, ax  1push
  end-code

