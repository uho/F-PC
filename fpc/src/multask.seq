\\ MULTASK.SEQ   Multi tasking code for Forth.  jm edit text  91-06-12 15:56
    91-06-12, -21 add more refs.   91-07-29,  91-08-09, -12   more edit.
    All running code is identical to Zimmer's MULTASK.SEQ of 91-02-19 20:36.
    jmod  91-12-20 12:51 edit; add TZ's new word MAKEUSER.


   The Multitasker is loaded as an application on top of the regular Forth
System.  There is support for it in the nucleus in the form of a USER
VOCABULARY at the beginning of KERNEL4.SEQ, and USER VARIABLES and USER
DEFERRED WORDS at the beginning of KERNEL2.SEQ, initialized in the middle of
KERNEL4.SEQ.
   PAUSEs are inserted inside of the Kernel IO words (KEY) and MOUSEKEY; and
the timing words MS, TENTHS, and SECONDS. OUTPAUSEs are inserted in the
words PRINTL, CONSOLEL, CONTYPEL, and PRNTYPEL.


MULTITASKING THRU "PAUSE"

   The Forth multitasking scheme is co-operative instead of interruptive. The
word PAUSE is the key to the operation of the multitasker. When a PAUSE
occurs, the multitasking loop looks around at all of the current tasks for
something to do. On return the program continues where it left off.
   The presence and location of the PAUSES is critical to the proper
operation of the of multitasking. Here are some rules.
   1) If a task has no PAUSEs in itself in or a referenced word, the task may
lock up the system.
   2) If a PAUSE interrupts an operation (for instance, a transmission to a
serial device), it may cause an error.
   3) If a PAUSE is the last word in a definition before the semicolon (see,
for instance, the word STOP, below), the word will run properly once, but if
you attempt to run it again the sytem will crash (either hang or return to
DOS), if it has nowhere to resume operation. So if you need to use STOP in a
word that will be executed more than once, place it inside of BEGIN ...
AGAIN loop, so it has somewhere to go.

   In the Kernel words, the pause is at the beginning of endless loops (that
is: begin pause ... again) such as KEY, that are in the main terminal
program (which is QUIT); and timing words (begin pause ... until) such as
SECONDS; and the beginning of words for functions which may be slow, such as
the output words, for example PRINTL which sends a character to the printer
(outpause ...). OUTPAUSE is initially set to NOOP, because there are some
cases where interruption of the transmission of a character stream will
cause mischief. You may need to set ' PAUSE IS OUTPAUSE  .

   It is possible that a word you use in multitasking needs to be a USER
VARIABLE, but is not. For instance, if you use the a time-delay word such as
HOURS, MINUTES, SECONDS, or TENTHS, in more than one task, you will find that
the fastest-executing task locks out the others. This is because each task
is using the same variable STIME. In order for each task to time
independently, STIME needs to be a USER VARIABLE.

   Tom Z. has kindly devised the following word to convert an existing
variable (such as STIME) into a user variable.


{
: MAKEUSER   ( -<name>- )
   ['] base @rel>abs
   ' dup>r  >body
   - r@  1+  !
   #user  @  r>  >body !
   2 #user  +! ;
}
EXAMPLE: Convert STIME to a user variable.

        makeuser stime
        user 2 allot    \ space for two more bytes,
                        \ since STIME is a double number

REFERENCES:    (1) Henry Laxen, "Multi-Tasking Forth", Forth Dimensions,
               part 1: V5.4, pp 26...28; part 2: V5.4, pp 37...38.
               (1983-11/12, 1984-1/2).

               (2) C.H. Ting, "Inside F83", (3d ed 1991) explains the use of
               the Multitasker in Sec. 3.7, and the operation of the F83
               Multitasker in detail in Chapter 23. The F-PC Multitasker
               is identical but for a few fixes made by Tom Zimmer in order
               to make it work in F-PC.

               (3) C.H. Ting, "F-PC Users Manual" (3rd ed),has nothing on
               Multitasking.

               (4) C.H. Ting, "F-PC Technical Manual" (3rd ed) has no indexed
               items on Multitasking, but there are relevant discussions in
               Sec. 3.1, User Variables and User Deferred Words (pp 57, 58);
               Sec. 5.1, User Vocabulary (pp 122,123); and Sec. 5.5,
               Initialize the User Area (p 131).

               (5) C.H. Ting, MULTI.SEQ   Examples of Using F83 Multitasker
               (programs to play Bach Cannons, using the Multitasker). A
               file in the set from C.H. Ting, distributed with (some
               versions?) of F-PC.

  Modified by Tom Zimmer to work with F-PC, which needs to save the ES
register along with IP. - 2/29/88 -

NOTICE !!       This multi tasking DOES NOT SUPPORT MULTIPLE USERS !!!

  There are a significant number of things what will have to be changed in
this Forth system to support multi users, like the editor would have to be
re-written, etc.  So I am not supporting multi user, only background
processing.

  Background processing MAY NOT do any compiling, like VARIABLE, or CREATE,
etc. at *** RUNTIME ***.                     06/06/88 13:58

  YDP and XDP are NOT user variables.

  TJZ 02/19/91 20:29 - Fixed a couple of bugs in multi-tasking that
prevented FSAVEd systems from running because multi-tasking was not
relocatable. It is now!. Of course there was a small performance penalty.

{
PREFIX
ONLY FORTH ALSO DEFINITIONS
DECIMAL
}

PAUSE is a NOP that can be patched, defined in Kernel1.
UP   is SYSTEM VARIABLE pointing to the current USER area for multitasking.

{
CODE (PAUSE)    ( -- )
                mov ax, es
                sub ax, xseg
                push ax
                PUSH IP
                PUSH RP
                MOV BX, UP      \ make BX point to user area
                MOV 0 [BX], SP  \ save SP in user area offset 0
                ADD BX, # 4     \ adjust BX to point to LINK
                ADD BX, 0 [BX]  \ Add value in LINK to BX, pointing it to
                                \ next tasks user area offset 0
                ADD BX, # 2     \ bump BX 2 to point to ENTRY
                JMP BX          \ jump to next tasks entry point
                END-CODE

CODE RESTART    ( -- )
                POP BX          \ pop address of where we came from
                MOV AX, # -4
                ADD BX, AX      \ adjust to beginning of user area
                MOV UP BX       \ set UP to point to begin of user area
                POP AX          \ pop off old PC
                POPF            \ restore status register
                MOV SP, 0 [BX]  \ restore SP (stack pointer)
                POP RP          \ restore RP, IP, and ES
                POP IP
                pop ax
                add ax, xseg
                mov es, ax
                NEXT            END-CODE

$0E9 CONSTANT INT#
}
Two user variables for the Multitasker are defined in Kernel2:
ENTRY is the entry point; it contains machine code.
LINK  is the link to the next task.
{

: LOCAL         ( base addr -- addr' )   UP @ -   +   ;
: @LINK         ( -- addr ) LINK DUP @ +   2+   ;
: !LINK         ( addr -- ) LINK 2+ -   LINK !   ;
}

Stop the execution of <taskname>.
ENTRY is a USER VARIABLE containing machine code that executes a task if it
is awake, or skips to the next task if it is asleep.
{
: SLEEP         ( <taskname> -- ) $0E990 SWAP ENTRY LOCAL !   ;
}

Start the execution of <taskname>.
{
: WAKE          ( <taskname> -- ) $0E9CD SWAP ENTRY LOCAL !   ;
}

Put the running task to sleep after it is completed (optional).
If STOP is the last word in a definition before the semicolon, that word can
only be executed once. A second execution will hang or crash the system. In
that case, put the STOP inside a loop, such as
      begin ... <words to do task once> stop again

{
: STOP          ( -- )      UP @ SLEEP   PAUSE   ;
}

Disable the multi-tasker.
{
: SINGLE        ( -- )
                $0E9  ['] PAUSE C!              \ set JMP in PAUSE
                >NEXT ['] PAUSE >BODY -         \ get offset to NEXT
                      ['] PAUSE 1+ ! ;          \ store in PAUSE + 1
}
Enable the multi-tasker
{
CODE MULTI      ( -- )
                MOV ' PAUSE # $0E9
                MOV BX, # ' (PAUSE) ' PAUSE 3 + -       \ relative I hope!
                MOV ' PAUSE 1+ BX
                MOV BX, # ' RESTART
                MOV AX, DS
                PUSH AX
                SUB AX, AX      MOV DS, AX
                MOV AX, CS      MOV INT# 4 * 2+ AX
                                MOV INT# 4 *    BX
                POP AX
                MOV DS, AX      NEXT
                END-CODE

UP @ WAKE   ENTRY !LINK
}

Test to see if the Multitasker is enabled.
{
: ?MULTI        ( -- f1 )       \ f1 = true if MULTI tasking
                ['] (PAUSE) ['] PAUSE @REL>ABS = ;

}
Name, initialize, and allocate a new task.
{
: TASK:         ( size -<name>- )
                CREATE   TOS HERE #USER @ CMOVE    \ Copy the USER Area
                @LINK  UP @ -ROT  HERE UP !  !LINK \ I point where he did
                DUP HERE +   DUP RP0 !   100 - SP0 !  SWAP UP !
                HERE ENTRY LOCAL !LINK             \ He points to me
                HERE #USER @ +  HERE DP LOCAL !
                HERE SLEEP   ALLOT   ;

}
Assign an existing task to the code at ip.
{
: SET-TASK      ( ES ip task -- )       \ NOTE: both ES and IP are passed
                                        \ to SET-TASK.
                >R SWAP R>
                DUP SP0 LOCAL @                 \ Top of Stack
                2- ROT xseg @ - OVER !          \ Initial ES
                2- ROT OVER !                   \ Initial IP
                2- OVER RP0 LOCAL @ OVER !      \ Initial RP
                SWAP TOS LOCAL !  ;

}
Assign an existing <taskaddr> to perform the following ordinary word <name>.
Where <name> and activate are used ONLY in a ":" definition, as in:

        : xyz   ( -- )  thetask ACTIVATE thefunction stop ;

Here, the background task "thetask" is assigne a new function "thefunction"
to perform, and it is activated.
{

: ACTIVATE      ( <taskaddr> -<name>- )
                DUP
                2R> ROT SET-TASK   WAKE  ;

}
Name and define a new background task. When <taskname> is later executed, it
leaves on the stack the address of its user area, for use by WAKE or SLEEP.
{
: BACKGROUND:   ( -<taskname>- )
                400 TASK:               \ assign 400 bytes to the task for
                                        \ stacks
                XHERE @LINK 2-          \ get address of new task, note that
                                        \ XHERE returns SEGMENT and OFFSET,
                                        \ which is used by SET-TASK
                SET-TASK  !CSP  ]  ;    \ set the task, and continue
                                        \ compiling upto ';'

}

   Here is a sample of how to create a background task that will do a listing
of the current file, with the hypothetical word FUNCTION1.  Create the task
MYTASK, and assign the default function FUNCTION1 to it. Put FUNCTION1 to
sleep after it finishes.

 background: MYTASK           \ Create the task MYTASK
                 FUNCTION1    \ Assign the already-defined FUNCTION1 to it
                 stop ;       \ Put FUNCTION1 to sleep after it finishes

   Next we define a word MYTASK-THIS, that changes the function assigned to
MYTASK to perform FUNCTION2. This allows us to change the function a task
performs without having to define a new task. Again, the task is stopped
after its assigned FUNCTION is performed.

        : mytask-this   mytask activate   function2 stop  ;


   Each background task should contain a PAUSE, either explicitly stated, or
by referencing a word that already contains a PAUSE.

  This next example defines a variable, and a routine which increments the
variable in the background.  Notice that the program is an infinite loop,
and will only stop when put to sleep, or when multi tasking is turned off
with SINGLE.  This example will actually work: try typing it into a file and
loading it.

      This example will actually work: copy it into another file and load it.

cls                                            \ clear the screen
0 value counts
background: counter
                begin
                  pause                        \ allow other tasks to run once
                  incr> counts                 \ increment counter
                  counts 0 1000 um/mod drop 0=
                    if  at?                    \ get current cursor position
                        38 1 at counts u.      \ display current count
                        at                     \ restore cursor position
                    then  \ display count every thousand
                again   ;

COUNTER WAKE                    \ start up the COUNTER task
MULTI

\ counter sleep         will stop the counter
\ counter wake          will start it again
\ single                will disable the multitasker

{

ONLY FORTH ALSO DEFINITIONS

}
 OUTPAUSE note by Tom Zimmer 07/28/91 17:05:19.90

  A sharp eye will note the word OUTPAUSE used in the EMIT and TYPE words.
Output was switched from the normal PAUSE word to OUTPAUSE because you
may want a multiple background processes to output to the same console,
intermingling their messages on the same screen, perhaps using AT to
position the cursor before each message is displayed. If PAUSEs are allowed
during output, then a task switch will likely occur in the middle of a
message being displayed by a background task. If this occurs then output
from multiple tasks will get mingled on a character by character basis,
with will likely be VERY confusing for anyone trying to read the screen.
Thus it was decided to prevent pauses from occuring during output to the
screen.  OUTPAUSE is provided in case you want to reinstate pauses for
some reason.

 ABORT note by Tom Zimmer 07/28/91 17:05:38.57

Typically small amounts of memory are assigned to each background task.
These memory amounts are in fact so small, that should any background
task encounter an error that causes it to vector through ABORT, then
F-PC will immediately detect an out of memory error since the space
between HERE and SP@ is very small in a typical task.  It is therefore
imperative that a background task NOT be allowed to call ABORT.

 Other comment additions by: J McKnight, MRL  work: 1415-965-8187

{


