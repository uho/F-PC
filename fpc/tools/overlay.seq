\\ OVERLAY.SEQ        Overlay mechanism for F-PC         by Tom Zimmer

  This file contains the code needed to allow defining overlays for F-PC.

  This tool allows the definition of specific entrypoints into an overlay,
and as such is best used for modules that have only a few entry points.
Other words in an overlay are not accessible after the overlay has been
defined. each overlay must be given an unique name of eight characters
or less, using STARTOV. The overlay is then saved to the specified name
with a .OVL file extension. Version verification is performed each time
an overlay is loaded, to assure an incompatible overlay is not accidently
loaded and used.

!! WARNING !!      !!! One overlay MAY NOT CALL ANOTHER OVERLAY !!!

***************************************************************************

  Used as follows:

        STARTOV myovfilename                    \ starts an overlay

                load the overlay contents       \ defines some new words

        ENDOV                                   \ ends overlay definition

                        ENTRYPOINT wordname1    \ makes somw words from
                        ENTRYPOINT wordname2    \ overlay available to world
                ' word3 ENTRYNAME  wordx        \ entry is a different name

        CLEAROV                                 \ clear the overlay from mem

        wordname1                       \ when used, causes overlay to be
                                        \ loaded, then wordname1 executed

  The defered word ?OVERROR can be changed by a user application to send
the user an error message in a particular way if needed. Any error
encountered while trying to read in an overlay file at runtime, will
terminate the overlay entrypoint word, and set the ?OVFLAG word FALSE.
This allows the application to clean up the stack parameters that would
have been used by the overlay entrypoint word if it had run.

                ***** Before Creating an Overlay *****

  This file must of course be loaded on F-PC 3.54 of higher, then the
system must be saved to a new .EXE file then restarted.  F-PC defaults the
system VALUEs #OVSEGS and #OVBYTES to zero when extending F-PC, so these
values must be set to something other than zero (F-PC must then be resaved)
for the overlay mechanism to work. When this file is loaded, they will be
given default values of 4k each by a conditional sequence at the end of
this file.

{

\       ************************************************************
\       Test for overlay mechanism already loaded, leave if it has.
\+ ovhndl       cr .( Overlay utility already loaded ) \S
\       ************************************************************

hidden also

handle ovhndl
0 value ?ov             \ is an overlay currently active?
0 value ?oving
0 value ovname          \ name pointer for overlay currently being defined
0 value xdpsave         \ a place to save some dictionary pointers
0 value xdpsegsave
0 value dpsave
0 value ?stacksave      \ a place to save the ?STACK function
0 value ?ovflag         \ a flag for an application to look at to check if
                        \ the most recent overlay word it executed worked
                        \ properly. Will contain FALSE if an error occured
                        \ while trying to read in the overlay file.

gettime 2constant ovstamp

defer ?overror          \ defered for easy application control

: %?overror     ( f1 a1 n1 -- f1 )      \ error test for overlay
                rot
                if      cr type true
                else    2drop   false
                then    ;

' %?overror is ?overror         \ install error handler

: startofov     ( -- a1 )       \ start of overlay in CODE space
                #ovbytes negate $10 - ;

: startofovseg  ( -- seg )      \ start of overlay in LIST space
                #ovsegs negate +xseg ;

: ?stackov      ( -- )          \ ?STACK while defining an overlay
                dp @ 80 negate u> if stackover then ;

: startov       ( | ovname -- ) \ start defining a new overlay
                #ovsegs 0= abort" No space available for an overlay"
                ?ov abort" An overlay is already active"
                startofov #ovbytes 0 fill
                startofovseg 0 #ovsegs 16 * 0 lfill
                off> ovname                     \ clear overlay filename
                bl word ovhndl $>handle         \ move name to overlay handle
                " OVL" ">$ ovhndl $>ext         \ change file extension
                ovhndl hcreate abort" Could not create overlay file"
                @> ?stack =: ?stacksave ['] ?stackov is ?stack
                xhere paragraph + =: xdpsave here =: dpsave
                startofovseg xdpseg ! xdp off
                startofov dp !
                ovstamp , ,
                headerless
                on> ?ov
                on> ?oving ;

: endov         ( -- )          \ finish the definition of a new overlay
                ?ov 0= abort" No overlay currently being defined"
                startofov #ovbytes ovhndl hwrite drop           \ write CODE
                0 #ovsegs 16 * ovhndl
                startofovseg exhwrite drop              \ write LIST
                headers
                xdpsave xdpseg ! xdp off dpsave dp !
                ?stacksave is ?stack                    \ restore stack check
                here =: ovname
                gettime nip ,
                ovhndl >pathend" here place
                here c@ 1+ allot
                ovhndl hclose drop
                off> ?oving ;

: clearov       ( -- )                  \ clear unneded symbols from memory
                ?ov 0= ?exit
                ovname 0= if endov then
                behead
                off> ?ov ;


: clearov_init  ( -- )
                defers initstuff
                clearov ;

' clearov_init is initstuff             \ install in initialization chain

: ?loadov       ( a1 -- a1 f1 )
                dup @ @ ?ov = dup ?exit drop    \ leave if ID is OK
                off> ?ovflag                    \ clear overlay error flag
                #ovsegs 0=
                " No room for overlay file"    ?overror if false exit then
                ovhndl clr-hcb                          \ pre-clear handle
                dup @ 2+
                me@ me$ dup >pathend over 1+ - 1 /string ovhndl place
                                                        \ place path in hndl
                ovhndl count + 1- c@ '\' <>             \ path ends in '\'
                if      " \" ovhndl +place              \ no, then add it
                then    count ovhndl +place             \ append filename
                ovhndl hopen
                " Couldn't open overlay file." ?overror if false exit then
                startofov #ovbytes ovhndl hread #ovbytes <>
                0 #ovsegs 16 * ovhndl
                startofovseg exhread #ovsegs 16 * <> or
                " Error in overlay read."      ?overror if false exit then
                startofov 2@ ovstamp d= 0=
                " Incompatible overlay"        ?overror if false exit then
                dup @ @ =: ?ov
                ovhndl hclose drop true
                on> ?ovflag ;

: entryname     ( a1 | name -- )        \ define name to execute a1 in overlay
                ?oving abort" Must do ENDOV first"
                false save!> warning
                create ovname , ,
                restore> warning
                does> ?loadov if 2+ perform else drop then ;

: entrypoint    ( | name -- )           \ define name as an overlay word
                >in @ ' swap >in !
                entryname ;

previous

\ **********************************************************************
\ ***** Give us some overlay room if we haven't already taken some *****
\ **********************************************************************

#ovsegs 0=                              \ if no overlay space, make some
#if     $00100 =: #ovsegs               \ 4k for LIST and
        $01000 =: #ovbytes              \ 4k for CODE
        cr
        cr .( You must do an "FSAVE <filename>" and then restart with )
        cr .( the new .EXE file before trying to create an overlay.)
        cr
#endif

\S ***********************************************************************

\               ******************************
\               A simple example of an overlay
\               ******************************


\ Start loading at about this line to define a sample overlay file.

        STARTOV MYOVTEST                        \ starts an overlay

                : OV1   ." First "   ;
                : OV2   ." Second "  ov1 ;
                : OV3   ." Third "   ov2 ;
                : OV4   ." Fourth "  ov3 ;
                : OV5   ." Fifth "   ov4 ;

        ENDOV                                   \ ends overlay definition

                ENTRYPOINT OV2                  \ makes some words from
                ENTRYPOINT OV5                  \ overlay available to world

        CLEAROV                                 \ clear the overlay from mem


