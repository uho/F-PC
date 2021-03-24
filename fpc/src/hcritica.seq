\ HCRITICA.SEQ  this file provides a special critical error handler
\ written by mike christopher  12/31/89 16:05:00.53
\ modified by Tom Zimmer       03/15/90 11:21:55.26
\ Added to FPC KERNEL.COM      05/25/90 16:08:09.42

FILES DEFINITIONS

VARIABLE HCRITICA.SEQ

FORTH DEFINITIONS

comment:
  Tom, if you desire to use this, you may do so without any restrictions.
  These words are in the public domain and may be used for any
  purpose comercial or otherwise

  Mike Christopher  28 Feb 1990


  These routines support DOS critical errors. If a critical error
  occurs, DOS gives the dreaded ---  Abort, Retry, Ignore ?  ( on xt's)
                                ---  Abort, Retry, Fail ?    (on AT's)

  To handle this more gracefully, you can use your own routine to decide
  what to do in response to a critical error (like the drive door being open)
  This particular incarnation simply instructs DOS that we wish to fail
  in the optimistic expectation that user software will catch the errors.
  In practice this is reasonable and it sure beats having vectors installed
  (memory allocations too!) when some operator decides to reply ABORT
  to DOS's default handler.

  The critical error interrupt differs from the Control C/Break interrupt
  because you can NOT just abort to forth!!! DOS expects to get control
  back and will NOT BE STABLE if you don't give control back!!

  This works well with F-PC because drive door problems result in a
  "unable to open" type of error. I humbly contend that since some
  vectors are snatched by F-PC, it should handle this itself.

  For More info, see Advanced MSDOS programming by Ray Duncan. (MS Press)
  (he's a fellow Forther too!)

Modifications by Tom Zimmer      03/15/90 11:22:10.25

  Re-ordered the save and restore vector functions, since DOS already
  restores the critical error handler when our program terminates, we
  need not do that. Note that whenever we shell out to DOS from Forth,
  the critical error handler gets reset to the DOS default, so we must
  re-install the handler when returning from a DOS shell (like DIR for
  example).

comment;

\ you may use hadcritical to see if the last thing you did caused a
\ critical error. If you are going to use it (its optional)
\  YOU must clear the flag after you've seen it.

VARIABLE HADCRITICAL        \ non-zero if critcal error has happened


LABEL CRITINT                           \ handle a critical error interrupt
                STI                     \ re-enable interrupts
                INC CS: HADCRITICAL WORD
                                        \ set when critical error occurs
                MOV AL, # 0             \ tell DOS to ignore the error
                IRET                    \ return from interrupt
                END-CODE

CODE SETCRITICAL
                PUSH DS
                MOV DX, # CRITINT       \ get new hndlr addr
                MOV AX, # $2524         \ set critical err int
                INT $21
                POP DS
                NEXT    END-CODE


comment:

\ ***************************************************************************
\ Using the following routines, we can save and restore the critical error
\ handler, but DOS does this when our program terminates anyway, so why
\ bother.
\ ***************************************************************************

create oldcritical  4 allot             \ room for seg:ofs of old routine
       oldcritical  4 erase             \ clear it out

code savecritical
                push ds                 \ save critical regs affected
                push es
                mov ax, # $3524         \ get current errhndlr at int $24
                int $21
                mov ax, es              \ get segment
                mov oldcritical ax      \ save seg
                mov oldcritical 2+ bx   \ save offset
                pop es                  \ es is safe now
                pop ds                  \ get old ds
                next
                c;

code resetcritical                      \ put back original critical hndler
                push ds                 \ save critical regs affected
                push es
                                        \ note: this is ORDER dependant.
                                        \ you have to do the offset first
                                        \ because you'll lose access to it
                                        \ as soon as ds changes!! tough bug!
                mov dx, oldcritical 2+  \ restore offset of old hndlr
                mov ax, oldcritical     \ restore segment
                mov ds, ax              \ of old handler
                mov ax, # $2524         \ set critical err int
                int $21
                pop es                  \ restore regs
                pop ds
                next
                c;

comment;

