\ VIDEO2.SEQ    Actual video output routine             By Tom Zimmer
\ Modified by Robert Berkey to perform LONG operations. 07/03/89 RB


FILES DEFINITIONS

VARIABLE VIDEO2.SEQ

FORTH DEFINITIONS

VARIABLE NOSETCUR       \ When this is on, VIDEO-TYPEL doesn't bother to
                        \ adjust the cursor position for performance reasons

DECIMAL

CODE VIDEO-TYPEL   ( seg string length -- )
                MOV AX, #LINE
                MOV DX, #OUT
                POP BX
                cmp bx, # 0             \ if length = 0 then exit
             <= if
                        add sp, # 4
                        next
                then
                PUSH BX                 \ Put length and #OUT on stack
                PUSH DX
                cmp ax, ' rows >body    \ Clip #LINE to ROWS
            u>= if
                        mov ax, ' rows >body
                        dec ax
                then
                mov #line ax
                PUSH AX
                ADD DX, BX              \ Set length + X position to DL
                cmp dx, ' cols >body    \ Clip #OUT to 80 characters
            u>= if
                        mov dx, ' cols >body
                        dec dx
                then
                mov #out dx
                cmp nosetcur # 0        \ Do we want to spend time setting
             0= if                      \ the cursor position?
                        MOV DH, AL      \ Move Y to DH
                        XOR BX, BX      \ Clear BX
                        MOV AH, # 2
                        PUSH SI         PUSH BP
                        INT $10         \ Move cursor to end of typed string
                        POP BP          POP SI
                then
                MOV AX, ' cols >body    \ calculate actual cursor position
                ADD AX, AX              \ 2 bytes per character
                POP BX          MUL BX          MOV DI, AX
                POP AX          SHL AX, # 1     ADD DI, AX
                POP CX          POP BX
                POP DX  ( ds seg )
                XCHG SI, BX                     \ SI is the source
                PUSH BX                         \ Save SI for later restoral
                PUSH ES                         \ save ES
                PUSH DX ( ds seg )
                MOV BX, # 1                     \ Bit mask for retrace
                MOV AX, VMODE-VAR
                CMP AX, # 7                     \ If video mode <> 7 (mono)
        0<> IF          MOV AX, BLANKING
                        OR AX, AX
            0<> IF      MOV DX, # 986           \ CGA status port
                        BEGIN   IN AL, DX       \ Wait for retrace
                                TEST AL, BL
                    0<> UNTIL
                        MOV DL, # 216           \ Address control reg
                        MOV AL, # 37            \ Disable the CRT
                        OUT DX, AL
                THEN
            THEN
                MOV AH, ATTRIB                  \ display attributes
                MOV ES, VIDEO-SEG               \ destination segment
                cmp cx, ' cols >body
            u>= if                              \ Clip line to COL chars
                        mov cx, ' cols >body
                then
                mov dx, # 132                   \ ABSOLUTE limit of output
                sub dx, cx
                xchg dx, cx
                shl cx, # 1
                POP DS
                add cx, # here $06 +             \ Add base addr of array
                \ add cx, # here $0A +             \ Add base addr of array
                \ MOV DS, TYPESEG                 \ source segment
                jmp cx                          \ JUMP to the right instruction

\ we are blowing out the loop here for speed, it only cost 264 bytes.

        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
        LODSB   STOSW   LODSB   STOSW   LODSB   STOSW   LODSB   STOSW
                POP ES                          \ restore ES
                POP SI                          \ Restore IP
                MOV BX, CS      MOV DS, BX      \ restore DS
                MOV AX, VMODE-VAR
                CMP AX, # 7
        0<> IF          MOV AX, BLANKING
                        OR AX, AX
            0<> IF      MOV DX, # 984           \ CGA mode control reg
                        MOV AL, # $2D           \ Enable byte for mode 3
                        OUT DX, AL
                THEN
            THEN
                NEXT            END-CODE

CODE VIDEO-TYPE ( string length -- )
   POP AX   POP BX   PUSH CS   PUSH BX   PUSH AX   JMP ' VIDEO-TYPEL
   END-CODE

