\ PROPRINT.SEQ  Print strings for IBM PROPRINTER        by Tom Zimmer

editor definitions

defined teletype nip 0=         \ test to see if we are using print controls
#if
        forth definitions

\s      \ don't load any further, we are not using Print controls

#then

\       Printer control strings the the IBM PROPRINTER

create procompon$       1 c, 15 c,      \ SI
create procompoff$      1 c, 18 c,      \ DC2
create proboldon$       2 c, 27 c, 'E' c,
create proboldoff$      2 c, 27 c, 'F' c,
create proulon$         3 c, 27 c, '-' c, 1 c,
create prouloff$        3 c, 27 c, '-' c, 0 c,
create prowideon$       3 c, 27 c, 'W' c, 1 c,      \ turnon WIDE
create prowideoff$      3 c, 27 c, 'W' c, 0 c,      \ TurnOFF WIDE

headers

forth definitions

: IBM-PROPRINT  ( --- )
                teletype                \ any unchanged default to NOTHING
                on> ?ptrans             \ enable print code translation
                procompoff$ =: pr-reset$
                procompon$  =: compresson$
                procompoff$ =: compressoff$
                proboldon$  =: boldon$
                proboldoff$ =: boldoff$
                proulon$    =: ulon$
                prouloff$   =: uloff$
                prowideon$  =: 1on$             \ set user function 1 for WIDE
                prowideoff$ =: 1off$ ;          \ set user function 1

IBM-PROPRINT    \ Select the PROPRINTER as the default printer.

forth definitions

headerless

\s

        %B This line should be printed BOLD %B
        %U This line should be printed UNDERLINE %U
        %B%U This line should be printed with BOLD and UNDERLINE %U%B
        %1 Double Wide Printing %1 On an IBM Proprinter
        %This will print normally


