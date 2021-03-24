\\ PROFILE.SEQ          Profile a Forth word            by Tom Zimmer

  This file provides the ability to profile a large F-PC application.
Words that consume large amounts of execution time are detected, and
accumulated into a 32k external array.

  The operation of this profiller is almost completely transparent to
the operation of your program.  A change is made to the DOS system clock,
to accumulate counts of where an application is each time a clock tick
occurs. PROFILE saves a count of each occurance of a particular "ES" (the
F-PC colon definition pointer) on each clock tick. After the application
terminates, PROFILE writes the accumulated results to a file called
PROF.TXT. The file will contain the paragraph of each word accumulated,
the count of how many times that paragraph was found, and the name of the
Forth word that starts at that paragraph.

  This profiler is different from most, in that doesn't try to measure
the performance of code words, it will in fact accumulate the time of any
code word executed, into the time of the colon definition that used it.
While this may seem odd, it is correct, as most of F-PC Forths CODE time
is spent in words like NEXT, NEST, and UNNEST which are as highly
optimized as they can be.  The primary use of PROFILE is to detect
algorithm problems in your program. Since counts are only performed at 18
times a second, you will need to profile a fairly long (in execution
time) program to get useful results.


  PROFILE can be used in two ways:

        PROF <word_name> <enter>

  will profile <word_name>, and write results to PROF.TXT. You can also
insert a start and stop command in your program to have a program
profiled every time it is run, or to profile only a part of the program:

        : MYWORD        ( -- )
                        PROF_START      \ start profiling
                        ...             \ your program goes here
                        PROF_FINISH     \ stop profiling and write
                        ;               \ results to PROF.TXT

  A companion program for TCOM called PROF.SEQ is a standalone program to
profile TCOM programs, or any executable program into 16 byte buckets of
where executing insturctions spend most of their time.  PROF can be used
on F-PC programs, but will profile code words, rather than colon
defintions.

{

VARIABLE ESSEG

LABEL PRNEST    MOV ESSEG AX
                MOV ES, AX      \  2 cycles     \ move into ES
                SUB IP, IP      \  3 cycles     \ clear IP
                NEXT
                END-CODE

LABEL PREXIT    XCHG RP, SP     \ 4 cycles
                POP IP          \ 8 cycles
                POP ES          \ 8 cycles
                MOV ESSEG ES
                XCHG RP, SP     \ 4 cycles
                NEXT
                END-CODE

: PRPATCH
        233 >NEST $0E + !                       \ put in JMP
        PRNEST >NEST $11 + - >NEST $0F + !      \ lay in jump address
        233 >DOES $0C + !                       \ put in JMP
        PRNEST >DOES $0F + - >DOES $0D + !      \ lay in jump address
        233 ['] EXIT !                          \ put in JMP
        PREXIT ['] EXIT   3 + - ['] EXIT   1+ ! \ lay in jump address
        233 ['] UNNEST !                        \ put in JMP
        PREXIT ['] UNNEST 3 + - ['] UNNEST 1+ ! \ lay in jump address
        ;

PRPATCH         \ patch the profiled functions into existing functions

     4  ARRAY    TIMERSAVE      \ vector in CODE space
$A000.  POINTER  PROF_SEG       \ a place to save the profile buffer segment
        VARIABLE PSAVED

LABEL MYTIMER   ( -- )          \ Timer interrupt routine
                PUSH DS
                PUSH BX
                PUSH DI
                MOV BX, CS: ESSEG
                MOV DS, CS: &> PROF_SEG \ DS = profile buffer segment
                CMP 0 [BX], # $FF       \ is bucket full?
                JAE 0 $                 \ if so, skip the increment
                INC 0 [BX] BYTE
        0 $:    POP DI
                POP BX
                POP DS
                JMP FAR CS: [] TIMERSAVE        \ go do any other interrupts
                END-CODE

CODE SAVE&SET_VECS ( -- )       \ save and set some vectors
                CMP PSAVED # 0 WORD
                JNE 0 $
                PUSH ES
                MOV AX, # $351C         \ Get the interupt vector for
                INT $21                 \ BIOS timer vector
                MOV CS: TIMERSAVE BX
                MOV CS: TIMERSAVE 2+ ES \ Save old vector
                POP ES
                                        \ Setup timer vector to our routine
                PUSH DS                 \ save data segment
                MOV AX, CS
                MOV DS, AX              \ set DS to CS
                MOV DX, # MYTIMER       \ the new timer tick interrupt
                MOV AX, # $251C
                INT $21
                POP DS                  \ recover the data segment
                INC PSAVED WORD
        0 $:    NEXT    END-CODE

CODE REST_VECS  ( -- )                  \ Restore the vectors
                CMP PSAVED # 0 WORD
                JE 0 $
                PUSH DS
                MOV DX, CS: TIMERSAVE
                MOV DS, CS: TIMERSAVE 2+
                MOV AX, # $251C
                INT $21
                POP DS
                MOV PSAVED # 0 WORD
        0 $:    NEXT    END-CODE

: UNPROF_EXITFUNC ( -- )
                REST_VECS
                DEFERS BYEFUNC ;

' UNPROF_EXITFUNC IS BYEFUNC    \ link into bye function list

handle profhndl                 \ the profile save file handle
create crlf$ $0D c, $0A c,      \ a CRLF string

32 array prof_name

: prof.id       ( nfa -- )
                yseg @ swap ?cs: prof_name 32 cmovel
                prof_name c@ 31 and prof_name c!
                prof_name count bounds
                ?do     i c@ $7F and i c!
                loop    prof_name count profhndl hwrite drop ;

: 1prof         ( a1 n1 -- a2 n2 )
                over prof_seg swap c@L 3 <       \ leave if low count
                if      1 /string exit
                then
                over dup>r
                crlf$ 2 profhndl hwrite drop
                hex
                dup 0 <# # # # # #> profhndl hwrite drop
                decimal
                prof_seg swap c@l (u.) 4 over -
                spcs swap profhndl hwrite drop  \ spaces
                          profhndl hwrite drop  \ word name
                spcs 1 profhndl hwrite drop
                ?cs: sseg !
                r> [ bug ] seg>cfa
                if      >name prof.id [ forth ]
                else    drop
                then    1 /string ;

: saveprof      ( -- )          \ display the profile results
                " PROF.TXT" profhndl ">handle
                profhndl hcreate abort" Failed to create profile save file"
                $0000 $8000
                begin   prof_seg sseg !
                        0 skip dup
                while   1prof
                repeat  2drop
                ?cs: sseg !
                crlf$ 2 profhndl hwrite drop    \ append a pair of CRLF's
                crlf$ 2 profhndl hwrite drop
                profhndl hclose drop ;

: prof_on       ( -- )          \ turn on profiling
                save&set_vecs ;

: prof_off      ( -- )          \ turn off profiling
                rest_vecs ;

: prof_start    ( -- )
                prof_seg 0 sizeof@> prof_seg drop 0 lfill
                                        \ fill array with zeros
                prof_on ;

: prof_finish   ( -- )
                prof_off
                saveprof ;

: prof          ( -- )
\+ unedit       UNEDIT
                prof_start
                interpret
                prof_finish ;


