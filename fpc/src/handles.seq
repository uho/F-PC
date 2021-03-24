\ HANDLES.SEQ   Handle impementation file               by Tom Zimmer

\ Link this file into the FILELIST chain.

FILES DEFINITIONS

VARIABLE HANDLES.SEQ

FORTH DEFINITIONS

\ This file contains the code to talk to a file with the
\ DOS 2.00+ handle routines.

DECIMAL

70 CONSTANT B/HCB
68 CONSTANT HNDLOFFSET
64 CONSTANT B/FILENAME
VARIABLE RWERR

                \ Attrib is normally  zero (0) for Read/Write.
                \ Attrib may be set to one (1) for Write ONLY.
                \ Attrib may be set to two (2) for Read  ONLY.
: >ATTRIB       ( handle --- attrib-addr )      66 + ;

: >HNDLE        ( handle --- handle-addr )      HNDLOFFSET + ;
: >NAM          ( handle --- name-string-addr ) 1+   ;
: CLR-HCB       ( HANDLE - ) DUP B/HCB ERASE -1 SWAP >HNDLE ! ;

                \  defining    running
: HANDLE        ( name ---  |  --- addr )
                 CREATE HERE B/HCB ALLOT CLR-HCB ;

        \       The HANDLE memory data structure is as shown here.

        \         1byte    65 bytes      2 bytes    2 bytes
        \       [ count  ] [ name....0 ] [ attrib ] [ handle > -1 ]
        \         addr       addr+1        addr+66    addr+68
        \          |          |             |          |
        \          |          |_>NAM        |_>ATTRIB  |_>HNDLE
        \          |
        \          |_Address of the array returned by a word
        \            defined with HANDLE.

CREATE DEFEXT 3 C,-T ASCII S C,-T ASCII E C,-T ASCII Q C,-T 4 ALLOT

: ?DEF.EXT      ( handle --- )    \ maybe add an extension to file
                 dup c@ 60 > if drop exit then
                 >r true r@ count over + 1- over umax
               ?do      i c@ ASCII . =
                        if      drop false leave
                        then
                        i c@ ASCII \ = ?leave
            -1 +loop    \ returns true if no decimal point found
                if      defext c@
                        if      defext count r@ count + 1+ swap cmove
                                ASCII . r@ count + c!
                                defext c@ 1+ r@ c@ + r@ c!
                        then
                then    r>drop ;

: ">HANDLE      ( a1 n1 a2 -- )         \ move string a1,n1 to handle a2
                dup>r CLR-HCB
                b/filename min r@ place
                0 r@ count + c!
                r> ?DEF.EXT ;

: $>HANDLE      ( a1 a2 --- )           \ move counted string a1 to handle a2
                >r count r> ">handle ;

: !HCB          ( handle --- )
                BL WORD COUNT CAPS @
                IF      2DUP UPPER
                THEN    ROT ">HANDLE ;

: FCB>HANDLE    ( A1 A2 --- )
                DUP CLR-HCB
                1+ dup>r SWAP 1+ dup>r 8 OVER + SWAP
                DO      I C@ BL = ?LEAVE
                        I C@ OVER C! 1+
                LOOP    ASCII . OVER C! 1+
                R> 8 + 3 OVER + SWAP
                DO      I C@ BL = ?LEAVE
                        I C@ OVER C! 1+
                LOOP    0 OVER C! R@ - R> 1- C! ;

: HANDLE>EXT    ( handle -- a1 )
                count + dup dup 4 -
                do      i c@ ASCII . =
                        if      drop i leave  then
                loop    ; \ points to final decimal point if present

: $>EXT         ( string-extension handle --- )
                dup c@ 60 > if 2drop exit then
                dup>r handle>ext
                ASCII . over c! 1+ >r count r@ over >r
                swap cmove r> r> + 0 over c! r@ - 1- r> c! ;

CODE HDOS1      ( cx dx fun -- ax cf | error-code 1 )
                pop ax
                pop dx
                pop cx
                int $21
                push ax
             u< if
                mov al, # 1
             else
                mov al, # 0
             then
                sub ah, ah
                1push
                end-code

CODE HDOS3      ( bx cx dx ds fun -- ax cf | error-code 1 )
                pop ax
                pop ds
                pop dx
                pop cx
                pop bx
                int $21
                push ax
             u< if
                mov al, # 1
             else
                mov al, # 0
             then
                sub ah, ah
                push ax
                mov ax, cs
                mov ds, ax
                next
                end-code

CODE HDOS4      ( bx cx dx fun -- ax cf | error-code 1 )
                pop ax
                pop dx
                pop cx
                pop bx
                int $21
                push ax
             u< if
                mov al, # 1
             else
                mov al, # 0
             then
                sub ah, ah
                1push
                end-code

CODE MOVEPOINTER ( double-offset handle --- )
                pop bx
                ADD bx, # HNDLOFFSET
                mov bx, 0 [bx]
                pop cx
                pop dx
                mov ax, # $4200  \ from start of file
                int $21
                next
                end-code

CODE ENDFILE    ( handle --- double-end )
                pop bx
                add bx, # hndloffset
                mov bx, 0 [bx]
                mov cx, # 0
                mov dx, # 0
                mov ax, # $4202  \ from end of file
                int $21
             u< if
                sub ax, ax
             then
                push ax
                push dx
                next
                end-code

DEFER PATHSET   ( handle --- f1 )

' 0= IS PATHSET

\   Code loaded later is plugged into PATHSET, to prepend the
\ current path to the handle specified on the top of the stack.
\
\   The returned vlue is zero if the path was set properly, or
\ non-zero if an error occured while setting the path.

CODE <HRENAME>  ( handle1 handle2 --- ax cf=0 | error-code 1 )
                pop di
                add di, # 1
                pop dx
                push es         \ Save ES for later restoral
                mov ax, ds
                mov es, ax      \ set es to ds
                add dx, # 1
                mov ax, # $5600  \ from start of file
                int $21
                pop es          \ Restore ES
                push ax
             u< if
                mov ax, # 1
             else
                mov ax, # 0
             then
                1push
                end-code
                        \ returns 18 if the rename was good, not zero.

: HRENAME       ( handle1 handle2 --- return-code )
                DUP PATHSET DROP OVER PATHSET DROP
                <HRENAME>
                if      $0FF and
                else    drop 0
                then    ;

: HCREATE       ( handle --- error-code )
                DUP PATHSET ?dup if  nip exit then
                dup >hndle >r       \     save handle address
                dup >attrib @         \   hndl --- bx hndl attib
                swap >nam               \ --- bx attrib name
                $3C02 hdos1 0=
                if      r@ ! 0      \ stuff handle, ret 0
                else    $0FF and
                then    r>drop ;

0 VALUE R/W-MODE                \ current read/write mode
0 VALUE R/W-DMODE               \ default read/write mode

\ This word allow you to set the default read/write mode used by F-PC.
\ It is used as follows:
\                               READ-WRITE DEF-RWMODE
\                       or      READ-ONLY  DEF-RWMODE
\
\ All further file open operations will be in the newly specified mode.

: DEF-RWMODE    ( -- )          \ use current mode as the default.
                r/w-mode %!> r/w-dmode ;

\ The following words effect only the next HOPEN operation to be performed.
\ After the open is done, R/W-MODE reverts to the the default mode for later
\ file opens.

: READ-ONLY     ( -- )          \ open a file for read only
                0 %!> r/w-mode ;

: READ-WRITE    ( -- )          \ open a file for read and write operations
                2 %!> r/w-mode ;

: WRITE-ONLY    ( -- )          \ open a file for write only.
                1 %!> r/w-mode ;

: HOPEN         ( handle --- error-code )
                DUP PATHSET ?dup if  nip exit then
                dup >hndle >r           \ save handle address
                dup >attrib @           \ hndl --- hndl attib
                swap >nam               \ --- attrib name
                $3D00 r/w-mode or
                hdos1 0=                \   read/write attribs
                if      r@ ! 0          \ stuff handle, ret 0
                else    $0FF and        \ else error code
                then    r>drop          \ clean rstack
                r/w-dmode %!> r/w-mode ;  \ revert to default mode

: HCLOSE        ( handle --- return-code )
                >hndle dup @ -1 rot ! dup -1 >
                if      0 0 $3E00 hdos4
                        if      $0FF and
                        else    drop 0 then
                else    drop 0
                then    ;

: HDELETE       ( handle --- return-code )
                0 0 rot >nam $4100 hdos4
                if $0FF and else drop 0 then ;

                \ extended read
: EXHREAD       ( a1 n1 handle segment -- length-read )
                >r >hndle @ -rot swap r> $3F00 hdos3
                if      $0FF and rwerr ! 0 then ;

                \ extended write
: EXHWRITE      ( a1 n1 handle segment -- length-written )
                >r >hndle @ -rot swap r> $4000 hdos3
                if      $0FF and rwerr ! 0 then ;

: HWRITE        ( a1 n1 handle --- length-written )
                >hndle @ -rot swap    \ handle count addr
                $4000 hdos4 if   $0FF and rwerr ! 0 then ;

: HREAD         ( a1 n1 handle --- length-read )
                >hndle @ -rot swap    \ handle count addr
                $3F00 hdos4 if   $0FF and rwerr ! 0 then ;

\ In the following definition, "attr" defines what type of special files
\ (besides normal files) are to be found by FIND-FIRST. The lower six bits
\ of "attr" are defined to control the find as follows:
\
\                 0       read-only
\                 1       hidden
\                 2       system
\                 3       volume label
\                 4       directory
\                 5       archive
\
\ The upper three bits are reserved, and should always be zero.

CODE FIND-FIRST ( adr attr -- t/f )     \ find first file including type attr
                xor     bx, bx          \ adr is null terminated filename
                mov     ax, # $4E00     \ return FALSE if name was found
                pop     cx
                pop     dx
                int     $21
                jnc     1 $
                dec     bx      \ error. in BX set true
        1 $:    push    bx
                next            end-code

CODE FIND-NEXT  ( -- t/f )              \ find next occurance of name above
                xor     bx, bx          \ return FALSE if another was found
                mov     ax, # $4F00
                int     $21
                jnc     1 $
                dec     bx      \ error
        1 $:    push    bx
                next            end-code

\ : FINDFIRST     ( string --- f1 )
\                 $010 swap $4E00 hdos1 drop $0FF and ;
\ : FINDNEXT      ( --- f1 )
\                       $000  $000 $4F00 hdos1 drop $0FF and ;

CODE DTA@       ( -- seg offset )       \ get the data transfer seg&offset
                mov     ah, # $2F
                mov     cx, es
                int     $21
                push    es
                push    bx
                mov     es, cx
                next            end-code

CODE DTA!       ( seg offset -- )       \ set the data transfer seg&offset
                mov     cx, ds
                pop     dx
                pop     ds
                mov     ah, # $1A
                int     $21
                mov     ds, cx
                next            end-code

: SET-DTA       ( a1 --- )
                ?cs: swap dta! ;

