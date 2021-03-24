\ NEW-WFL.SEQ   A NEW window file selection tool from RUSSIA

\ This appears to be a significant improvement in my file selection
\ utility.  Though it is quite large (12k), it works very well.
\ I (Tom Zimmer) have made only very minor changes to make it
\ compatible with the current version of F-PC.  The strange characters
\ in the comments, translate to cyrilic characters on a russian
\ computer.


\ Оконный селектор файлов.

decimal prefix

        variable curr-color     \  color setting mode
code draw-line  ( l,n -- )
                pop     ax
                pop     cx
                mov     bx, attrib
                mov     ah, # $9
                int     $10
                next
end-code

 vocabulary getvoc

 also hidden also getvoc getvoc definitions

\ string in format ASCIIZ *.* for searching files in catalog

create MASK*.*  ascii * c, ascii . c, ascii * c, 0 c,

        defer get-attrib1       ' noop is get-attrib1
        defer get-attrib2       ' noop is get-attrib2
        defer get-attrib4       ' noop is get-attrib4
        defer get-attrib5       ' noop is get-attrib5
        defer get-attrib6       ' noop is get-attrib6
        defer get-attrib10      ' noop is get-attrib10

\ Coordinates for typing file names on the screen

create X-COORD  3 C, 20 C, 37 C,

  3             constant Y-COORD
  38            constant PATH-LONG      \ length of path buffer

  24            constant NAME-LONG      \ length of name buffer

  14            constant MASK-LONG      \ length of searching mask

 500            constant MAXFILES       \ length of segment names buffer


\ Format of coding the file in the buffer: length - 4 bytes,
\ length of file name - 1-13 bytes. That makes up to 17 bytes as a maximum.

        variable attribs
                 8 allot        \ saving color attributes

        variable old-path
               70 allot         \ saving path to current catalog

        variable curr-time
                 8 allot        \ String of time counter  uses for reducing
                                \ "trash" on the screen.
maxfiles 16 um*
        pointer  dir-seg        \ Pointer of the segment to be used.

        variable end-files      \ Pointer of a space occupied by files.
                                \ Grous up.

        variable end-catalogs   \ Pointer of a space occupied by catalogs.
                                \ Grous down.

        variable n-files        \ Files counter.

        variable n-catalogs     \ Catalogs counter.

        variable ?end-select    \ "True" means that file was chosen

        variable path-buf
                 70 allot       \ Buffer for inputing and editing of path
                                \ Length of the buffer is determined by DOS
                                \ requiremens.

        variable ren-buf
                 70   allot     \ Buffer for inputing and editting of file
                                \ names

        variable mask-buf
            MASK-LONG allot
                                \ Mask buffer  for choosing files

        variable name-buf
            NAME-LONG allot     \ Buffer for inputing and editing file name

        variable u-dta
                 $80 allot
                                \ Buffer for DTA while searching files

        variable end-seg
                                \ End of names buffer

        variable ?newpage       \ "true" - means that it is necessary
                                \ to rewrite the screen

        variable curr-name      \ Number of chosen file

        variable ~offset        \ offset

        variable find-buf
                 20 allot       \ Letters buffer  for searching

        variable old-litera     \

        variable curr-device    \ Current drive number


        variable first-file?    \ Are any files in catalog ?


        variable x&y.mouse      \ Hold on current mouse coordinates


 MAXFILES 1- 16 * end-seg !

code ?4+        ( n, t/f -- n/n+4 )
                pop     ax
                or      ah, al
        0<> if  pop     ax
                add     ax, # 4
                push    ax
        then    next
end-code

code scr-up     ( -- )
                mov     ax, # $0601     \ 1 stringt, funcktion 6
                mov     bh, attrib      \ window attributes
                mov     cx, # $0304     \ coordinates: upper left corner,
                mov     dx, # $1532     \              down right corner.
                int     $10
                next
end-code

code scr-down   ( -- )
                mov     ax, # $0701
                mov     bh, attrib
                mov     cx, # $0304
                mov     dx, # $1532
                int     $10
                next
end-code

\ To read in buffer " path-buf " path to current catalog

code curr-path  ( -- )
                mov     ah, # $47
                xor     dl, dl
                push    si
                mov     si, # path-buf 3 +
                int     $21
                pop     si
                next
end-code

\ Changing catalog according with string in the buffer

code set-path   ( ASCIIZ -- t/f )
                mov     ah, # $3B
                pop     dx
                int     $21
                jc      1 $
                xor     ax, ax
        1 $:   1push
end-code

\ change device number 0 - A, 1 - B ...

code change-drv ( n -- )
                mov     ah, # $0E
                pop     dx
                and     dx, # $0F
                int     $21
                next
end-code

\ Convert count string to  ASCIIZ string

code count>z    ( a -- )
                cld
                pop     bx
                xor     ch, ch
                mov     cl, 0 [bx]              \ set counter
                or      cl, cl                  \ if 0, stop
                jz      1 $
                xchg    bx, si                  \ save si, set si to the begin-
                                                \ ning 1st name in buffer
                mov     di, si                  \ address where
                inc     si                      \ address from
                push    es
                mov     ax, cs
                mov     es, ax                  \ segment where
                mov     ds, ax                  \ segment from
                repnz   movsb
                xor     al, al                  \ add 0 to the string end
                stosb
                pop     es
                mov     ax, cs
                mov     ds, ax
                mov     si, bx
        1 $:    next
end-code


code z>count    ( a -- )
                cld
                pop     bx
                push    bx
                xor     cx, cx
                xchg    bx, si                  \ save si, set si to the
                                                \ beginning 1st name in buf.
                push    es
                mov     ax, cs
                mov     es, ax                  \ segment from
                mov     ds, ax                  \ segment where
                lodsb
                or      al, al
                jz      10 $                    \ if 0, stop
        1 $:    inc     cl
                lodsb
                or      al, al
                jnz     1 $
                dec     si                      \ address correction from
                mov     di, si                  \ address where
                dec     si                      \ address correction from
                mov     dx, cx                  \ save counter
                std
                repnz   movsb
                cld
        10 $:   pop     es
                mov     ax, cs
                mov     ds, ax
                mov     si, bx
                pop     bx
                mov     0 [bx], dl              \ counter init
                next
end-code

\ prepeare string with counter in memory for screen output by TYPE,

code n>type     ( a -- )
                cld
                pop     bx
                push    bx
                xor     cx, cx                  \ cx - total string length
                xor     dx, dx                  \ dx - extending length
                xchg    bx, si                  \ save si, set si to the begin-
                                                \ ning 1st name in buf.
                mov     di, si                  \ address where
                push    es
                mov     ax, cs
                mov     es, ax                  \ segment where
                mov     ds, ax                  \ segment from
                lodsb
                or      al, al
                jz      10 $                    \ if 0, stop
                cmp     al, # ascii .           \ code .
                jz      8 $
                add     di, # 14                \ di - to the buf. end
        1 $:    inc     cl
                lodsb
                cmp     al, # ascii .           \ extention found
                jz      2 $
                or      al, al                  \ file end found
                jnz     1 $
                jmp     5 $
        2 $:    inc     dx
                lodsb
                or      al, al                  \ find file name end
                jnz     2 $
                xchg    dx, cx
                std
                mov     al, # $20               \ blank code (bl register !)
                push    cx
                sub     cx, # 5
                neg     cx
                repnz   stosb
                pop     cx
                dec     cx
                dec     si
                dec     si
                repnz   movsb
                mov     cx, dx
                stosb
                sub     cx, # 8
                jmp     6 $
        5 $:    mov     al, # $20               \ blank code (bl register !)
                mov     dx, cx
                sub     cx, # 13
        6 $:    neg     cx
                std
                repnz stosb
                mov     cx, dx
                mov     si, di
                dec     si
                dec     si
                repnz   movsb
                stosb
                mov     ax, # 14
                stosb
                cld
                jmp     10 $
        8 $:    mov     ax, # $0E
                mov     cl, al
                stosb
                lodsb
                cmp     al, # ascii .           \ code .
                mov     al, # $20               \ blank code (bl register !)
                stosb
                mov     ax, # ascii .
                jnz     9 $
                dec     cx
                stosb
        9 $:    stosb
                dec     cx
                dec     cx
                mov     al, # $20
                repnz   stosb
        10 $:   pop     es
                cld
                mov     ax, cs
                mov     ds, ax
                mov     si, bx
                pop     bx
                next
end-code

code first-find ( adr,attr -- t/f )
                xor     bx, bx
                mov     ah, # $4E
                pop     cx
                pop     dx
                int     $21
                jnc     1 $
                dec     bx      \ error. in BX set true
        1 $:    push    bx
                next
end-code

code next-find  ( -- t/f )
                xor     bx, bx
                mov     ah, # $4F
                int     $21
                jnc     1 $
                dec     bx      \ error
                push    ax
        1 $:    push    bx
                next
end-code

code dta@       ( -- d )
                mov     ah, # $2F
                mov     cx, es
                int     $21
                push    es
                push    bx
                mov     es, cx
                next
end-code

code dta!       ( d -- )
                mov     cx, ds
                pop     ds
                pop     dx
                mov     ah, # $1A
                int     $21
                mov     ds, cx
                next
end-code

code curr-drv   ( - n )
                mov     ah, # $19
                int     $21
                xor     ah, ah
                add     ax, # ascii A
                1push
end-code


code sys-del    ( a,t/f -- t,/f )
                xor     bx, bx
                mov     cx, ds
                pop     ax
                pop     dx
                or      ah, al
        0= if   mov     ah, # $3A       \ delete catalog
        else    mov     ah, # $41       \ delete file
        then    int     $21
                jnc     1 $
                dec     bx
        1 $:    push    bx
                mov     ds, cx
                next
end-code

\ file renaming
\ steck top   : a1 - address ASCIIZ chain old name  ,
\               a2 - address ASCIIZ chain new name .

code sys-ren    ( a1,a2 -- t/f )
                xor     cx, cx
                pop     bx
                pop     dx
                push    ds
                push    es
                push    di
                mov     ax, cs
                mov     es, ax
                mov     ds, ax
                mov     di, bx
                mov     ah, # $56
                int     $21
                pop     di
                pop     es
                pop     ds
                jnc     1 $
                dec     cx
        1 $:    push    cx
                next
end-code

\ сбросить атрибуты файла

code drop-atr   ( a -- t,/f )
                mov     bx, ds
                xor     cx, cx
                pop     dx
                mov     ax, # $4301
                int     $21
                mov     cx, # 0
                jnc     1 $
                dec     cx
        1 $:    push    cx
                mov     ds, bx
                next
end-code

\ преобразовать номер файла (каталога) в координаты X и Y для печати на экран

code n>at       ( n -- x,y )
                pop     ax
                mov     cl, # 19
                div     cl
                xor     bx, bx
                xchg    al, bl
                xchg    ah, al
                mov     bl, X-COORD [bx]
                add     ax, # Y-COORD
                push    bx
                push    ax
                next
end-code

\ Переслать атрибуты файла, его длинну и ASCIIZ имя в буфер

code +file      ( -- )
                cld
                mov     bx, si                  \ сохранить si
                xor     cx, cx                  \ инициализировать счетчик
                mov     di, end-files           \ адрес куда
                mov     ax, &> dir-seg          \ сегмент куда
                push    cs
                pop     ds                      \ сегмент откуда
                push    es
                mov     es, ax
                mov     si, # u-dta 26 +        \ адрес длинны файла
                lodsw                           \ переслать длинну файла
                stosw
                lodsw
                stosw
                mov     si, # u-dta 30 +        \ адрес имени файла
        1 $:    lodsb                           \ переслать имя
                cmp     al, # ascii A 1-
                jle     2 $
                cmp     al, # ascii Z
                jg      2 $
                add     al, # 32                \ преобразовать в строчные
        2 $:    or      al, al
                stosb
                loopnz  1 $
                pop     es
                mov     ax, cs
                mov     ds, ax
                mov     si, bx
                mov     end-files di            \ сохранить для следующего
                inc     word n-files            \ увеличить счетчик файлов
                next
end-code

\ прочитать имя файла из буфера файлов в буфер пользователя

code @file      ( a,n -- )
                cld
                mov     bx, si                  \ сохранить si
                pop     cx                      \ инициализировать счетчик
                pop     di                      \ адрес куда
                mov     ax, cs                  \ сегмент куда
                mov     ds, &> dir-seg          \ сегмент откуда
                push    es
                mov     es, ax
                mov     si, # 0                 \ начало 1-го имени в буфере
                or      cx, cx
                jz      3 $                      \ поиск не нужен
                mov     dx, # 4
        1 $:    add     si, dx                  \ пропустить длинну файла
        2 $:    lodsb                           \ сканирование списка
                or      al, al                  \ конец файла?
                jnz     2 $
                loop    1 $
        3 $:    lodsw                           \ переслать длинну файла
                stosw
                lodsw
                stosw
        4 $:    lodsb                           \ переслать имя
                or      al, al
                stosb
                loopnz  4 $
                pop     es
                mov     ax, cs
                mov     ds, ax
                mov     si, bx
                next
end-code

\ Переслать ASCIIZ имя каталога в буфер изменив порядок литер на обратный

code +catalog   ( -- )
                cld
                inc     word n-catalogs
                mov     bx, si                  \ сохранить si
                xor     cx, cx                  \ инициализировать счетчик
                mov     di, end-catalogs        \ адрес куда
                mov     ax, &> dir-seg          \ сегмент куда
                mov     si, # u-dta 30 +        \ адрес откуда
                push    cs
                pop     ds                      \ сегмент откуда
                push    es
                mov     es, ax
                lodsw
                dec     si
                dec     si
                cmp     ax, # $002E
                jnz     1 $
                dec     n-catalogs
                jmp     2 $
        1 $:    lodsb
                std
                stosb
                cld
                or      al, al
                loopnz  1 $
        2 $:    pop     es
                mov     ax, cs
                mov     ds, ax
                mov     si, bx
                mov     end-catalogs di
                next
end-code

code @catalog   ( a,n -- )
                cld
                mov     bx, si                  \ сохранить si
                mov     si, end-seg             \ начало 1-го имени в буфере
                mov     ds, &> dir-seg          \ сегмент откуда
                pop     cx                      \ инициализировать счетчик
                pop     di                      \ адрес куда
                push    es
                mov     ax, cs                  \ сегмент куда
                mov     es, ax
                or      cx, cx
                jz      3 $                     \ поиск не нужен
        1 $:    std
                lodsb                           \ сканирование списка
                cld
                or      al, al                  \ конец файла?
                jnz     1 $
                loop    1 $
        3 $:    std
                lodsb                           \ переслать имя
                cld
                stosb
                or      al, al
                loopnz  3 $
                pop     es
                mov     ax, cs
                mov     ds, ax
                mov     si, bx
                next
end-code

\ получить длинну файла из DTA или буфера с аналогичным форматом записи длинны
\ на стек - число двойной длинны

code @long      ( a -- d )
                pop     bx
                mov     ax, 0 [bx]
                mov     cx, 2 [bx]
                push    ax
                push    cx
                next
end-code

\ получить имя файла или каталога в буфер true - имя файла, 0 - каталога.

: @name         ( a,n -- t/f )
                ~offset @ + n-catalogs @ 2dup <
                if drop @catalog 0 else - @file true then ;

: z-type        ( ASCIIZ -- )
                dup n>type count type ;

: messeg-window ( --)
                17 10 do 5 i at 70 spaces loop ;

: please.wait   ( -- )
                >rev
                16 10 do  3 i at 48 spaces loop
                8 11 at 36 ascii ─ draw-line ." ┌" 44 11 at ." ┐"
                8 12 at ." │         Reading directory.        │"
                8 13 at ." │         Please wait.              │"
                8 14 at 36 ascii ─ draw-line ." └" 44 14 at ." ┘"
                >norm ;

: scoops-dir    ( -- )
                savescr please.wait
                n-catalogs 0! n-files 0! end-files 0!
                end-seg @ end-catalogs !
                dta@ u-dta ?cs: dta!            \ пользовательский DTA
                mask*.* $37 first-find          \ получить имя первого файла
                first-file? !
                begin
                [ u-dta 21 + ] literal c@       \ считать атрибут и пересылать
                $10 and if +catalog then        \ в буфер, если каталог
                next-find until drop
                mask-buf $23 first-find 0=      \ получить имя первого файла
                if
                  begin +file next-find
                  until drop
                then
                dta! restscr ;                  \ восстановить DTA

\ собрать имя текущего файла, путь к файлу и имя устройства в буфере имени

: scoops-name   ( a -- )
                path-buf count >r ren-buf 1+ r@ cmove
                path-buf c@ 3 <>
                 if ascii \ ren-buf r@ + 1+ c!
                 else r> 1- >r
                 then
                name-buf + dup z>count                  \ смещение со стека
                count ren-buf r@ + 2+ swap dup>r cmove
                r> r> + 1+ ren-buf c!
                true ren-buf dup c@ + ren-buf
                do i c@ ascii . = if drop 0 leave then loop
                if ascii . ren-buf count + c! ren-buf incr then ;

: .time-key     ( -- )
                begin   key?  mousebutton or
                until   >norm ;

: wait-esc      ( -- )
                7 emit 20 15 at 6 spaces ." Press Esc to continue ... "
                6 spaces >norm
                begin   .time-key key
                        27 =
                until   >norm ;

: .da           ( -- )
                ."   Yes  " ;

: .net          ( -- )
                ."   No   " ;

: mouse-y/n     ( -- 27/13/0 )
                mousebutton 2 = if 27 exit then
                show.ms mousexy hide.ms 15 =
                if
                  dup 22 28 ubetween if drop 13 exit then    \ код ENTER
                  dup 50 56 ubetween if drop 27 exit then    \ код ESC
                then
                drop false ;

: ?yes/no       ( x1,y1,x2,y2,t/f -- t/f )
                >r
                begin
                 2dup at r@ if >norm else >rev  then .net 2swap
                 2dup at r@ if >rev  else >norm then .da  2swap
                 >norm show.ms .time-key hide.ms
                 mousebutton
                 if mouse-y/n else key then
                 dup 27 = if r> 2drop 2drop 2drop 0 exit then 13 <>
                while
                 r> -1 xor >r
                repeat 2drop 2drop r> ;

: .at-path      ( -- )
                get-attrib10 17 1 at 60 spaces
                17 1 at  nosetcur 0! path-buf count type
                path-buf c@ 3 <> if ascii \ emit then
                mask-buf dup dup z>count count type count>z
                true nosetcur ! >norm ;
: at-name       ( a -- )        \ со стека смещение для scoops-name
                >norm 8 13 at 64 spaces 10 13 at scoops-name
                ren-buf count type ;

: qweshn-file   ( -- )
                19 11 at  ." ─── Do you wish to delete this file? ───"
                4 at-name ;

: qweshn-cat    ( -- )
                19 11 at  ." Do you wish to delete this subdirectory? "
                0 at-name ;

: qweshn-del    ( -- t/f)
                get-attrib6
                14 16 \ curr-color @ 1 and 1 <> if swap then
                21 over at 9 ascii ▀ draw-line
                49 swap at 9 ascii ▀ draw-line
                21 over at 9 ascii ▄ draw-line
                49 swap at 9 ascii ▄ draw-line
                >norm 21 15 at 9 spaces  49 15 at 9 spaces
                22 15 50 15 true ?yes/no ;

: set-flash     ( -- )
                attrib @ $80 or attrib ! ;

: fill-screen   ( -- )
                curr-name 0! n-files @ n-catalogs @ + 57 0
                do i n>at at i over <
                  if
                   name-buf i @name name-buf swap ?4+ z-type
                  else
                   14 spaces
                  then
                loop drop first-file? @
                if 0 n>at at ."   No files.  "
                   2 23 at get-attrib10 33 spaces >norm
                then ;

: get-marker    ( n -- )
                dup n-catalogs @ n-files @ + 1- >
                if drop exit then             \ имя с этим номером недоступно
                curr-name @ n>at at           \ установить курсор
                name-buf curr-name @ @name
                name-buf swap ?4+ >norm z-type
                2 23 at get-attrib10
                name-buf over @name
                 if name-buf @long name-buf 4 + dup z-type
                    -rot 10 d.r ."  bytes. "
                 else name-buf dup  @ $2E2E =
                        if   name-buf n>type 33 spaces
                        else name-buf z-type ."   Subdirectory.  "
                        then
                 then
                >rev over n>at at get-attrib4 count type >norm
                curr-name ! ;

: string>buf    ( a1,count,a2, -- )
                dup>r 1+ swap dup>r cmove r> r> c! ;

: .newpage      ( -- )
                ~offset 0! .at-path scoops-dir fill-screen ;

: f-delete      ( -- )
                first-file? @ ?exit
                savescr get-attrib2 messeg-window >norm
                ?newpage 0! name-buf curr-name @ @name
                if get-attrib2 qweshn-file qweshn-del dup ?newpage !
                  if ren-buf count>z ren-buf true sys-del
                     if get-attrib2 set-flash 5 9 at
                        ."   Attention! This file is marked read-only!    "
                        qweshn-del dup ?newpage !
                      if ren-buf drop-atr drop ren-buf
                         true sys-del drop
                      then
                     then
                  then
                else get-attrib2 name-buf @ $2E2E =
                 if 18 12 at  ." Can't delete this directory!"
                   wait-esc >norm ?newpage 0!
                 else qweshn-cat qweshn-del dup ?newpage !
                   if ren-buf count>z ren-buf false sys-del
                      if get-attrib2 set-flash 5 9 at
                         ."   Attention! This subdirectory it is not empty! "
                         ." Can't delete!" 6 spaces ?newpage 0!
                         wait-esc >norm
                      then
                   then
                 then
                then
                restscr ?newpage @
                if .newpage 0 get-marker then ;

: mem-path      ( -- )
                savescr get-attrib2 messeg-window 18 12 at
                3 spaces ." Save current directory?"
                qweshn-del if path-buf old-path over c@ 1+ cmove then
                restscr  ;

: init-path-buf ( -- )
                [ ascii : ascii \ join ] literal path-buf 1+ !
                curr-drv path-buf c! curr-path path-buf z>count ;

\ проверка правильности указания пути пользователем.
\ если не указан признак корневого каталога в path-buf добавляется
\ текущий путь. Если общая длинна пути превышает 65 байтов, то
\ устанавливается ошибка.

: scan-buf\     ( a -- n, a )
                count swap over +
                begin swap dup
                while 1- swap 1- dup c@ ascii \ = ?exit
                repeat swap ;

: scan-buf:     ( a -- n, a )
                count swap over +
                begin swap dup
                while 1- swap 1- dup c@ ascii : = ?exit
                repeat swap ;

: scan-buf:\    ( a -- n, a )
                count swap over +
                begin swap dup
                while 1- swap 1- dup @ [ ascii : ascii \ join ] literal
                      = ?exit
                repeat swap ;

\ добавить к имени в буфере пути текущее устройство и путь,
\ если путь и устройство не указаны пользователем.

: ?addcurrent   ( -- )
                path-buf 2+ @ [ ascii : ascii \ join ] literal = ?exit
                path-buf ren-buf 70 cmove init-path-buf
                ren-buf count drop c@ ascii \ =
                if 2 path-buf c!
                else path-buf c@ 3 <>
                 if ascii \ path-buf count + c! path-buf incr then
                then
                ren-buf count >r path-buf count + r@ cmove r> path-buf c+! ;

\ если в буфере одиночный символ и он "\", то  установить корневой каталог
\ текущего устройства. Если в строке есть ":" и путь не указан,
\ то добавить текущий путь,

: ?error-path   ( -- t/f )
                path-buf count 1 = swap c@ ascii \ = and
                 if init-path-buf 3 path-buf c! then
                path-buf scan-buf: drop 0<> path-buf c@ 2 = and
                 if 0 init-path-buf
                 else
                   ?addcurrent path-buf dup count>z set-path path-buf z>count
                 then ;

: path-select   ( -- )
                savescr get-attrib5 messeg-window messeg-window
                 13 11 at
                ." Type in a new Directory Specification. "
                >norm 8 13 at 64 spaces 10 13 at ." Path: "
                begin
                  [ also hidden ]
                  17 13 path-buf PATH-LONG cursor-on
                  on> autoclear ['] >norm is >edattrib lineeditor cursor-off
                  [ previous    ]
                  drop path-buf count upper
                  path-buf scan-buf: drop
                  if path-buf 1+ c@ ascii A - change-drv then
                  ?error-path dup
                while drop set-flash 22 15 at
                  ."   This path is not correct!     "
                  >norm 7 emit
                repeat
                restscr 0= if .newpage 0 get-marker then ;

: mask-select   ( -- )
                savescr get-attrib5 messeg-window
                15 11 at ." Enter new mask from selection files."
                >norm 8 13 at 64 spaces
                mask-buf z>count 15 13 at  ."  Current mask: "
                [ also hidden ]
                31 13 mask-buf MASK-LONG 1- cursor-on
                on> autoclear ['] >norm is >edattrib lineeditor cursor-off
                [ previous    ]
                drop mask-buf count>z restscr .newpage 0 get-marker ;

\ для правильной работы функции в корневом каталоге надо убрать из буфера
\ имя устройства и \

: ?>root        ( a -- a/a+3 )
                dup scan-buf\ drop 2 = if 3 + then ;

: ren-select    ( -- )
                first-file? @ ?exit
                name-buf dup curr-name @ @name
                if   4 +  true                          \ Файл
                else @ $2E2E = ?exit name-buf false     \ Каталог
                then
                savescr get-attrib5 messeg-window
                if   15 11 at ."    Rename the file:   "
                else 11 11 at ." Rename the subdirectory:  "
                then
                dup dup z>count count type
                ascii \ path-buf count dup>r + c! 1 path-buf c+!
                path-buf ren-buf over c@ 1+ cmove
                dup count >r path-buf count + r@ cmove
                r> path-buf c+!
                15 13 at ."    Enter new name:" >norm
                [ also hidden ]
                dup 37 13 rot 14 cursor-on on> autoclear
                ['] get-attrib5 is >edattrib ['] >norm >is @ >r
                ['] get-attrib5 is >norm
                lineeditor cursor-off r> is >norm
                [ previous    ]
                if dup count >r ren-buf count + r@ cmove r> ren-buf c+!
                   ren-buf path-buf dup c@ comp
                   if ren-buf count>z path-buf count>z
                      path-buf ?>root ren-buf ?>root sys-ren
                      if get-attrib1 5 9 at set-flash
                         ."    This name already exist!   "
                         >norm wait-esc
                      else scoops-dir
                      then path-buf z>count
                   then
                then drop r> path-buf c! restscr
                name-buf dup curr-name @ dup n>at at
                @name ?4+ >rev z-type >norm ;

\   Первоначально производится проверка имени, так как имя родительского
\ каталога переименовать нельзя. Затем:
\  1 сохранить экран
\  2 сохранить текущее значение длинны строки в буфере пути,
\  3 переслать путь в буфер для переименования,
\  4 вывести текущее имя и запрос нового имени файла,
\    (в случае отказа от переименования идти к 7)
\  5 сформировать ASCIIZ строки в буферах пути и буфере переименования
\  6 при неудачном переименовании дать сообщение об ошибке,
\  7 восстановить экран строку пути в буфере пути и закончить.

: change-cat    ( -- )
                path-buf count>z path-buf set-path drop path-buf z>count
                .newpage ;

: gotoroot      ( -- )
                3 path-buf c! change-cat 0 get-marker ;

: finde-name    ( -- )
                first-file? @ ?exit
                0 n-catalogs @ 0
                do name-buf dup i @name drop dup z>count
                   ren-buf dup c@ 1+ comp
                   0= if drop i leave then
                loop ;

: file-select   ( -- )
                first-file? @ ?exit
                name-buf curr-name @ @name
                if $5555 ?end-select ! 4 scoops-name exit then
                name-buf @ $2E2E =
                if path-buf scan-buf\
                   ren-buf 14 cmove >r path-buf c@ r@ - 1- ren-buf c!
                   r> 3 max path-buf c! change-cat
                   finde-name get-marker
                else
                  name-buf z>count name-buf count >r path-buf count
                  dup 3 = if + else + ascii \ over c! 1+ path-buf incr then
                  r@ cmove r> path-buf +! change-cat 0 get-marker
                then ;

: f-unselect    ( -- )
                $AAAA ?end-select ! ;

: find-liter    ( n -- n )
                first-file? @ ?exit
                upc old-litera @ over <>
                if   dup old-litera ! ~offset 0! 0 true
                else curr-name @ 1+ 0
                then  ?newpage ! >r
                begin n-files @ n-catalogs @ + r@ ~offset @ + 1+ <
                      if r>drop drop 7 emit exit then
                      name-buf dup r@ @name ?4+ c@ upc over <>
                while r> 1+ >r
                repeat drop r@ 56 r> <
                if  56 - ~offset +! true ?newpage ! 56
                then
                ?newpage @ if fill-screen then
                get-marker ;

: marker-left   ( -- )
                curr-name @ 19 - dup 0 <
                if drop else get-marker then ;

: marker-right  ( -- )
                first-file? @ ?exit
                curr-name @ 19 + n-files @ n-catalogs @ + 1- min
                56 min get-marker ;

: nbuftype      ( t/f -- )
                name-buf swap ?4+ z-type ;

: marker-up     ( -- )
                curr-name @
                if curr-name @ 1- get-marker
                else ~offset @
                 if name-buf 0 @name 0 n>at at nbuftype
                    ~offset decr scr-down 17 3 at
                    ."  │ "  name-buf 19 @name nbuftype
                    ."  │ "  name-buf 38 @name nbuftype
                  0 get-marker
                 then
                then ;

: marker-down   ( -- )
                first-file? @ ?exit
                ~offset @ curr-name @ + 1+ n-files @ n-catalogs @ + =
                if exit then
                curr-name @ 56 =
                if name-buf 56 @name 56 n>at at nbuftype
                    ~offset incr scr-up
                    name-buf 18 @name 18 n>at at nbuftype
                    ."  │ "  name-buf 37 @name nbuftype
                    ."  │ "
                  56 get-marker
                else
                curr-name @ 1+ get-marker
                then ;

comment:

\ дополнительные функции

: in-page-up    ( -- )
                curr-name @ 19 / 19 * get-marker ;

: in-page-down  ( -- )
                first-file? @ ?exit
                19 curr-name @ 19 mod - curr-name @ + 1-
                n-files @ n-catalogs @ + 1- min
                56 min get-marker ;
comment;

: marker-home   ( -- )
                ~offset @
                if   ~offset 0! fill-screen then
                0 get-marker ;

: marker-end    ( -- )
                first-file? @ ?exit
                n-files @ n-catalogs @ + 57 over <
                if 57 - ~offset ! fill-screen 57 then
                1- get-marker ;

: page-up       ( -- )
                ~offset @
                if
                 ~offset @ 57 - 0<
                  if ~offset 0!
                  else -57 ~offset +!
                  then fill-screen
                else
                then 0 get-marker ;

: page-down     ( -- )
                first-file? @ ?exit
                n-files @ n-catalogs @ + 57 over <
                if 57 ~offset +! ~offset @ 57 + over >
                  if 57 - ~offset !
                  else drop
                  then fill-screen 57
                then
                1- get-marker ;

128 funcarray d-functable ' d-functable value functable

   @> keysfuncptr         ' d-functable =: keysfuncptr

        ' marker-up     72 $80 or lkey!           \  
        ' marker-down   80 $80 or lkey!           \  
        ' marker-left   75 $80 or lkey!           \  
        ' marker-right  77 $80 or lkey!           \  
        ' page-up       73 $80 or lkey!           \ Pg Up
        ' page-down     81 $80 or lkey!           \ Pg Down
        ' marker-home   71 $80 or lkey!           \ Home
        ' marker-end    79 $80 or lkey!           \ End

\       ' .help         59 $80 or lkey!           \ F1
        ' page-up       60 $80 or lkey!           \ F2
        ' mask-select   61 $80 or lkey!           \ F3
        ' page-down     62 $80 or lkey!           \ F4
        ' path-select   63 $80 or lkey!           \ F5
        ' ren-select    64 $80 or lkey!           \ F6
        ' f-delete      66 $80 or lkey!           \ F8
        ' mem-path      67 $80 or lkey!           \ F9
        ' f-unselect    68 $80 or lkey!           \ F10
        ' f-unselect   113 $80 or lkey!           \ alt~F10

         =: keysfuncptr

create mousefuncarray
        ' marker-up      ,                        \  
        ' marker-down    ,                        \  
        ' marker-right   ,                        \  
        ' marker-left    ,                        \  
        ' marker-home    ,                        \ Home
        ' marker-end     ,                        \ End
        ' gotoroot       ,                        \ ^\ root
        ' noop           ,                        \ F1
        ' page-up        ,                        \ F2
        ' mask-select    ,                        \ F3
        ' page-down      ,                        \ F4
        ' path-select    ,                        \ F5
        ' ren-select     ,                        \ F6
        ' f-delete       ,                        \ F8
        ' mem-path       ,                        \ F9
        ' f-unselect     ,                        \ F10
        ' file-select    ,                        \ ENTER

: funcmouse     ( x,y -- )
                dup 4 20 between
                if
                  4 - 2* mousefuncarray + @ execute drop
                else
                  2drop
                then ;

: getmouse     ( x,y -- )
                dup 3 21 between
                if 2dup join x&y.mouse @ over x&y.mouse ! =
                   mousebutton 1 = and
                  if file-select 2drop x&y.mouse 0!
                  else 3 - swap 2 + 19 / 19 * + get-marker
                  then
                else 2drop
                then ;

: mouse-normir  ( -- )
                mousexy hide.ms dup 1 =
                if 2drop path-select exit then
                over 52 >
                if funcmouse
                else getmouse
                then show.ms ;

: key-select    ( -- )
                mousebutton if mouse-normir exit then
                key             \ 02/03/92 20:57 tjz changed from KEY-F
                dup 127 >       \ 02/03/92 20:57 tjz changed from SPLIT
                if
                  $7F and functable execute
                else
                 case
                    13 of file-select endof             \ Enter
                    28 of gotoroot    endof             \ Ctrl~\
                    27 of f-unselect  endof             \ Esc
                          find-liter
                 endcase
                then show.ms ;

: dir-alloc     ( -- t/f )
                dir-seg 0=
                if true get-attrib5 messeg-window 17 13 at
                   ." Insufficent memory. Press any key." >norm  7 emit
\                    ." Мало памяти. Нажмите любую клавишу." >norm  7 emit
                    key drop
                 else 0
                 then ;

: dir-dealloc   ( -- )
                unpointer> dir-seg ;

: init-getfile  ( -- t/f ) \ true - переполнение озу инициализация невозможна.
                ~offset 0! find-buf 0! old-litera 0!
                dir-alloc
                init-path-buf
                dup 0=
                if path-buf old-path over c@ 1+ cmove
                   " *.*"  mask-buf string>buf mask-buf count>z
                   scoops-dir fill-screen  curr-name 0!
                then ;

: restore-path  ( -- )
                old-path 1+ c@ ascii A - change-drv old-path c@ 2 =
                if 3 old-path c! ascii \ old-path 3 + c! then
                old-path dup count>z set-path drop ;

: string-screen ( x,y -- )
                at ." │" 16 spaces ." │" 16 spaces
                ." │" 16 spaces ." │ " get-attrib10 ;

: ?stringscreen ( x,y -- )
                string-screen curr-color @ 1 = if 7 >fg then ;

: draw-screen   ( -- )
         >norm 1  0 at 77 ascii ═ draw-line ." ╒" 78 0 at ." ╕"
         1  1 at ." │" get-attrib10 ."  Current path:"
         62 spaces >norm ." │"
         1  2 at 77 ascii ═ draw-line ." ╞" 18 2 at ." ╤"
         35 2 at ." ╤" 52 2 at ." ╤" 78 2 at ." │"
         1  3 ?stringscreen ." ▀▀▀▀▀▀" >norm ."     ────  ────   │"
         1  4 string-screen ."      " >norm ."   marker up       │"
         1  5 string-screen ."      " >norm ."   marker down     │"
         1  6 string-screen ."      " >norm ."   marker right    │"
         1  7 string-screen ."      " >norm ."   marker end      │"
         1  8 string-screen ."  Home " >norm ."   marker to home  │"
         1  9 string-screen ."  End  " >norm ."   marker to end   │"
         1 10 string-screen  "   ^\  " type >norm ."   set root        │"
         1 11 string-screen ."       " >norm ."     ────  ────   │"
         1 12 string-screen ."   F2  " >norm ."   page up         │"
         1 13 string-screen ."   F3  " >norm ."   change mask     │"
         1 14 string-screen ."   F4  " >norm ."   page down       │"
         1 15 string-screen ."   F5  " >norm ."   select path     │"
         1 16 string-screen ."   F6  " >norm ."   rename          │"
         1 17 string-screen ."   F8  " >norm ."   delete          │"
         1 18 string-screen ."   F9  " >norm ."   save path       │"
         1 19 string-screen ."  Esc  " >norm ."   cancel          │"
         1 20 string-screen ."  Enter" >norm ."   select          │"
         1 21 ?stringscreen ." ▄▄▄▄▄▄" >norm ."     ────  ────   │"
         1 22 at 56 ascii ─ draw-line ." ├"
         18 22 at ." ┴" 35 22 at ." ┼" 52 22 at ." ┤"
         ."   Other keys next file   " >norm ." │"
         1 23 at ." │" 33 spaces ." │" get-attrib10 ."  Time           "
         >norm ." │" ."   starting with Letter.  "
         >norm ." │" 1 24 at 77 ascii ═ draw-line ." ╘"
         35 24 at ." ╧" 52 24 at ." ╧" 78 24 at ." ╛" ;

        also hidden also

: save-attrib   ( -- )
                [ ' >norm     >is ] literal @ [ attribs 0 + ] literal !
                [ ' >rev      >is ] literal @ [ attribs 2 + ] literal !
                [ ' >edattrib >is ] literal @ [ attribs 4 + ] literal !
                                     attrib @ [ attribs 6 + ] literal !
                                     base   @ [ attribs 8 + ] literal ! ;

: rest-attrib   ( -- )
                [ attribs 0 + ] literal @ is >norm
                [ attribs 2 + ] literal @ is >rev
                [ attribs 4 + ] literal @ is >edattrib
                [ attribs 6 + ] literal @ attrib !
                [ attribs 8 + ] literal @ base ! ;

: color0        ( -- )
                blue >bg green >fg ;

: color1        ( -- )
                red >bg white >fg ;

: color2        ( -- )
                green  >bg white >fg ;

: color3        ( -- )
                white >fg blue >bg ;

: color4        ( -- )
                ltgray >bg black  >fg ;

: color5        ( -- )
                brown  >bg black  >fg ;

: color6        ( -- )
                blue   >bg white  >fg ;

: color7        ( -- )
                red    >bg black  >fg ;

: color7'       ( -- )
                red    >bg ltgray >fg ;

: color8
                black  >bg ltgray >fg ;

: color9        ( -- )
                cyan   >bg black  >fg ;

: color10       ( -- )
                cyan   >bg white  >fg ;
\                green  >bg black  >fg ;

: norm-color    ( -- )
                ['] color0      is get-attrib1
                ['] color1      is get-attrib2
                ['] color3      is get-attrib4
                ['] color6      is get-attrib5
                ['] color7'     is get-attrib6
                ['] color10     is get-attrib10
                ['] color5      is >rev
                ['] color4      is >norm
                ;

: rev-color    ( -- )
                ['] color0      is get-attrib1
                ['] color1      is get-attrib2
                ['] color3      is get-attrib4
                ['] color6      is get-attrib5
                ['] color7      is get-attrib6
                ['] color10     is get-attrib10
                ['] color4      is >rev
                ['] color8      is >norm
                ;

: get-w/b       ( -- )
                ['] color8      is get-attrib1
                ['] color8      is get-attrib2
                ['] color8      is get-attrib4
                ['] color8      is get-attrib5
                ['] color8      is get-attrib6
                ['] color8      is get-attrib10
                ['] color4      is >norm
                ['] color8      is >rev
                ;

: get-b/w       ( -- )
                ['] color4      is get-attrib1
                ['] color4      is get-attrib2
                ['] color4      is get-attrib4
                ['] color4      is get-attrib5
                ['] color4      is get-attrib6
                ['] color4      is get-attrib10
                ['] color8      is >norm
                ['] color4      is >rev
                ;

: get-attrib    ( -- )
                curr-color @ 3 and normval reverseval =
                if 1 or else 2 and then dup curr-color !
                exec:
                rev-color norm-color get-b/w get-w/b ;

        previous previous previous previous definitions

: (getfile)     ( -- )
                [ also getvoc also hidden ]
                savecursor savescr \ записать куpсоp и экpан
                save> base decimal
                save-attrib get-attrib >norm cursor-off
                nosetcur @ true nosetcur !
                draw-screen init-getfile 0=
                 if .at-path ?end-select 0!
                    cursor-off marker-home
                    begin .time-key key-select ?end-select @ until hide.ms
                 then
                dir-dealloc rest-attrib nosetcur !
                restore> base
                restscr restore-path
                ?end-select @ $5555 = if ren-buf true else 0 then
                restcursor
                [ previous previous ] ;

' (getfile) is getfile


\s : q     getfile if count type then cr ;

