\ VIDEO.SEQ     Direct VIDEO Screen output              by Tom Zimmer

FILES DEFINITIONS

VARIABLE VIDEO.SEQ

FORTH DEFINITIONS

DECIMAL

VARIABLE ATTRIB   7 ATTRIB !-T          \ display attribute value
VARIABLE VIDEO-SEG                      \ Segment of display
VARIABLE VMODE-VAR      \ Video mode save variable
VARIABLE BLANKING       \ Are we blanking during screen drawing?
 0 VALUE COLS           \ Screen columns
 0 VALUE ROWS           \ Screen rows
 0 VALUE CROWS          \ Cursor DOT rows

CODE ?VMODE     ( --- N1 )              \ Get the video mode from DOS
                MOV AH, # $0F
                INT $10
                SUB AH, AH
                MOV VMODE-VAR AX        \ Save video mode in VMODE-VAR
                PUSH AX
                MOV AX, # $40           \ absolute segment $40:00
                MOV DS, AX              \ goes into DS
                MOV BL, $4A             \ get the # of COLS from    $40:$4A
                MOV CL, $84             \ get the # of ROWS FROM    $40:$84
                MOV BH, $60             \ get the Cursor bottom row $40:$60
                MOV AX, CS              \ restore DS from CS
                MOV DS, AX
                MOV ' CROWS >BODY BH
                MOV ' COLS  >BODY BL
                CMP CL, # 24
              < IF      MOV CL, # 25    \ AT LEAST 25 ROWS
                ELSE    INC CL
                THEN
                MOV ' ROWS >BODY CL
                NEXT            END-CODE

DEFER INITMONO  ' NOOP IS INITMONO      \ INITIALIZATION VECTOR AVAILABLE
DEFER INITCOLOR ' NOOP IS INITCOLOR     \ FOR MONOCHROME AND COLOR

: VMODE.SET     ( --- )
                ?VMODE 7 =
                IF      $0B000   INITMONO
                ELSE    $0B800   INITCOLOR
                THEN    VIDEO-SEG ! ;

CODE BLANK.COLOR ( --- )              \ Blanks screen if in COLOR mode
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
                next            end-code

CODE SHOW.COLOR ( --- )                 \ Re-enable the display
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

