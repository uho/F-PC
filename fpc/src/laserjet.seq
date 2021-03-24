\ LASERJET.SEQ          Print strings for LASERJET      by Tom Zimmer

editor definitions

defined teletype nip 0=         \ test to see if we are using print controls
#if
        forth definitions
\s      \ don't load any further, we are not using Print controls

#then

\ Printer control strings the the LASERJET & DESKJET

create hpreset$    ," &k0S9"          \ " &k0S 9"
create hpinit$     ," (10U&k0S&l7.4c66F"  \ " &k4S &l7.4c66F &a10L"
create hp8cpi$     ," &k8S"            \ " &k8S"
create hp16cpi$    ," &k2S&a10L"      \ " &k2S"
create hp10cpi$    ," &k0S"            \ " &k0S"
create hp12cpi$    ," &k4S"            \ " &k4S"
create hpulon$     ," &dD"             \ " &dD"
create hpuloff$    ," &d@"             \ " &d@"
create hpiton$     ," (s1S"            \ " (s1S"
create hpitoff$    ," (s0S"            \ " (s0S"
create hpblon$     ," (s3B"            \ " (s3B"
create hpbloff$    ," (s0B"            \ " (s0B"

\ &k4S&l7.4c66F&a10L   re-init printer now

headers

forth definitions also editor also

: LASERJET      ( --- )
                teletype                \ any unchanged default to NOTHING
                on> ?ptrans             \ enable print code translaton
                hpinit$     =: pr-init$
                hpreset$    =: pr-reset$
                hp16cpi$    =: compresson$
                hp10cpi$    =: compressoff$
                hpblon$     =: boldon$
                hpbloff$    =: boldoff$
                hpulon$     =: ulon$
                hpuloff$    =: uloff$
                hpiton$     =: 1on$             \ set user function 1 italics
                hpitoff$    =: 1off$ ;          \ set user function 1

LASERJET        \ Select the LASERJET as the default printer.

previous previous

headerless

\s
        %B This line should be printed BOLD %B
        %U This line should be printed UNDERLINE %U
        %B%U This line should be printed with BOLD and UNDERLINE %U%B
        %1 ITALICS Printing %1 On an LASER JET printer
        %This will print normally

