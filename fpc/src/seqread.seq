\ SEQREAD.SEQ   Sequential read and load file           by Tom Zimmer

\ Link this file into the FILELIST chain.

FILES DEFINITIONS

VARIABLE SEQREAD.SEQ

FORTH DEFINITIONS

DECIMAL

DEFER OTHER.CHECK       ' NOOP IS OTHER.CHECK   \ allow extra termination
                        ( a1 f1 -- a1 f2 )      \ testing.

\       Read sequential lines from a file, delimited by CRLF.

    0 VALUE WITHPATH            \ should the PATH be included in the file var
16384 VALUE IBLEN               \ current input buffer length
16384 VALUE IBFULL              \ full buffer size, used to restore IBLEN

IBLEN 0 POINTER INBSEG          \ the input buffer

\ A couple of editor words, needed to give the information editor when
\ a compile error has occured.
    0 VALUE SCREENCHAR
  $0A VALUE DELIMITER

\ The value of OBLEN can be reduced to 64 if you want to read lines from
\ normal Forth BLOCK files. You should use BLKTOSEQ.SEQ for this though.

  255 VALUE OBLEN               \ output buffer length

    0 VALUE INSTART
    0 VALUE INLENGTH
   VARIABLE TOTALLINES
     CREATE OUTBUF OBLEN 1+ ALLOT \ the line output buffer

7 B/HCB * CONSTANT MAXNEST        \  maximum of 7 hcb's

       CREATE HNDLS MAXNEST B/HCB + ALLOT
        HNDLS ' SEQHANDLE >BODY !-T           \ PRESET POINTER
     VARIABLE FILEPOINTER 2 allot \ most recent read
     VARIABLE LOADING             \ Are we in the proccess of loading a file?
            0 LOADING !-T         \ initialize to not loading.
       CREATE CONHNDL B/HCB  ALLOT      1 CONHNDL >HNDLE !-T
       CREATE PRNHNDL B/HCB  ALLOT      4 PRNHNDL >HNDLE !-T

: CONSOLEL      ( SEG C1 -- )
                OUTPAUSE
                SWAP >R SP@ 1 CONHNDL R> EXHWRITE 2DROP
                #OUT INCR ;

: (CONSOLE)     ( C1 -- )
                ?CS: SWAP CONSOLEL ;

: PRINTL        ( SEC C1 -- )
                OUTPAUSE
                SWAP >R SP@ 1 PRNHNDL R> EXHWRITE 2DROP
                #OUT INCR ;

: (PRINT)       ( C1 --- )
                ?CS: SWAP PRINTL ;

: PRNTYPEL      ( SEG A1 N1 --- )
                OUTPAUSE
                0MAX ROT PRNHNDL SWAP EXHWRITE #OUT +! ;

: CONTYPEL      ( SEG A1 N1 --- )
                OUTPAUSE
                0MAX ROT CONHNDL SWAP EXHWRITE #OUT +! ;

: (TYPEL)       ( SEG A1 N1 --- )       \ External type, from other segment
                PRINTING @
                IF      PRNTYPEL
                ELSE    CONTYPEL
                THEN    ;

: (TYPE)        ( A1 N1 --- )
                ?CS: -ROT TYPEL ;

DEFER LOADSTAT  ' NOOP IS LOADSTAT

: SEQHANDLE+        ( --- A1 )
                seqhandle b/hcb + ;

: .SEQHANDLE        ( --- )
                seqhandle count type ;

CODE CURPOINTER ( handle --- double-current )
                pop bx
                add bx, # hndloffset
                mov bx, 0 [bx]
                sub cx, cx
                mov dx, cx
                mov ax, # $4201  \ from end of file
                int $21
                push ax
                push dx
                next
                end-code

\ : SAVEPOINTER   ( --- )
\                 seqhandle curpointer inlength 0 d- filepointer 2! ;

CODE GET_ALINE  ( --- a1 )
                push es                         \ Save ES for later restoral
                mov di, ' instart >body         \ Searching from INSTART
                mov ax, ' DELIMITER >body       \ Searching for line delimiter
                mov cx, ' inlength >body        \ for INLENGTH clipped to OBLEN
                cmp cx, ' oblen >body           \ if INLENGTH > OBLEN
              > if      mov cx, ' oblen >body   \ clip search length to OBLEN
                then    mov dx, cx              \ save search length in DX
          cx<>0 if      mov es, ' inbseg >body  \ searching INBSEG segment
                        repnz           scasb   \ Scan for Linefeed char
                then    sub dx, cx              \ DX = length of line
                sub ' inlength >body dx         \ subtract line from remaining
                mov outbuf dl byte              \ set the length of OUTBUF
                mov bx, si                      \ save IP for later restoral
                mov si, ' instart >body         \ moving from INSTART
                add ' instart >body dx          \ set start to after line
                mov cx, dx                      \ cx = length to move
                mov di, # outbuf 1+             \ moving to OUTBUF
                mov ds, ' inbseg >body          \ from INBSEG segment
                mov ax, cs      mov es, ax      \ to CODE segment
                TEST DI, # 1
                JZ 0 $
                JCXZ 0 $
                MOVSB
                DEC CX
        0 $:    SHR CX, # 1
                REPNZ           MOVSW
                RCL CX, # 1
                REPNZ           MOVSB
                mov ax, cs      mov ds, ax      \ restore DS
                mov si, bx                      \ restore IP
                inc loadline word               \ bump line counter
                mov bx, # filepointer           \ Set BX to point to FILEPOINTER
                sub cx, cx                      \ clear CX
                mov cl, outbuf                  \ read byte length of OUTBUF
                add 2 [bx], cx                  \ Add to 32bit contents of
                adc 0 [bx], # 0 word            \ FILEPOINTER
                pop es                          \ restore ES
                mov ax, # outbuf                \ return address of buffer
                1push           end-code

: FILLBUFF      ( --- )         \ Refill the input buffer.
                inbseg instart over 0 inlength cmovel
                %off> instart
                inlength iblen inlength -
                seqhandle inbseg exhread        \ perform the actual read
                %+!> inlength                   \ adjust buffer length
                                \
                                \ strip off any Control Z's at end of file by
                -6 -1           \ scaning the last 6 chars of the read buffer
                                \ when a FILLBUFF occurs.
                do      inlength i + 0< ?leave          \ leave if begin buf
                        inbseg inlength i + c@L 26 =    \ is it ^Z?
                        if      bl
                                inbseg inlength i + c!L \ change to BLANK
                        then
            -1 +loop    ( savepointer ) ;

CODE ?FILLBUFF  ( --- )         \ refill INBUF if needed
                cmp ' inlength >body # oblen 1+ word
            u>= if      next
                then                            \ If we got here, then
                mov ax, # ' fillbuff            \ go and re-fill the buffer
                jmp ax
                end-code

: %LINEREAD     ( --- a1 )      \ read a line delimited by CRLF
                ?fillbuff       \ re-fill buffer if needed.
                get_aline ;     \ returns line including CRLF

DEFER LINEREAD  ' %LINEREAD IS LINEREAD

: (DOERROR)     ( a1 n1 --- )
                2>r
                cr ." file = " .seqhandle
                ."  at Line " loadline @ u.
                cr outbuf count type cr
                >in @ 1- here c@ - 0MAX
                dup %!> screenchar 0
                ?do ASCII - emit loop
                ." ^-- " 2r> type space
                quit    ;

DEFER DOERROR   ' (DOERROR) IS DOERROR

$0A VALUE DEFBASE

: HEXBASE       ( --- )
                hex base @ %!> defbase ;

: DECIMALBASE   ( --- )
                decimal base @ %!> defbase ;

: NOBASE        ( --- )
                %off> defbase ;

: .BASE         ( --- )
                ." Current BASE in DECIMAL is "
                base @ dup decimal . base ! ;

: ?BASE_RESTORE ( f1 --- )              \ Restore the base to the default
                if      defbase ?dup    \ base if f1 = true, and defbase
                        if      base !  \ is not zero
                        then
                then    ;

: (?SERROR)     ( a1 n1 f1 --- )
                dup ?base_restore
                %@> loading
                if
                        if      2>R sp0 @ sp! printing off loading off
                                ['] <run> is run errfix
                                2R> doerror
                                quit                    \ error from disk
                        else    2drop                   \ no error comes here
                        then
                else    (?error)       \ command line error
                then    ;

' (?SERROR) IS ?ERROR

: ?SEQRANGE     ( --- )         \ Verify seqhandle points to a handle in
                                \ the HNDLS array
                seqhandle hndls maxnest over + between 0=
                abort" SEQHANDLE is not set to the HANDLE stack" ;

: SEQUP         ( --- )
                ?SEQRANGE
                seqhandle >hndle @ -1 >
                if      seqhandle b/hcb + dup hndls maxnest + U< 0=
                        abort" Nested too deeply on FLOAD, use CLOSEALL"
                        dup %!> seqhandle clr-hcb
                then    ;

: SEQINIT       ( --- )
                noop hndls %!> seqhandle
                hndls   maxnest over + swap
                do      i clr-hcb
         b/hcb +loop
                iblen paragraph (lit) inbseg >body 4 + ! \ set read buf size
\ set the pointer's size in paragraphs to the current size of IBLEN.
                inbseg 0= memchk                         \ force allocation
                conhndl clr-hcb " CON." conhndl ">handle
                1 conhndl >hndle !
                prnhndl clr-hcb " PRN." prnhndl ">handle
                4 prnhndl >hndle ! ;

: IBRESET       ( --- )
                %off> instart
                %off> inlength ;

: $HOPEN        ( A1 --- F1 )   \ Returns a boolean FALSE for open successful
                seqhandle hclose drop
                seqhandle $>handle
                seqhandle hopen
                ibreset ;

DEFER GETFILE       ( --- <a1> f1 )     \ return a1 filename addr and

' FALSE IS GETFILE                      \ Default to failed

: FILE>TIB      ( a1 --- )              \ given a counted string a1, insert it
                count \ 2dup type space   \ into the Terminal Input Buffer.
                2dup true -rot over + swap
                do      i c@ ASCII . =
                        if      drop false leave
                        then
                loop    >r dup #tib ! >in off
                tib swap cmove r>
                if      ASCII . #tib @ tib + c!
                        #tib incr
                then    ;

: GFL           ( --- )                 \ optionally prompt for file if non
                                        \ is currently in the TIB.
                more? 0=
                if      getfile 0= abort" No filename specified"
                        dup count type space
                        file>tib
                then    ;

: SEEK          ( d1 --- )  \ Move the filepointer in seqhandle to the offset
                seqhandle movepointer ;   \ specified by d1.

0 VALUE LISTVAR

: SHOWLINES     ( --- )         \ enable displaying of loaded lines
                -1 %!> listvar ;

: HIDELINES     ( --- )         \ disable displaying of loaded lines
                0 %!> listvar ;

CODE CRLF>BL'S  ( a1 --- a1 )   \ change CRLF at end of string to blanks
                pop bx          \ leaving the string address on the stack
                push bx         \ Same as -> DUP COUNT + 2- DUP @ $0D0A =
                mov al, 0 [bx]  \            IF 8224 SWAP ! ELSE DROP DROP ;
                sub ah, ah
                add bx, ax
                dec bx
                cmp 0 [bx], # $0A0D word        \ if line ends in CRLF
             0= if      mov 0 [bx], # 8224 word \ change then to blanks
                then    next
                end-code

CODE SETTIB     ( a1 --- )      \ Set TIB to counted string a1
                pop bx
                mov al, 0 [bx]
                inc bx
                mov 'tib bx
                sub ah, ah
                mov #tib ax
                mov >in # 0 WORD        \ 05/25/90 tjz
                next
                end-code

: %.LOADLINE    ( a1 --- a1 )
                cr loadline @ 5 u.r space
                dup count type ;

DEFER .LOADLINE         ' %.LOADLINE IS .LOADLINE

CODE ?.LOADLINE ( a1 --- a1 )
                mov cx, ' listvar >body
                inc cx
          cx<>0 if      next                    \ if LISTVAR = -1, leave
                then
                mov ax, # ' .loadline           \ if LISTVAR <> zero, continue
                jmp ax  end-code

CODE LENGTH.CHECK ( a1 --- a1 f1 )
                mov ax, # true
                mov cx, ' inlength >body        \ if read length <> 0
          cx<>0 if      1push                   \ then we aren't done
                then
                pop di                          \ get a copy of line buf ptr
                push di
                cmp 0 [di], # 0 byte            \ if line buffer <> 0
            0<> if      1push                   \ then we aren't done
                then
                mov ax, # false
                1push   end-code                \ else we are done

: FILLTIB       ( --- )
                lineread crlf>bl's ?.loadline settib ;

: SEQDOWN       ( --- )
                ?SEQRANGE
                seqhandle hclose drop           \ close the file
                seqhandle clr-hcb               \ clear the handle
                seqhandle b/hcb -               \ decrease handle by b/hcb
                hndls maxnest + b/hcb - umin    \ clip below stack end
                hndls umax %!> seqhandle        \ and above stack begin
                seqhandle >hndle @ -1 <>        \ if a file is open
                loading @ and                   \ and we are loading
                if      filepointer 2@
                        seqhandle movepointer   \ adjust file pointer
                        IBRESET                 \ reset read pointers
 ( rep )                >in @ loadline decr filltib >in !
                                                \ re-fill TIB,
                                                \ but preserve >IN
                then    ;

: CLOSE         ( --- )
                seqdown ;

: CLOSEALL      ( --- )
                ?SEQRANGE
                begin   seqhandle hndls u>
                while   seqdown
                repeat  seqdown ;

: <LOAD>        ( --- )
                loadstat
                ibfull %!> iblen         \ set maximum length read buffer
                true  %save!> loading
                      %save>  'tib
                      %save>  >in
                false %save!> #tib
                      %save>  loadline
                      %save>  run
                0 %!> screenchar
                begin   lineread
                        length.check    ( -- a1 f1 )
                        other.check     ( -- a1 f1 )
                while   crlf>bl's ?.loadline settib run
                repeat  drop
                %@> loadline %+!> totallines
                %restore> run
                %restore> loadline
                %restore> #tib
                %restore> >in
                %restore> 'tib
                %restore> loading
                loading @               \ if we are still loading,
                if      256   %!> iblen \ use a small read buffer
                else    ibfull %!> iblen \ else use the full size buffer
                then    ;

DEFER LOADER    ' <LOAD> IS LOADER

: >LINE         ( n1 --- )
                0.0 seqhandle movepointer
                0.0 filepointer 2!
                loadline off
                IBRESET
                1-      0MAX   ?dup
                if      0
                       ?do      lineread c@ 0= ?leave
                        loop
                then    ;


: LOADED,       ( --- )         \ refactored to support new view.seq by REP
                %save> 'tib
                %save> >in
                %save> #tib             \ save interpretation status
                seqhandle count
                withpath 0=             \ Should the PATH not be
                if                      \ included in the file VARIABLE?
                        begin   2dup
                                ASCII \ scan dup   \ skip the leading path
                        while   2swap 2drop
                                1 /string
                        repeat  2drop
                then
                %!> #tib                \ set #tib
                %!> 'tib                \ set tib to seqhandle
                %off> >in               \ clear >in
                %save> context          \ save current context
                %save> current          \  and current vocab state
                files definitions       \ select files vocabulary
                variable                \ make the header
                %restore> current
                %restore> context       \ restore vocabulary state
                %restore> #tib
                %restore> >in
                %restore> 'tib ;

 TRUE VALUE ?LOADED,    \ Allow disabling LOADED, for one time.
FALSE VALUE ?NOLOADED,

: <FLOAD>       ( --- )    \  refactored to support new view.seq by REP
                ?loaded,   ?noloaded, 0=  and
                if
                        loaded,
                        0 %save!> loadline
                        here 2- >r  \ save pointer to files variable  rep
                        0.0 seqhandle movepointer
                        0.0 filepointer 2!
                        loader
                                        \ make sure the variable still exists
                        r@ body> @rel>abs       \ is the CFA pointing to
                        ['] loading @rel>abs =  \ DOVAR ?
                        if    here r> ! \ store end in file variable    rep
                        else  r>drop    \ else discard the address      tjz
                        then
                else
                        %on> ?loaded,    \ do loaded, on following floads
                        0 %save!> loadline
                        0.0 seqhandle movepointer
                        0.0 filepointer 2!
                        loader
                then    %restore> loadline ;

: CHARREAD      ( --- c1 )      \ Read a character from the current file.
                loading @
        if      begin   %@> >in   %@> #tib =    \ If nothing in line
                        inlength 0> and         \ and input buf not empty
                while   ?fillbuff               \ Optionally refill buffer
                        filltib                 \ refill the TIB
                repeat
        then    %@> >in   %incr> >in   tib + c@ ;

: ?FILEOPEN     ( --- )                 \ Verify a file is open.
                seqhandle >hndle @ 0<
                abort" A file MUST be open to perform this operation." ;

: OK            ( --- )         \ Load currently open file
                ?fileopen
                IBRESET
                <fload> ;


: \S            ( n1 --- )              \ Ignore the rest of the file.
                seqhandle endfile 2drop \ Move to end of file
                loadline off
                IBRESET
                %@> #tib %!> >in ;      \ Ignore rest of line

