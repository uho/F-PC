\ DUMP.SEQ      A simple dump utility      Enhancements by Tom Zimmer

DECIMAL

\ The dump utility gives you a formatted hex dump with the ascii
\ text corresponding to the bytes on the right hand side of the
\ screen.  In addition you can use the SM word to set a range of
\ memory locations to desired values.  SM displays an address and
\ its contents.  You can go forwards or backwards depending upon
\ which character you type. Entering a hex number changes the
\ contents of the location.  DL can be used to dump a line of
\ text from a screen.

VARIABLE DUMPSEG

: %DUMPC@       ( A1 --- C1 )
                DUMPSEG @ SWAP C@L ;

DEFER DUMPC@    ' %DUMPC@ IS DUMPC@

I: .2W   ( n -- )   0 <#   # #   #>   TYPE   SPACE   ;

I: D.2W   ( addr len -- )   BOUNDS ?DO   I DUMPC@ .2W   LOOP   ;

I: EMIT.   ( char -- )
   127 AND DUP BL 126 BETWEEN NOT IF DROP '.' THEN EMIT ;

: DLN   ( addr --- )
   CR   ." ณ" DUMPSEG @ 0 <# ':' HOLD # # # # #> TYPE
        DUP 0 <# # # # # #> TYPE ." ณ " 8 2DUP D.2W
   OVER + 8 D.2W ." ณ"   16 BOUNDS
   ?DO   I DUMPC@ EMIT.   LOOP  ." ณ" ;

I: .HEAD ( addr len -- addr' len' )
        OVER 15 AND
        CR ." ีอออออออออั"
        49 0 DO ." อ" LOOP ." ั" 16 0 DO ." อ" LOOP ." ธ"
        CR ." ณ SEG:OFF ณ "
        16 0 DO DUP I + 15 AND 2 .R SPACE LOOP ." ณ"
        16 0 DO DUP I + 15 AND 1 .R LOOP DROP  ." ณ"
        CR ." ฦอออออออออุ"
        49 0 DO ." อ" LOOP ." ุ" 16 0 DO ." อ" LOOP ." ต" ;

I: .FOOT ( --- )
        CR ." ิอออออออออฯ"
        49 0 DO ." อ" LOOP ." ฯ" 16 0 DO ." อ" LOOP ." พ" CR ;

: LDUMP         ( SEG addr len -- )
                ROT DUMPSEG !
                BASE @ -ROT HEX .HEAD BOUNDS
                DO      I DLN  KEY? ?LEAVE
            16 +LOOP    BASE !  .FOOT ;

: DUMP          ( A1 N1 --- )
                ?CS: -ROT LDUMP ;

: YDUMP         ( A1 N1 --- )
                YSEG @ -ROT LDUMP ;

: XDUMP         ( SEG N1 --- )
                SWAP +XSEG 0 ROT LDUMP ;

: DU            ( addr -- addr+64 )
                DUP 64 DUMPSEG @ -ROT LDUMP 64 +   ;

