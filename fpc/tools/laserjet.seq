\ LASERJET.SEQ          Print strings for LASERJET      by Tom Zimmer

defined teletype nip 0=         \ test to see if we are using print controls
#if
        forth definitions
\s      \ don't load any further, we are not using Print controls

#then

\       Printer control strings the the LASERJET & DESKJET
\                       ON                                  OFF
\ 80 cpl          1B 26 6C 37 2E 32 37 43 1B 26 6B 30 53
\ 132 cpl         1B 2B 6C 37 2E 32 37 43 1B 26 6B 32 53
\ Underline       1B 26 64 44                           1B 26 64 40
\ Bold            1B 28 73 33 42
\ Normal          1B 28 73 30 42
\ Light           1B 28 73 2D 33 42
\ Italics         1B 28 73 31 53                        1B 28 73 30 53

create JETcompon$       ," &l7.27C&k2S"
create JETcompoff$      ," &l7.27C&k0S"
create JETboldon$       ," (s3B"
create JETboldoff$      ," (s0B"
create JETulon$         ," &dD"
create JETuloff$        ," &d@"
create JETitalicson$    ," (s1S"
create JETitalicsoff$   ," (s0S"

ALSO EDITOR ALSO

: LASERJET      ( --- )
                teletype                \ any unchanged default to NOTHING
\                53 =: prtlines                  \ Use fewer lines on LJ
                JETcompon$  =: compresson$
                JETcompoff$ =: compressoff$
                JETboldon$  =: boldon$
                JETboldoff$ =: boldoff$
                JETulon$    =: ulon$
                JETuloff$   =: uloff$
                JETitalicson$ =: 1on$           \ set user function 1 italics
                JETitalicsoff$ =: 1off$ ;       \ set user function 1

PREVIOUS PREVIOUS

LASERJET        \ Select the LASERJET as the default printer.

forth definitions
\s
        %B This line should be printed BOLD %B
        %U This line should be printed UNDERLINE %U
        %B%U This line should be printed with BOLD and UNDERLINE %U%B
        %1 ITALICS Printing %1 On an LASER JET printer
        %This will print normally

