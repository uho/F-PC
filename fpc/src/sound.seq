\ SOUND.SEQ             Some Sound Experiments

comment:

  A simple sound generator for F-PC, programs the timer chip for the
proper frequency, then enables the tone to the speaker for "duration"
number of tenths of a second.

comment;

\ port addresses pulled from:
\        "The Peter Norton Programmer's Guide to the IBM-PC"

66 constant timerdata           \ timer data port
67 constant timerctrl           \ timer control port
97 constant tonegate            \ tone gate

                                                \ freq     = frequency in Hz
: note          ( freq duration-ms -- )         \ duration = milli-seconds
                >r                              \ save duration
                182 timerctrl pc!               \ prepare for new frequency
                1193280. rot um/mod nip                 \ calculate timer vals
                256 /mod swap                           \ for desired freq.
                timerdata pc! timerdata pc!             \ set counter values
                tonegate pc@ $03 or  tonegate pc!       \ tone on
                r> ms                                   \ for duration
                tonegate pc@ $FC and tonegate pc! ;     \ tone off

                                                \ freq     = frequency in Hz
: tone          ( freq duration-tenths -- )     \ duration = tenths of sec
                50 umin 100 * note ;

: ring          ( -- )          \
                5 0 do  575 50 note 750 50 note loop ;

  1 value beepduration          \ time in tenths of a second for BEEP
500 value beepfreq              \ frequency of BEEP in Hz.

: newbeep       ( -- )
                beepfreq beepduration tone ;

' newbeep is beep               \ install NEWBEEP into BEEP defered word


