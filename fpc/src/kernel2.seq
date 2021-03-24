\ KERNEL2.SEQ   More kernel stuff

FILES DEFINITIONS

VARIABLE KERNEL2.SEQ

FORTH DEFINITIONS

USER DEFINITIONS
VARIABLE  TOS         ( top of stack )
VARIABLE  ENTRY       ( entry point, contains machine code )
VARIABLE  LINK        ( link to next task )
VARIABLE  ES0         ( initial ES: segment )
VARIABLE  SP0         ( initial parameter stack )
VARIABLE  RP0         ( initial return stack )
VARIABLE  DP          ( dictionary pointer )
VARIABLE  OFFSET      ( relative to absolute disk block 0 )
VARIABLE  BASE        ( for numeric input and output )
VARIABLE  HLD         ( points to last character held in pad )
VARIABLE  PRINTING    ( indicates if printing is enabled )

   DEFER  EMIT        ( send a character to ouput device )
   DEFER  KEY?        ( test if a character is ready to be received )
   DEFER  KEY         ( get the next character from the keyboard )
   DEFER  TYPE        ( send a string of characters to the console )
   DEFER  TYPEL       ( send a string from extended memory to console )

META DEFINITIONS
VARIABLE  PRIOR       ( used for dictionary searches )
VARIABLE  STATE       ( compilation or interpretation )
VARIABLE  WARNING     ( give user duplicate warnings if on )
VARIABLE  DPL         ( numeric input punctuation )
VARIABLE  R#          ( editing cursor position )
VARIABLE  CSP         ( holds stack pointer for error checking )
VARIABLE  LAST        ( points to nfa of latest definition )
VARIABLE  CURRENT     ( vocabulary which gets definitions )
12 CONSTANT #VOCS     ( the number of vocabularies to search )
VARIABLE  CONTEXT     ( vocabulary searched first )
HERE THERE #VOCS 2* DUP ALLOT CS:ERASE

VARIABLE  'TIB        ( address of terminal input buffer )
VARIABLE  WIDTH       ( width of name field )
VARIABLE  VOC-LINK    ( points to newest vocabulary )
VARIABLE  >IN         ( offset into input stream )
VARIABLE  SPAN        ( number of characters expected )
VARIABLE  #TIB        ( number of characters to interpret )
VARIABLE  END?        ( true if input stream exhausted )
VARIABLE  #OUT        ( number of characters emitted )
VARIABLE  #LINE       ( the number of lines sent so far )

VARIABLE XDP          ( offset to next available location in list space )
VARIABLE XDPSEG       ( segment to next available location in list space )
VARIABLE YDP          ( offset to next available location in head space )
VARIABLE YSTART       ( offset to beginning of head space in .COM file )
VARIABLE DPSTART      ( beginning of list space in .COM or .EXE file )
VARIABLE XSEGLEN      ( length of list space in segments )
VARIABLE XMOVED       ( flag to tell if list has been moved )
VARIABLE SSEG         ( search & scan segment )
VARIABLE PHEAD        ( pointer linked list head pointer )
VARIABLE #PARS        ( number of paragraphs already used by the system )

0  VALUE SEQHANDLE    ( the sequential handle pointer )
VARIABLE LOADLINE     ( line # last read by LINEREAD )

32 CONSTANT BL          \ ASCII space
 8 CONSTANT BS          \ ASCII backspace
 7 CONSTANT BELL        \ ASCII bell

VARIABLE CAPS           \ Flag: if true, convert names to upper case.
VARIABLE >IN_WORD       \ offset in line to word just parsed out with WORD

CODE FILL       (  start-addr count char -- )
\ Fill each byte of memory in the specified address range with "char".
                CLD             MOV BX, DS
                POP AX          POP CX          POP DI
                PUSH ES         MOV ES, BX
                REPNZ           STOSB           POP ES
                NEXT            END-CODE

CODE LFILL      (  seg start-addr count char -- )
\ Fill each byte of memory in the specified address range with "char".
                CLD             POP AX          POP CX
                POP DI          POP BX
                PUSH ES         MOV ES, BX
                REPNZ           STOSB           POP ES
                NEXT            END-CODE

: ERASE         ( addr len -- )   \ Put zeros in the area at addr.
                0 FILL   ;
: BLANK         ( addr len -- )   \ Put ASCII spaces in the area at addr.
                BL FILL   ;

CODE COUNT      ( addr -- addr+1 len )
\ Convert from the address of a counted string to an address and count.
                POP BX          SUB AX, AX      MOV AL, 0 [BX]
                INC BX          PUSH BX
                1PUSH           END-CODE

CODE LENGTH     ( addr -- addr+2 len )  \ really word count
\ Similiar to COUNT , except that the count is in a word, not a byte.
                POP BX          MOV AX, 0 [BX]
                ADD BX, # 2
                PUSH BX
                1PUSH           END-CODE

                                        \ 07/03/89 RB
CODE COUNTL     ( seg addr -- seg addr+1 len )
\ Like COUNT, but works with a LONG (seg/offset) address.
                POP BX          POP DS
                XOR AX, AX      MOV AL, 0 [BX]
                INC BX
                PUSH DS         PUSH BX
                MOV DX, CS      MOV DS, DX
                1PUSH           END-CODE

: MOVE          ( from to len -- )
\ Move "len" bytes from "from" address to "to" address, non-destructively.
                -ROT   2DUP U< IF   ROT CMOVE>   ELSE   ROT CMOVE   THEN ;

DECIMAL

CREATE ATBL     \ Uppercase translation table
 0  C,   1  C,   2  C,   3  C,   4  C,   5  C,   6  C,   7  C,
 8  C,  32  C,  10  C,  11  C,  12  C,  13  C,  14  C,  15  C,
16  C,  17  C,  18  C,  19  C,  20  C,  21  C,  22  C,  23  C,
24  C,  25  C,  26  C,  27  C,  28  C,  29  C,  30  C,  31  C,
32  C,  '!' C,  '"' C,  '#' C,  '$' C,  '%' C,  '&' C,  ''' C,
'(' C,  ')' C,  '*' C,  '+' C,  ',' C,  '-' C,  '.' C,  '/' C,
'0' C,  '1' C,  '2' C,  '3' C,  '4' C,  '5' C,  '6' C,  '7' C,
'8' C,  '9' C,  ':' C,  ';' C,  '<' C,  '=' C,  '>' C,  '?' C,
'@' C,  'A' C,  'B' C,  'C' C,  'D' C,  'E' C,  'F' C,  'G' C,
'H' C,  'I' C,  'J' C,  'K' C,  'L' C,  'M' C,  'N' C,  'O' C,
'P' C,  'Q' C,  'R' C,  'S' C,  'T' C,  'U' C,  'V' C,  'W' C,
'X' C,  'Y' C,  'Z' C,  '[' C,  '\' C,  ']' C,  '^' C,  '_' C,
'`' C,  'A' C,  'B' C,  'C' C,  'D' C,  'E' C,  'F' C,  'G' C,
'H' C,  'I' C,  'J' C,  'K' C,  'L' C,  'M' C,  'N' C,  'O' C,
'P' C,  'Q' C,  'R' C,  'S' C,  'T' C,  'U' C,  'V' C,  'W' C,
'X' C,  'Y' C,  'Z' C,  '{' C,  '|' C,  '}' C,  '~' C,  127 C,
\ Characters above 127 are translated to below 127
 0  C,   1  C,   2  C,   3  C,   4  C,   5  C,   6  C,   7  C,
 8  C,   9  C,  10  C,  11  C,  12  C,  13  C,  14  C,  15  C,
16  C,  17  C,  18  C,  19  C,  20  C,  21  C,  22  C,  23  C,
24  C,  25  C,  26  C,  27  C,  28  C,  29  C,  30  C,  31  C,
32  C,  '!' C,  '"' C,  '#' C,  '$' C,  '%' C,  '&' C,  ''' C,
'(' C,  ')' C,  '*' C,  '+' C,  ',' C,  '-' C,  '.' C,  '/' C,
'0' C,  '1' C,  '2' C,  '3' C,  '4' C,  '5' C,  '6' C,  '7' C,
'8' C,  '9' C,  ':' C,  ';' C,  '<' C,  '=' C,  '>' C,  '?' C,
'@' C,  'A' C,  'B' C,  'C' C,  'D' C,  'E' C,  'F' C,  'G' C,
'H' C,  'I' C,  'J' C,  'K' C,  'L' C,  'M' C,  'N' C,  'O' C,
'P' C,  'Q' C,  'R' C,  'S' C,  'T' C,  'U' C,  'V' C,  'W' C,
'X' C,  'Y' C,  'Z' C,  '[' C,  '\' C,  ']' C,  '^' C,  '_' C,
'`' C,  'A' C,  'B' C,  'C' C,  'D' C,  'E' C,  'F' C,  'G' C,
'H' C,  'I' C,  'J' C,  'K' C,  'L' C,  'M' C,  'N' C,  'O' C,
'P' C,  'Q' C,  'R' C,  'S' C,  'T' C,  'U' C,  'V' C,  'W' C,
'X' C,  'Y' C,  'Z' C,  '{' C,  '|' C,  '}' C,  '~' C,  127 C,

CODE UPC        ( char -- char' )
\ Convert a character to upper case.
                POP AX
                MOV BX, # ATBL
                XLAT
                1PUSH
                END-CODE

CODE UPPER      ( addr len -- )         
\ Convert a string to upper case.
                POP CX                  \ get length
                POP DI                  \ and starting address
                PUSH SI                 \ save IP
                MOV DX, ES              \ and LIST POINTER
                MOV BX, DS
                MOV ES, BX              \ set ES to DS
                MOV SI, DI              \ set SI to DI
                MOV BX, # ATBL          \ loadup BX with table
                CLD                     \ clear direction flag
          CX<>0 IF
                        HERE                    \ get a char and traslate it
                                LODSB   XLAT
                                STOSB
                        LOOP                    \ until all chars are done
                THEN
                MOV ES, DX              \ restore ES=LIST
                POP SI                  \     and SI=IP
                NEXT    END-CODE

CODE ?UPPERCASE ( a1 -- a1 )           
\ Conditionally convert a counted string to upper case
                MOV CX, CAPS            \ test CAPS variable
  CX<>0 IF                              \ leave if CAPS is not on
                POP DI
                PUSH DI                 \ get a copy of address a1
                SUB CX, CX
                MOV CL, 0 [DI]
                INC DI                  \ Addr and Cnt in DI & CX
                PUSH SI                 \ save IP
                MOV DX, ES              \ and LIST POINTER
                MOV BX, DS
                MOV ES, BX              \ set ES to DS
                MOV SI, DI              \ set SI to DI
                MOV BX, # ATBL          \ loadup BX with table
                CLD                     \ clear direction flag
          CX<>0 IF
                        HERE                    \ get a char and traslate it
                                LODSB   XLAT
                                STOSB
                        LOOP                    \ until all chars are done
                THEN
                MOV ES, DX              \ restore ES=LIST
                POP SI                  \     and SI=IP
                NEXT
        THEN
                NEXT
                END-CODE

CODE HERE       ( -- adr )
\ Return the address of the top of the dictionary.
                MOV BX, UP
                PUSH DP [BX]
                NEXT
                END-CODE

CODE PAD        ( -- adr )
\ Return the address of a floating temporary storage area.
                MOV BX, UP
                MOV AX, DP [BX]
                ADD AX, # 80
                1PUSH           END-CODE

CODE -TRAILING  ( addr len -- addr len1 )
\ The length of string is conditionally reduced by the number of trailing
\ blanks.
                POP CX
                POP DI          PUSH DI
          CX<>0 IF      MOV AX, DS
                        PUSH ES
                        STD
                        MOV ES, AX
                        ADD DI, CX
                        DEC DI
                        MOV AL, # $20
                        REPE SCASB
                    0<> IF      INC CX
                        THEN
                        CLD
                        POP ES
                THEN
                PUSH CX
                NEXT            END-CODE

CODE COMP       ( addr1 addr2 len -- -1 | 0 | 1 )
\ Compare two strings.  If equal, return 0.  If str1 < str2, return -1.
\ If str1 > str2, return 1 .
                MOV DX, SI      POP CX
                POP DI          POP SI
          CX<>0 IF      PUSH ES
                        MOV ES, SSEG
                        REPZ CMPSB
                    0<> IF
                             0< IF      MOV CX, # -1
                                ELSE    MOV CX, # 1
                                THEN
                        THEN
                        POP ES
                THEN
                MOV SI, DX
                PUSH CX
                NEXT            END-CODE

CODE CAPS-COMP  ( addr1 addr2 len -- -1 | 0 | 1 )
\ Perform a comparison of two strings, but ignore Case differences.
                MOV DX, SI      POP CX
                POP DI          POP SI
                PUSH ES         MOV ES, SSEG
                BEGIN
                    JCXZ  0 $
                    MOV     AH, 0 [SI]      INC SI
                    MOV ES: AL, 0 [DI]      INC DI
                    OR AX, # $02020         CMP AH, AL
                    JNE 1 $                 DEC CX
                AGAIN
        1 $: 0< IF
                   MOV CX, # -1
                ELSE
                   MOV CX, # 1
                THEN
        0 $:    MOV SI, DX
                POP ES
                PUSH CX
                NEXT            END-CODE

: COMPARE       ( addr1 addr2 len -- -1 | 0 | 1 )
\ Compare two strings.  If CAPS is true, ignore case.
                CAPS @ IF   CAPS-COMP   ELSE   COMP   THEN   ;

CODE ?CS:       ( -- cs )
\ Return the code segment CS
                PUSH CS         NEXT            END-CODE

CODE ?DS:       ( -- cs )
\ Return the code segment CS
                PUSH DS         NEXT            END-CODE

CODE ?ES:       ( -- es )
\ Return the extra segment ES
                PUSH ES         NEXT            END-CODE

CODE @L         ( seg addr -- word )
\ Load a 16 bit word from the specified segment and offset.
                POP BX          POP DS          MOV AX, 0 [BX]
                MOV BX, CS      MOV DS, BX
                1PUSH           END-CODE

CODE C@L        ( seg addr -- byte )
\ Load an 8 bit byte from the specified segment and offset.
                POP BX          POP DS          MOV AL, 0 [BX]
                XOR AH, AH      MOV BX, CS      MOV DS, BX
                1PUSH           END-CODE

CODE C!L        ( byte seg adr )
\ Store the byte at the specified segment and offset.
                POP BX          POP DS          POP AX
                MOV 0 [BX], AL  MOV BX, CS      MOV DS, BX
                NEXT            END-CODE

CODE !L         ( n seg adr -- )
\ Store the 16 bit word n at the specified segment and offset.
                POP BX          POP DS          POP AX
                MOV 0 [BX], AX  MOV BX, CS      MOV DS, BX
                NEXT            END-CODE

CODE <BDOS>     ( n fun -- m )
\ Perform a simple DOS call.  fun is the function number, and n
\ is the value of the DX register.  The result code is pushed as m .
                POP AX          MOV AH, AL      POP DX
                INT $21         SUB AH, AH
                1PUSH           END-CODE

DEFER BDOS      ' <BDOS> IS BDOS
\ A defered DOS call.

CODE BDOS2      ( CX DX AX -- CX DX AX )
\ Similiar to BDOS, except that an additional register, CX , is used.
                POP AX          POP DX          POP CX
                MOV AH, AL      INT $21
                PUSH CX         PUSH DX         PUSH AX
                NEXT            END-CODE

: OS2           BDOS2 255 AND ;

VARIABLE BIOSCHAR       \ Holds the char from BIOS on scan by BIOSKEY?
VARIABLE BIOSKEYVAL     \ Holds the key value from BIOSKEY

CODE BIOSKEY?   ( -- f1 )
\ Return a true flag if a key, other than control break, has been pressed.
        BEGIN
                MOV AH, # 1
                PUSH SI         PUSH BP
                INT $16
                POP BP          POP SI
                MOV BIOSCHAR AX
          0= IF
                MOV AX, # 0
                1PUSH
             THEN
                CMP AX, # 0     \ Ignore Control Break keys
     0= WHILE
                MOV AH, # 0     \ That is, throw them away
                PUSH SI         PUSH BP
                INT $16
                POP BP          POP SI
        REPEAT
                MOV AX, # -1
                1PUSH           END-CODE

CODE BIOSKEY    ( -- c1 )
\ Return the value of the next key, other than control break.
        BEGIN
                MOV AH, # 0
                PUSH SI         PUSH BP
                INT $16
                POP BP          POP SI
                CMP AX, # 0             \ Ignore Control BREAK, 00 Hex.
    0<> UNTIL
                MOV BIOSKEYVAL AX
                1PUSH           END-CODE

DEFER KEYFILTER ' NOOP IS KEYFILTER     \ Pre-filter keys before passing on.

DEFER BGSTUFF   ' NOOP IS BGSTUFF       \ BACKGROUND STUFF

: (KEY?)        ( -- f )
\ Returns TRUE if user depressed a key.  Otherwise, FALSE.
                BGSTUFF BIOSKEY? ;

: (KEY)         ( -- char )
\ Wait until the user presses a key, then return its value.
                BEGIN   PAUSE KEY? UNTIL
                BIOSKEY DUP 127 AND 0=
                IF      FLIP DUP 3 =
                        IF      DROP 0          \ allow a NULL
                        ELSE    127 AND 128 OR
                        THEN
                ELSE    255 AND
                THEN    KEYFILTER ;

DEFER OUTPAUSE  ( ' PAUSE ) ' NOOP IS OUTPAUSE
\ A defered word for background tasks while sending characters to screen.

DEFER CONSOLE
\ A defered word for sending characters to the screen.

CODE CMOVEL     ( sseg sptr dseg dptr cnt )
\ Move "cnt" characters from source segment and offset to destination
\ segment and offset.
                CLD             MOV BX, SI
                POP CX          POP DI
                POP AX          POP SI
                POP DS          PUSH ES         MOV ES, AX
\                test di, # 1
\                jz 0 $
\                jcxz 0 $
\                movsb
\                dec cx
\        0 $:    shr cx, # 1
\                REPNZ           MOVSw
\                rcl cx, # 1
                repnz           movsb
                POP ES
                MOV AX, CS      MOV DS, AX
                MOV SI, BX
                NEXT            END-CODE

CODE CMOVEL>    ( sseg sptr dseg dptr cnt )
\ Similiar to CMOVEL , except move is in the "reverse" direction,
\ i.e., from high memory to low memory.
                MOV BX, SI
                POP CX          DEC CX
                POP DI          POP AX
                POP SI          POP DS
                PUSH ES         MOV ES, AX
                ADD DI, CX      ADD SI, CX      INC CX
                STD
                REPNZ           MOVSB
                CLD
                POP ES
                MOV AX, CS      MOV DS, AX
                MOV SI, BX
                NEXT            END-CODE

\ **********************************************************************
\ THERE MUST BE AT LEAST 160 BYTES BETWEEN THE SOURCE AND DESTINATION
\ PARAGRAPHS FOR "CMOVE-PARS" AND "CMOVE-PARS>" TO WORK PROPERLY.
\ **********************************************************************

: CMOVE-PARS    ( source-par destination-par length-pars --- )
\ move paragraphs from source to destination, of length.
\ source and destination must be greater than 160 bytes (10 paragraphs) apart.
                ?dup                    \ is there anything to move?
        if      0 10 um/mod             \ calculate blocks of 10 segments
                swap >r                 \ save the remainder for later
                0
                ?do     2dup 0 tuck     \ setup as seg-off seg-off
                        160 cmovel      \ move 160 bytes
                        10 10 d+        \ adj for next move
                loop    0 tuck          \ prepare for final move
                r> 16 * cmovel          \ move remainder of data
        else    2drop                   \ nothing to move
        then    ;

: CMOVE-PARS>   ( source-par destination-par length-pars --- )
\ reverse move paragraphs from source to destination, of length.
\ source and destination must be greater than 160 bytes (10 paragraphs) apart.
                ?dup                    \ is there anything to move?
        if      dup>r dup d+            \ adjust to end for backwards move
                r>
                0 10 um/mod             \ calculate blocks of 10 segments
                >r                      \ save main move blocks
                                        \ move remainder first
                dup>r dup d-            \ adj from end back by remainder
                2dup 0 tuck r> 16 * cmovel
                r> 0
                ?do     10 10 d-        \ adjust for this move
                        2dup 0 tuck     \ setup as seg-off seg-off
                        160 cmovel      \ move 160 bytes
                loop                    \ -- seg seg
        then    2drop ;                 \ cleanup stack

$01000 VALUE #CODESEGS \ Number of segments needed for CODE.            64k
$02000 VALUE #LISTSEGS \ Number of segments needed for : definitions.   96k
$01000 VALUE #HEADSEGS \ Number of segments needed for HEADS.           64K

\ The following values are for overlays. They default to zero, no overlays

$00000 VALUE #OVSEGS   \ Number of segments needed for OVERLAYS.         0k
$00000 VALUE #OVBYTES  \ Number of BYTES    needed for OVERLAYS.         0k


: MEMCHK        ( f1 -- )
\ If flag is true, Terminate execution and return to DOS with error message.
                IF      ." Insufficient Memory"
                        0 0 BDOS
                THEN ;

CODE DOS_DEALLOC ( n1 -- f1 )
\ n1 = block to de-allocate, f1 = 0 is ok.
\ f1 = 9 means invalid block address.
                MOV AH, # $49 
                POP DX
                PUSH ES         MOV ES, DX      INT $21
             U< IF
                SUB AH, AH
             ELSE
                MOV AX, # 0
             THEN
                POP ES          1PUSH           END-CODE

CODE DOS_ALLOC  ( n1 -- n2 n3 f1 )
\ n1 = size needed, n3 = segment
\ n2 = largest segment available
\ f1 =  8 not enough memory.
                MOV AH, # $48            
                POP BX
                INT $21
                PUSH BX         PUSH AX
             U< IF
                SUB AH, AH
             ELSE
                MOV AX, # 0
             THEN
                1PUSH           END-CODE

CODE DOS_SETBLOCK ( seg siz -- f1 )
\ Re-adjust the memory block specified by "seg" to the new size "siz"
\ in segments.
                POP BX                  \ get new size
                MOV AH, # $4A           \ setblock call
                POP DX
                PUSH ES
                MOV ES, DX
                INT $21
             U< IF      SUB AH, AH
                ELSE    MOV AX, # 0
                THEN
                POP ES
                1PUSH           END-CODE

: DOS_MAXBLOCK  ( -- max_segs )
\ Return max_segs of how much more memory can be allocated.
                -1 DOS_ALLOC 2DROP ;

DEFER DEALLOC   ' DOS_DEALLOC  IS DEALLOC
DEFER ALLOC     ' DOS_ALLOC    IS ALLOC
DEFER SETBLOCK  ' DOS_SETBLOCK IS SETBLOCK
DEFER MAXBLOCK  ' DOS_MAXBLOCK IS MAXBLOCK
DEFER CURSORSET ' NOOP         IS CURSORSET

: DOSVER        ( -- n1 )
\ Get the DOS version number.
                0 $030 BDOS $0FF AND ;

                                        \ 07/03/89 RB
CODE +XSEG      ( n1 -- n2 )            \ Add XSEG to n1, returning n2.
                POP AX
                ADD AX, XSEG
                1PUSH           END-CODE

\ the base structure for all pointers to follow

0.0 POINTER F-PC

: SETYSEG       ( -- )
\ Sets head segment + more space
                ?CS: SSEG !
                XSEGLEN @ +XSEG XDPSEG !
                XDP OFF
                DPSTART @ DP !
                DOSVER 2 <
                IF      ." Must have DOS 2.x or higher."
                        0 0 BDOS
                THEN
                #CODESEGS #OVSEGS + #LISTSEGS + #HEADSEGS +
                ['] F-PC >BODY 4 + !            \ set the size of F-PC
                0POINTERS                       \ clear out the pointer list
                F-PC 0= MEMCHK                  \ adjust memory usage
                #OUT 0! $018 ( 24 DECIMAL ) #LINE !
                CURSORSET ;

CODE YHERE      ( -- adr )
\ The next available location in "Head" space.
                PUSH YDP        NEXT
                END-CODE

CODE YS:        ( w -- yseg w )
\ Insert the base of the head segment under the offset at the top.
                POP AX          PUSH YSEG
                1PUSH           END-CODE

CODE Y@         ( addr -- n )
\ Fetch the word at the specified offset in the head segment.
                POP BX
                MOV DS, YSEG
                PUSH 0 [BX]
                MOV BX, CS      MOV DS, BX
                NEXT            END-CODE

CODE Y!         ( n addr -- )
\ Store word n at the offset in the head segment.
                POP BX
                MOV DS, YSEG
                POP 0 [BX]
                MOV BX, CS      MOV DS, BX
                NEXT            END-CODE

CODE YC@        ( addr -- char )
\ Fetch the byte at the offset in the head segment.
                POP BX          SUB AX, AX
                MOV DS, YSEG
                MOV AL, 0 [BX]
                MOV BX, CS      MOV DS, BX
                1PUSH           END-CODE

CODE YC!        ( char addr -- )
\ Store the byte at the specified offset in the head segment.
                POP BX          POP AX
                MOV DS, YSEG
                MOV 0 [BX], AL
                MOV BX, CS      MOV DS, BX
                NEXT            END-CODE

CODE Y,         ( n -- )
\ Add the 16 bit value  n  to the end of the working head space.
                MOV BX, YDP
                ADD YDP # 2 WORD
                POP CX
                MOV DS, YSEG
                MOV 0 [BX], CX
                MOV BX, CS      MOV DS, BX
                NEXT
                END-CODE

CODE YCSET      ( byte addr -- )
\ Set the bits at offset in the head segment according to "b".
                POP BX          POP AX
                MOV DS, YSEG
                OR 0 [BX], AL
                MOV BX, CS      MOV DS, BX
                NEXT            END-CODE

CODE YHASH      ( ystr vocaddr -- thread )
\ Find the vocabulary thread corresponding to a counted string in head 
\ space.
                POP DX          POP BX
                MOV DS, YSEG
                MOV AX, 1 [BX]          \ Get first and second chars
                SHL AL, # 1             \ Shift first char left one
                MOV CL, 0 [BX]          \ Get count
                AND CX, # 31            \ mask out all but actual word length
                DEC CX                  \ dec, and if zero then use a blank.
    CX<>0  IF   ADD AL, AH
           ELSE MOV AH, # 32
                ADD AL, AH              \ Plus second char
           THEN SHL AX, # 1             \ The sum shifted left one again
                ADD AL, 0 [BX]          \ Plus count byte
                AND AX, # #THREADS 1-
                SHL AX, # 1     ADD AX, DX
                MOV CX, CS      MOV DS, CX
                1PUSH           END-CODE

CODE XHERE      ( -- seg adr )
\ Returns segment an offset of next available byte in list space.
                PUSH XDPSEG     PUSH XDP
                NEXT            END-CODE

CODE X,         ( n -- )        \ XHERE !L  2 XDP +!
\ Adds a 16 bit value to the end of list space.
                POP AX
                MOV BX, XDP
                MOV DS, XDPSEG
                MOV 0 [BX], AX
                MOV BX, CS
                MOV DS, BX
                ADD XDP # 2 WORD
                NEXT            END-CODE

CODE XC,        ( n -- )        \ XHERE C!L 1 XDP +!
\ Adds an 8 bit value to the end of list space.
                POP AX
                MOV BX, XDP
                MOV DS, XDPSEG
                MOV 0 [BX], AL
                MOV BX, CS
                MOV DS, BX
                INC XDP WORD
                NEXT            END-CODE

CODE PR-STATUS  ( n1 -- b1 )
\ n1 is the printer number.  Return the printer status byte.
                POP DX          \ PRINTER NUMBER
                MOV AH, # 2
                PUSH SI         PUSH BP
                INT $17
                POP BP          POP SI
                MOV AL, AH
                MOV AH, # 0
                1PUSH           END-CODE

: <?PTR.READY> ( -- f1 )
\ $090 is printer not busy & printer selected.
                0 PR-STATUS ( $090 AND ) $090 = ;

DEFER ?PRINTER.READY    ' <?PTR.READY> IS ?PRINTER.READY
\ A defered word.  Returns  TRUE  if printer is ready.

DEFER CR
\ Send a carraige-return and line-feed to the console.

DEFER PEMIT     \ ' (PRINT) IS PEMIT
\ A version of  EMIT  that sends a character to the printer.

: (EMIT)        ( char -- )
\ Send a character to the console, and optionally to the printer.
                PRINTING @
                IF      PEMIT
                ELSE    CONSOLE
                THEN    ;

: CRLF          ( -- )
\ Sends a carriage return line feed sequence.
                13 EMIT 10 EMIT #OUT OFF
                #LINE DUP @ 1+
                PRINTING @ 0=
                IF      ROWS 1- MIN  THEN SWAP ! ;

: FEMIT         ( c1 -- )
\ A fast version of EMIT.  Control characters show graphic equivalence.
                SP@ 1 TYPE DROP ;

CREATE SPCS     ( -- a1 )      \ An array of 80 spaces for use by SPACES
                $02020
                DUP , DUP , DUP , DUP , DUP , DUP , DUP , DUP ,
                DUP , DUP , DUP , DUP , DUP , DUP , DUP , DUP ,
                DUP , DUP , DUP , DUP , DUP , DUP , DUP , DUP ,
                DUP , DUP , DUP , DUP , DUP , DUP , DUP , DUP ,
                DUP , DUP , DUP , DUP , DUP , DUP , DUP ,     ,

: SPACE         ( -- )    \ Display a space on the terminal.
                SPCS 1 TYPE ;

: SPACES        ( n -- )
\ Send a sequence of  n  spaces to the console.
                0MAX    DUP 79 >
                IF      80 /MOD 0
                        ?DO     SPCS   80 TYPE
                        LOOP
                THEN    SPCS SWAP TYPE ;

: BACKSPACES    ( n -- )
\ Send a sequence of  n  backspaces to the console.
                0 ?DO   BS EMIT -2 #OUT +! LOOP  ;

: %BEEP         ( -- )
                BELL (EMIT) #OUT DECR ;

DEFER BEEP      ( -- )          ' %BEEP IS BEEP
\ Ring the bell on the terminal


: BS-IN         ( n c -- 0 | n-1 )
\ If at beginning of line, beep, otherwise back up 1.
                >R DUP
                IF      1-   BS EMIT
                        #OUT @ 2- 0MAX #OUT !
                ELSE    BEEP
                THEN    R> ;

: (DEL-IN)      ( n c -- 0 | n-1 )
\ If at beginning of line, beep, otherwise back up and erase 1.
                >R DUP
                IF      1- BS EMIT SPACE BS EMIT
                        #OUT @ 4 - 0MAX #OUT !
                ELSE    BEEP
                THEN    R> ;

DEFER DEL-IN    ' (DEL-IN) IS DEL-IN
\ If at beginning of line, beep, otherwise back up and erase 1.

: BACK-UP       ( n c -- 0 c )
\ Wipe out the current line by overwriting it with spaces.
                >R DUP BACKSPACES   DUP SPACES   BACKSPACES   0  R> ;

: RESET-IN      ( -- )
\ Reset the system to a relatively clean state.
                FORTH   TRUE ABORT" Reset"  ;

DEFER RES-IN    ' RESET-IN IS RES-IN
\ Reset the system to a relatively clean state.

: P-IN          ( -- )
\ Toggle the printer on or off
                PRINTING @ 0= PRINTING !  ;

: (ESC-IN)      ( a n char -- a n+1 char )
\ Default handler of ESC character
                >R 2DUP + @ EMIT 1+ R> ;

DEFER ESC-IN    ' (ESC-IN) IS ESC-IN
\ A defered word to handle ESC character

: CR-IN         ( m a n c -- m a m c )
\ Finish input and remember the number of chars in SPAN
                >R SPAN !   OVER   BL EMIT R>  ;

: (CHAR)        ( a n char -- a n+1 char )
\ Process an ordinary character by appending it to the buffer.
                DUP>R 3DUP EMIT + C!   1+  R> ;

DEFER CHAR      ' (CHAR) IS CHAR
\ is usually (CHAR). Executed for most characters.

DEFER ^CHAR     ' CHAR   IS ^CHAR
\ Similiar to  CHAR  for control characters.

: NORM-KEYTABLE    ( a n1 char n2 -- a n1+1 char )
\ Execute the control character corresponding to n2
               EXEC:
   ^CHAR   ^CHAR  ^CHAR  RES-IN ^CHAR  ^CHAR   ^CHAR  ^CHAR
   DEL-IN  ^CHAR  ^CHAR  ^CHAR  ^CHAR  CR-IN   ^CHAR  ^CHAR
   P-IN    ^CHAR  ^CHAR  ^CHAR  ^CHAR  BACK-UP ^CHAR  ^CHAR
   BACK-UP ^CHAR  ^CHAR  ESC-IN ^CHAR  ^CHAR   ^CHAR  ^CHAR ;

DEFER KEYTABLE  ( a n1 char n2 -- a n1+1 char )
\ A defered word to execute the control character corresponding to n2 .
' NORM-KEYTABLE IS KEYTABLE


: NEXPECT       ( adr len start -- )
\ expect to a buffer that may already contain some data.
                DUP>R IF OVER R@ TYPE THEN
                DUP SPAN !   SWAP R> ( LEN ADR 0_SOFAR )
                BEGIN   2 PICK OVER - ( len adr #so-far #left )
                WHILE   2>R >R KEY R> SWAP 2R> ROT
                                \ The above looks silly no doubt, it is done
                                \ to assure the stack is empty of the
                                \ parameters used by NEXPECT, so a background
                                \ task can display the stack when both shift
                                \ keys are pressed together.
                        DUP BL <
                        IF      DUP KEYTABLE DROP
                        ELSE    DUP 127 =
                                IF   DEL-IN   ELSE   CHAR   THEN  DROP
                        THEN
                REPEAT  3DROP ;

: (EXPECT)      ( adr len --- )
\ Accept text into the buffer at "adr" for "len" bytes.
                0   NEXPECT  ;          ( len adr 0 )

DEFER EXPECT    ' (EXPECT) IS EXPECT
\ Get a string from the terminal and place it in the buffer provided.

CODE TIB        ( -- addr )
\ Leaves address of text input buffer.
                PUSH 'TIB       NEXT    END-CODE

                                        \ 07/03/89 RB
CODE MORE?      ( -- Flag )             \ Is words left in input stream?
                MOV AX, >IN
                SUB AX, #TIB
                SBB AX, AX
                1PUSH   END-CODE

: QUERY         ( -- )
\  Get more input from the user and place it at TIB.
                TIB COLS EXPECT  SPAN @ #TIB ! >IN OFF  ;

      VARIABLE DISK-ERROR
\ Returns the address of a variable which contains error information on the
\ most recent attempt to access the disk.

   -2 CONSTANT LIMIT
\ The highest address in the Code Segment used by Forth.

LIMIT 10 - CONSTANT FIRST
\ This is a simple constant having the value 10 less than  LIMIT .

FIRST 10 - CONSTANT INIT-R0
\ Address of the base of the Return Stack.

DECIMAL

FORTH DEFINITIONS

: HEX           ( -- )   
\ Set the contents of BASE to 16 (i.e., Hexadecimal).
                16 BASE !  ;

: DECIMAL       ( -- )
\ Restore the contents of base to 10 (i.e., Decimal)
                10 BASE !  ;

: OCTAL         ( -- )
\ Set the contents of BASE to 8 (i.e., Octal)
                8 BASE !  ;

: BINARY        ( -- )
\ Set the contents of BASE to 2 (i.e., Binary)
                2 BASE !  ;

DEFER DEFAULT
\ Opens the default file per the execute line.  
\ Does nothing if no file was given.

CODE DIGIT      ( char base -- n f )
\ If the character is equivalent to a digit in the specified base,
\ convert the character and return a  TRUE  flag, else leave char and  FALSE.
                POP DX          POP AX          PUSH AX
                SUB AL, # ASCII 0
                JB 0 $
                        CMP AL, # 9
              > IF
                        CMP AL, # 17
                        JB 0 $
                        SUB AL, # 7
                THEN
                CMP AL, DL
                JAE 0 $
                        MOV DL, AL
                        POP AX
                        MOV AX, # TRUE
                2PUSH
        0 $:    SUB AX, AX      1PUSH           END-CODE

: DOUBLE?       ( -- f )
\ Returns non-zero if a period was encountered during last numeric scan. 
                DPL @ 1+   0<> ;

: CONVERT       ( +d1 adr1 -- +d2 adr2 )
\ Convert the string at adr1 to a double number until an unconvertable
\ character is encountered (pointed to by adr2).  Accumulate in +d1.
                BEGIN   1+  DUP>R  C@  BASE @  DIGIT
                WHILE   SWAP  BASE @ UM*  DROP  ROT  BASE @ UM*  D+
                        DOUBLE?  IF  DPL INCR THEN  R>
                REPEAT  DROP  R>  ;

: (NUMBER?)     ( adr -- d flag )
\ Convert string at  adr  to a number.  If successful, leave  TRUE  flag.  
\ The string should terminate with an ASCII space.
                0 0  ROT  DUP 1+  C@  ASCII -  =  DUP  >R  -  DPL -1!
                BEGIN   CONVERT  DUP C@  ASCII , ASCII / BETWEEN
                WHILE   DPL 0!
                REPEAT  -ROT  R> IF  DNEGATE  THEN   ROT C@ BL =  ;

: NUMBER?       ( adr -- d flag )
\ Convert a counted string to a number.  The string should terminate
\ with an ASCII space and contain a valid, possibly signed, number.
                FALSE  OVER COUNT BOUNDS
                ?DO     I C@ BASE @ DIGIT NIP
                        IF      DROP TRUE LEAVE THEN
                LOOP
                IF  (NUMBER?)  ELSE  DROP  0 0 FALSE  THEN  ;

: %$NUM         ( a1 -- d1 f1 )         \ process as a hex number $A123
                dup>r DUP COUNT 1- 0MAX >R
                DUP 1+ SWAP R> CMOVE    \ Extract the $.
                DUP C@ 1- OVER C!       \ Shorten count by 1.
                BL OVER COUNT + C!      \ Append a blank to string.
                BASE @ >R               \ Save the base for later restoral.
                HEX NUMBER?             \ Try to convert the number in HEX
                R> BASE !               \ Restore the BASE.
                DUP 0=                  \ If its not a number, restore the $.
                IF      R@ COUNT >R DUP 1+ R> CMOVE>
                        1 R@ C+!
                        ASCII $ R@ 1+ C!
                THEN    R>DROP ;

: %'NUM         ( a1 -- d1 f1 )         \ process as an ascii char 'A'
                2+ C@         0 TRUE DPL ON ;

: %^NUM         ( a1 -- d1 f1 )         \ process as a control char ^A
                2+ C@ $1F AND 0 TRUE DPL ON ;

: %NUMH         ( a1 -- d1 f1 )         \ process as a hex number A123H
                DUP COUNT + 1- >R       \ save addr of end last char
                BL R@ DUP C@ >R  C!     \ save last char of string & set to bl
                BASE @ >R               \ save the base to restore later
                HEX                     \ set the BASE to HEX
                NUMBER?                 \ convert the number in BINARY
                R> BASE !               \ restore the base
                R> R> C! ;              \ restore trailing B

DEFER $NUM      ' %$NUM   IS $NUM       \ HEX
DEFER 'NUM      ' %'NUM   IS 'NUM       \ ASCII
DEFER ^NUM      ' %^NUM   IS ^NUM       \ CONTROL
DEFER #NUM      ' NUMBER? IS #NUM       \ A NUMBER
DEFER NUMH      ' %NUMH   IS NUMH       \ HEX

: %NUMB         ( a1 -- d1 f1 )         \ process as a BINARY number 10101B
                BASE @ $0A =            \ but ONLY if in DECIMAL number base
        IF      DUP COUNT + 1- >R       \ save addr of end last char
                BL R@ DUP C@ >R  C!     \ save last char of string & set to bl
                2 BASE !                \ set BASE=2 (binary)
                NUMBER?                 \ convert the number in BINARY
                R> R> C!                \ restore trailing B
                DECIMAL                 \ return to DECIMAL
        ELSE    #NUM                    \ else convert as normal number
        THEN    ;

DEFER NUMB      ' %NUMB   IS NUMB

\ Extend the special number handling done by F-PC to include
\ HEX numbers entered with an 'H' or 'h' postfix character
\ and binary numbers entered with a '&' postfix char.

CODE %NUMBER    ( a1 -- d1 f1 )
\ Convert count delimited string at a1 into double number.  Special
\ prefixes and sufixes allowed.
                MOV DI, SP
                MOV BX, 0 [DI]
                MOV AL, 1 [BX]
                CMP AL, # ASCII $               \ test for leading $
             0= IF      JMP ' $NUM              \ process as HEX
                THEN
                MOV AL, 1 [BX]
                MOV AH, 3 [BX]
                CMP AX, # ASCII '  dup flip +   \ test for lead & trail '
             0= IF      JMP ' 'NUM              \ process as ascii char
                THEN
                MOV AX, 0 [BX]
                CMP AX, # ASCII ^ flip $02 +    \ test for lead ^ & cnt = 2
             0= IF      JMP ' ^NUM              \ process as control char
                THEN
                MOV AL, 0 [BX]                  \ get count
                SUB AH, AH                      \ clear AH
                ADD BX, AX                      \ add to base address
                MOV AL, 0 [BX]                  \ get last character
                CMP AL, # ASCII h               \ test for trailing 'h'
             0= IF      JMP ' NUMH              \ process as HEX
                THEN
                CMP AL, # ASCII H               \ test for trailing 'H'
             0= IF      JMP ' NUMH              \ process as HEX
                THEN
                CMP AL, # ASCII b               \ test for trailing 'b'
             0= IF      JMP ' NUMB              \ process as BINARY
                THEN
                CMP AL, # ASCII B               \ test for trailing 'B'
             0= IF      JMP ' NUMB              \ process as BINARY
                THEN
                JMP ' #NUM                      \ else process as a number
                END-CODE

: (NUMBER)      ( a1 -- d1 )
\ Convert count delimited string at a1 into a double number.
                %NUMBER NOT ?MISSING ;

DEFER NUMBER    ' (NUMBER) IS NUMBER
\ Convert count delimited string at a1 into a double number.

: HOLD          ( char -- )
\ Save the character for later output.  Characters are entered in a
\ right to left sequence!
                HLD DECR HLD @ C!   ;

: <#            ( -- )  
\ Start numeric conversion.
                PAD  HLD  !  ;

: #>            ( d# -- addr len )
\ Terminate numeric conversion.
                2DROP  HLD  @  PAD  OVER  -  ;

: SIGN          ( n1 -- )
\ If n1 is negative insert a minus sign into the string.
                0< IF  ASCII -  HOLD  THEN  ;

: #             ( d1 -- d2 )
\ Convert a single digit in the current base.
                BASE @ MU/MOD ROT 9 OVER <
                IF  7 + THEN ASCII 0  +  HOLD  ;

: #S            ( d -- 0 0 )
\ Convert a number until it is finished.
                BEGIN  #  2DUP  OR  0=  UNTIL  ;

: (U.)          ( u -- a l )
\ Convert an unsigned 16 bit number to a string.
                0    <# #S #>   ;

: U.            ( u -- )
\ Convert an unsigned 16 bit number to a string.
                (U.)   TYPE SPACE   ;

: U.R           ( u l -- )
\ Output u as an unsigned single number right justified in a field of
\ l spaces.
                >R   (U.)   R> OVER - SPACES   TYPE   ;

: (.)           ( n -- a l )
\ Convert a signed 16 bit number to a string.
                DUP ABS 0   <# #S   ROT SIGN   #>   ;

: .             ( n -- )
\ Output as a signed single number with a trailing space.
                (.)   TYPE SPACE   ;

: .R            ( n l -- )
\ Output n as a signed single number right justified in a field of l spaces.
                >R   (.)   R> OVER - SPACES   TYPE   ;

: (UD.)         ( ud -- a l )
\ Convert an unsigned double number ud to a string.
                <# #S #>   ;

: UD.           ( ud -- )
\ Output as an unsigned double number with a trailing space
                (UD.)   TYPE SPACE   ;

: UD.R          ( ud l -- )
\ Output ud as an unsigned double number right justified in a field of
\ l spaces.
                >R   (UD.)   R> OVER - SPACES   TYPE  ;

: (D.)          ( d -- a l )
\ Convert a signed double number to a string.
                TUCK DABS   <# #S   ROT SIGN  #>   ;

: D.            ( d -- )
\ Output as a signed double number with a trailing space.
                (D.)   TYPE SPACE   ;

: D.R           ( d l -- )
\ Output d as a signed double number right justified in a field of l spaces.
                >R   (D.)   R> OVER - SPACES   TYPE   ;

CODE  SKIP      ( addr len char -- addr' len' )
\ Skip char through addr for len, returning addr' and len' of char+1.
                POP AX          POP CX
                JCXZ 0 $
                POP DI
                MOV DX, ES      MOV ES, SSEG
                REPZ            SCASB
                MOV ES, DX
            0<> IF
                INC CX          DEC DI
            THEN
                PUSH DI         PUSH CX
                NEXT
        0 $:    PUSH CX         NEXT            END-CODE

CODE  SCAN      ( addr len char -- addr' len' )
\ Scan for char through addr for len, returning addr' and len' of char.
                POP AX          POP CX
                JCXZ 0 $
                POP DI
                MOV DX, ES      MOV ES, SSEG
                REPNZ           SCASB
                MOV ES, DX
             0= IF
                INC CX          DEC DI
             THEN
                PUSH DI         PUSH CX
                NEXT
        0 $:    PUSH CX         NEXT            END-CODE

CODE /STRING    ( addr len n -- addr' len' )
\ Index into the string by n.  Returns addr+n and len-n.
                POP AX          POP BX
                PUSH BX
                CMP AX, # 0
            >=  IF      CMP BX, AX
                    U<= IF
                        XCHG BX, AX     \ AX = SMALLER OF AX BX
                     THEN
                THEN
                POP BX          POP DX
                ADD DX, AX      PUSH DX
                SUB BX, AX      PUSH BX
                NEXT            END-CODE

CODE SOURCE     ( -- addr len )         \ TIB #TIB @
\ Return address and count of the input string in the Text input buffer.
                MOV DX, 'TIB
                MOV AX, #TIB
                2PUSH
                END-CODE

: PARSE         ( char -- addr len )
\ Scan the input stream until char is encountered.
                >R   SOURCE >IN @ /STRING   OVER SWAP R> SCAN
                >R OVER -  DUP R>  0<> -  >IN +!  ;

CODE WORD       ( c1 --- addr )
\  Parse the input stream for char and return a count delimited
\  string at here.  Note there is always a blank following it.
                MOV DI, 'TIB
                MOV CX, #TIB
                POP BX
                PUSH ES                         \ Save ES for later restoral
                MOV DX, DS      MOV ES, DX      \ ES = DS from now to END
                MOV AX, >IN
                CMP CX, AX
            U<= IF              MOV AX, CX      \ AX = SMALLER OF AX CX
                THEN
                ADD DI, AX
                SUB CX, AX
                MOV AX, BX
          CX<>0 IF              REPZ            SCASB
                            0<> IF              INC CX
                                                DEC DI
                                THEN
                THEN
                MOV DX, #TIB            \ 04/12/91 added to save start of
                SUB DX, CX              \ word just parsed out
                MOV >IN_WORD DX         \
                MOV DX, DI
                MOV AX, BX
          CX<>0 IF              REPNZ           SCASB
                             0= IF              INC CX
                                                DEC DI
                                THEN
                THEN
                SUB DI, DX
                MOV BX, #TIB
                MOV AX, DX
          CX<>0 IF      DEC CX
                THEN
                SUB BX, CX      MOV >IN BX
                MOV BX, UP
                MOV DX, DP [BX]
                MOV CX, DI
                MOV DI, DX
                MOV 0 [DI], CL
                INC DI          \ CLD
                MOV BX, IP
                MOV IP, AX
                TEST DI, # 1
                JZ 0 $
                JCXZ 0 $
                MOVSB
                DEC CX
        0 $:    SHR CX, # 1
                REPNZ           MOVSW
                RCL CX, # 1
                REPNZ           MOVSB
                MOV AL, # 32    STOSB
                MOV IP, BX
                POP ES                          \ Restore ES
                PUSH DX
                NEXT            END-CODE

