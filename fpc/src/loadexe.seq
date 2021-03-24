\ LOADEXE.SEQ

HEADERLESS

HEX

CREATE SUVEC  100 , 0 ,         \ A place to save the entry absolute
                                \ address of Forth. The zero gets plugged
                                \ with the current value of CS.
VARIABLE DPSAVED                \ A place to save DP for restoral
VARIABLE XDPSEGLEN              \ holds the relative value for XSEGLEN
VARIABLE WITHHEADS              \ which is later adjusted in COLD to an
         WITHHEADS ON           \ absolute value.

LABEL  SEXE
        MOV SUVEC 2+ DS         \ Save DS=CS into setup vector for
                                \ use in running forth after all this
                                \ setup is done.

        MOV AX, DS                      \ move DS to AX
        ADD AX, ' #CODESEGS >BODY       \ add CODE size
        ADD AX, ' #OVSEGS   >BODY       \ add OVERLAY segments
        ADD AX, ' #LISTSEGS >BODY       \ and LIST size to get to heads
        MOV ES, AX                      \ move head seg to ES
        MOV CX, YDP                     \ get contents of YDP to CX
        MOV SI, CX                      \ COPY it into SI
        DEC SI
        MOV DI, SI              \ decrement SI and move to DI
                                \ We have set up the END addresses for a move
                                \ backwards, to assure no overlap during
                                \ expansion. THIS IS VERY IMPORTANT !!
        SUB AX, AX              \ Clear AX
        XCHG AX, YSTART         \ Exchange AX, and YSTART clearing it
        ADD AX, SUVEC 2+        \ Add to setup vector, it contains
        PUSH DS                 \ save DS
        MOV DS, AX              \ move AX to DS
        OR CX, CX 0<>           \ if YDP was not zero (0)
        IF      STD             \ moving BACKWARDS !!!
                REPZ
                MOVSB           \ move HEADS to head space
                CLD
        THEN
        POP DS                  \ restore DS
        MOV YSEG ES             \ set YSEG to ES
        MOV DX, DS                      \ move DS to AX
        ADD DX, ' #CODESEGS >BODY       \ Add CODE space segments and
        ADD DX, ' #OVSEGS   >BODY       \ add OVERLAY segments to get
                                        \ to the LIST segment start.
        PUSH DX                 \ Save for later use bye ES
        MOV CX, XDPSEGLEN       \ get contents of XSEGLEN which is the LENGTH
                                \ to move in SEGMENTS.

        MOV BX, DPSTART         \ mov LIST offset segment to BX
                                \ DPSTART in SAVEEXE holds the segment offset
                                \ from CS to where LIST section starts.
        ADD BX, SUVEC 2+        \ Add this to the setup vector which holds
                                \ the current value of CS. The result is the
                                \ absolute segment address of start of LIST.
        ADD BX, CX              \ Adjust source to end of move, will be
        DEC BX                  \ plugged into DS later.
        ADD DX, CX              \ Bump destination segment to end of move
        DEC DX                  \ We are moving BACKWARDS to avoid any
                                \ possibility of an overlay.
        PUSH DS                 \ save DS for later restoral
        STD                     \ Yes we are moving BACKWARDS.
        HERE
                MOV ES, DX      \ Destination segment is held in DX
                MOV DS, BX      \ Source segment is held in BX
                MOV AX, CX      \ Save the segment count in AX
                MOV CX, # 10    \ 10 HEX is 16 decimal, ONE segment
                MOV SI, CX      \ Set source and destination offsets.
                DEC SI
                MOV DI, SI
                REPZ            \ move one 16 byte segment of LIST
                MOVSB           \ to LIST space.
                DEC DX          \ Decrement source and destination holders
                DEC BX
                MOV CX, AX      \ restore CX for LOOP instruction.
        LOOP
        CLD
        POP DS                  \ restore DS and ES
        POP ES
        MOV XSEG ES             \ set XSEG to ES
        MOV AX, DPSAVED         \ Move DPSAVED into DPSTART,
        MOV DPSTART AX          \ for use later in setting DP
        MOV AX, XDPSEGLEN
        MOV XSEGLEN AX          \ Set this for later adjustment to the
                                \ absolute XSEGment value.
        MOV XMOVED # -1 WORD    \ Initialize LIST to already moved. so
                                \ the normal for bootup code wil not move
                                \ the LIST again.
        JMP FAR [] SUVEC        \ Jump to forth cold entry.
        END-CODE

DECIMAL

HEADERS


