\\ EMMEXEC.SEQ   Shell to DOS, leave F-PC in Expanded Memory   by Tom Zimmer

  This is a support file for EXEC.SEQ. It's job is to reduce F-PC's
allocated memory size, shell out and perform a DOS command, re-adjust
F-PC's memory allocation back up to the normal level, and read F-PC back
into program memory from expanded memory, then deallocate the expanded
memory that was being used.

  The results obtained with this file along with EXEC.SEQ, is a
SHELL command for F-PC that consumes only 8k of program memory while
performing a DOS command as long as sufficient expanded memory is
available.

  This file is placed as low as possible in the meta-compile load sequence
to minimize the amount of memory kept during a a DOS Shell command.

{

FILES DEFINITIONS

VARIABLE EMMEXEC.SEQ

FORTH DEFINITIONS

$FE CONSTANT EMMSTK             \ Put stack just below Forth in header area
VARIABLE #FPCPAGES              \ number of 16k pages needed by F-PC
VARIABLE EMMPAGE#               \ page counter during F-PC program restore.
VARIABLE EMMHNDL                \ holds the number used to talk to EMM driver
VARIABLE EMMPARS                \ size in PARS to keep when shelling to DOS
VARIABLE EMMPARST               \ start in CS: of where we are saving from
VARIABLE USE-DISK               \ should we flush F-PC to disk?
       0 USE-DISK !-T           \ init to don't use disk
VARIABLE DSK-STATUS             \ saved F-PC to disk was ok?=FALSE
   $FFFF DSK-STATUS !-T         \ init to error condition

: DISKON        ( -- )          \ flush F-PC to disk enabled
                USE-DISK ON ;

: DISKOFF       ( -- )          \ flush F-PC to disk is disabled
                USE-DISK OFF
                DSK-STATUS ON ;

CREATE EXEC$      $100 ALLOT    \ the execute command line parameters
CREATE EXEC.PARAM $10  ALLOT    \ the EXEC parameter array

CREATE CMDPATH ,"-T \COMMAND.COM" 0 ,-T $40 ALLOT
CREATE EXTHNDL ,"-T FPCIMAGE.$$$" 0 ,-T $40 ALLOT

VARIABLE SS_SAVE                \ a place to save the stack segment
VARIABLE SP_SAVE                \ a place to save the stack pointer

\ WARNING !! These functions need a lot of setup before they can be used.
\ Study the functions in EXEC.SEQ before trying to directly use this word.
\ Normally this function is ONLY invoked by "$SYS".

\ SETBLOCK ****************************************************************
\
\ F-PC has already been moved out to expanded memory before <EMMEXEC>
\ ever gets called, so just shrink F-PC's memory block to the size we
\ have decided we need. About 8k.
\
LABEL SMALFPC   ( -- )          \ reduce memory used by F-PC
                mov ax, cs
                mov es, ax              \ ES = CS
                mov bx, emmpars         \ get new size in paragraphs
                mov ax, # $4A00
                int $21                 \ adjust memory smaller
                ret             end-code

\ SETBLOCK ****************************************************************
\
\ Re-adjust F-PC's memory block back up where it was before we flushed
\ ourselves to expanded memory.
\
LABEL EMM>FPC    ( -- )         \ get F-PC back from Expanded Memory
                mov ax, cs
                mov es, ax              \ ES = CS
                mov bx, cs: #pars       \ reset memory to original size
                mov ax, # $4A00
                int $21
\ RECOVER FPC FROM EMM ****************************************************
\
\ The hardest work is done here. Pull F-PC back into memory from expanded
\ memory.
\
                mov cs: emmpage# # 0 word       \ reset the current page
                begin   mov dx, cs: emmhndl     \ emm handle
                        mov bx, cs: emmpage#    \ logical page
                        mov ax, # $4400         \ map pages func & phy page
                        int $67                 \ expanded memory interrupt
                        xor si, si              \ source starts at offset 0
                        xor di, di              \ destination starts at 0
                        mov es, cs: emmparst    \ set destination into ES
                        mov ds, cs: page-frame  \ DS = physical page frame
                        mov cx, # $2000         \ move count WORDS = 16kbytes
                        repnz   movsw
                        add cs: emmparst # $400 word \ adjust to next page
                        inc cs: emmpage# word   \ next expanded memory page
                        mov bx, cs: emmpage#    \ get a copy of #pages moved
                        cmp bx, cs: #fpcpages   \ have we moved the pages yet?
             >= until
                mov ax, cs
                mov ds, ax                      \ restore data segment
\ DEALLOCATE PAGES ********************************************************
\
\ GIve back the expanded memory we were using to the DOS EMM driver.  If we
\ need to shell out again, we will just re-allocate what we need.
\
                mov dx, emmhndl         \ get the emm handle
                mov ah, # $45           \ deallocate pages function
                int $67
                mov al, ah              \ ah = status
                sub ah, ah
                mov emm-status ax
                mov cur_emm_hndl # -1 word      \ clear current handle
                ret             end-code

\ RECOVER FPC FROM DISK ***************************************************
\
LABEL DSK>FPC    ( -- )         \ get F-PC back from Disk
                mov ax, cs
                mov es, ax              \ ES = CS
                mov ds, ax              \ DS = CS
                mov bx, cs: #pars       \ reset memory to original size
                mov ax, # $4A00
                int $21                 \ set BLOCK size
                mov bx, # exthndl 68 +          \ get the file handle
                                                \ + 68 = HNDLOFFSET addr
                mov bx, 0 [bx]                  \ get real DOS handle
                xor cx, cx                      \ reset to start of file
                xor dx, dx
                mov ax, # $4200                 \ from start of file
                int $21                         \ file position
                mov cs: emmpage# # 0 word       \ reset the current page
                begin   mov ds, cs: emmparst    \ set destination into ES
                        xor dx, dx              \ offset is zero
\ already in BX         mov bx, # exthndl 68 +  \ the file handle variable
\ already in BX         mov bx, 0 [bx]          \ get real DOS handle
                        mov cx, # $4000         \ move count in bytes = 16k
                        mov ax, # $3F00         \ disk read
                        int $21                 \ read from disk file
                        add cs: emmparst # $400 word \ adjust to next page
                        inc cs: emmpage# word   \ next expanded memory page
                        mov cx, cs: emmpage#    \ get a copy of #pages moved
                        cmp cx, cs: #fpcpages   \ have we moved the pages yet?
             >= until
                mov ax, cs
                mov ds, ax                      \ restore data segment
\ already in BX         mov bx, # exthndl 68 +  \ the file handle variable
\ already in BX         mov bx, 0 [bx]          \ get real DOS handle
                mov ax, # $3E00                 \ close function
                int $21
                mov dx, # exthndl 1+            \ filename after count byte
                mov ax, # $4100                 \ file delete
                int $21
                ret             end-code

CODE <EXTEXEC>  ( --- return-code )     \ shell to DOS & save to EMM or DISK
                mov dx, # cmdpath 1+            \ DX contains command path
                mov sp_save sp          mov ss_save ss  \ Save SP and SS
                mov ax, # emmstk        mov sp, ax      \ SP becomes EMMSTK
                push es                                 \ save these regs
                push si
                push bp
                cmp emm-status # 0 word         \ was there Expanded memory?
     0= if      call smalfpc                    \ make segment small
        else    cmp dsk-status # 0 word         \ or use disk?
             0= if      call smalfpc            \ make segment small
                then
        then
\ EXEC ********************************************************************
\
\ The real Shell to DOS is performed here. We have shrunk ourselves to a
\ very small size, and given all possible memory back to DOS. Now we will
\ perform the DOS command the user requested.
\
                mov ax, cs
                mov es, ax              \ ES = CS
                mov bx, # exec.param
                mov ax, # $4B00
                int $21
             u< if      and ax, # $FF   \ ONLY when carry is non zero
                else    mov ax, # 0
                then
                push ax                 \ save AX, the error code
                cmp emm-status # 0 word         \ was there Expanded memory?
     0= if      call emm>fpc                    \ recover from Expanded Mem
        else    cmp dsk-status # 0 word         \ or use disk?
             0= if      call dsk>fpc            \ recover from Disk
                then
        then
\ RESTORE REGISTERS *******************************************************
\
\ Restore the registers we saved at the front end of this before returning
\ to F-PC.
\
                mov ax, cs
                mov ds, ax
                pop ax                  \ recover error code from small stack
                pop bp                  \ restore these registers
                pop si
                pop es
                mov ss, ss_save         \ Restore SP and SS
                mov sp, sp_save
                1push   end-code        \ ax contains error code

VARIABLE EMMSYSEND              \ end of expanded memory resident portion

