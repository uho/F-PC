\ QVIDEO.SEQ    Fast video routine LINKAGE.             by Tom Zimmer

comment:

  Set F-PC to use a very fast direct screen write routine called VIDEO-TYPE.
This is incredibly fast, but does have at least one side effect. Types beyond
the end of the right screen edge do NOT wrap properly. If you need to have
type do a proper wrap at the right screen edge, specify SLOW below. This
will cause all typing and emitting to go through a DOS WRITE operation.

comment;

DECIMAL

: QTYPEL        ( SEG A1 N1 --- )
                @> PRINTING
                IF      (TYPEL)
                ELSE    VIDEO-TYPEL
                THEN    ;

: FAST          ( --- )
                ['] QTYPEL     IS TYPEL
                FALSE =: ?DOSIO ;

: SLOW          ( --- )
                ['] (TYPEL)    IS TYPEL
                TRUE =: ?DOSIO ;

FAST

\S

CODE COLOR-EMIT ( C1 --- )
                POP AX
                CMP AX, # 13                    \ Is char a Carraige Return?
             0= IF      MOV AH, # $0E
                        INT $10
                        MOV #OUT # $00 WORD     \ reset to left column
                        NEXT
                THEN
                CMP AX, # 10                    \ or is char a LineFeed?
             0= IF      MOV AH, # $0E
                        INT $10
                        MOV AX, #LINE           \ Get line number
                        INC AX
                        INC AX
                        CMP AX, ' ROWS >BODY    \ bump line if not at bottom
                      < IF      DEC AX
                                MOV #LINE AX
                        THEN
                        next
                THEN
                MOV AH, # $09
                MOV BX, ATTRIB
                SUB BH, BH
                MOV CX, # 1
                INT $10
                ADD #OUT # $01 WORD
                NEXT            END-CODE




