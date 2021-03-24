\ SVSESDAT.SEQ save/load mechanism for temporary data in a F-PC session - ms

ONLY FORTH ALSO HIDDEN DEFINITIONS

handle sesfile          \ file handle to store temporary session data

sesfile !hcb F-PC.SES   \ store default filename 'F-PC.SES' into handle
gettime 2constant sesstamp \ version verifikation stamp

variable sesdathead     \ resdat linked list head pointer

ALSO FORTH DEFINITIONS

\ ***************************************************************************
\ SESSIONDATL marks a FAR referenced data range as session data to be saved.
\ n1  =  size of data range in bytes, limited to 64k-1 bytes
\ a1  =  offset start address of data
\ cfa =  cfa of the word that returns the actual segment address of data
\        ( will be normaly cfa of a pointer )
\ ***************************************************************************
\ SESSIONDATL compiles the following nameless link structure into code space
\               +0      +2             +4      +6            +8
\               [ link ][ pointer-cfa ][ addr ][ size_bytes ][.....
\ ***************************************************************************
: sessiondatl   ( n1 a1 cfa --- )
                sesdathead begin dup @ ?dup while nip repeat \ eo link chain
                here 0 , swap !        \ append a new instance to link chain
                , , , ; \ compile cfa of a pointer instance, offset address
                        \ and size into code space

\ ***************************************************************************
\ SESSIONSEGMENT marks a POINTER as session data to be saved.
\ 'name' must be the name of an F-PC POINTER structure smaller than 64k bytes.
: sessionsegment ( -<name>- )    \ save the session segment of 'name'
                defined
                if      dup %sizeof@ drop 0 rot sessiondatl
                else    cr count type ."  <- segment was not defined"
                then    ;

\ ***************************************************************************
\ SESSIONDAT marks a data range in code space as session data to be saved.
\ n1  =  size of data range in bytes
\ a1  =  offset start address of data
\ ***************************************************************************
: sessiondat    ( n1 a1 --- )
                ['] ?cs: sessiondatl ;

HIDDEN DEFINITIONS

I: sesdat-init  ( -- )
                defers initstuff
                sesfile hopen ?exit
                pad 4 sesfile hread drop pad 2@ sesstamp D= not ?exit
                sesdathead
                begin @ ?dup
                while dup 2+ @tbl+ execute >r   \ segment address
                      @tbl+ swap @tbl+ nip sesfile r> exhread drop
                repeat
                sesfile hclose drop ;

' sesdat-init is initstuff

I: sesdat-byefunc ( -- )
                defers byefunc
                sesfile hcreate ?exit
                ['] sesstamp >body 4 sesfile hwrite drop
                sesdathead
                begin @ ?dup
                while dup 2+ @tbl+ execute >r   \ segment address
                      @tbl+ swap @tbl+ nip sesfile r> exhwrite drop
                repeat
                sesfile hclose drop ;

' sesdat-byefunc is byefunc

\ ***************************************************************************
\ some real examples that should show the usage of SESSIONDAT and SESSIONDATL
\ ***************************************************************************

16 COLORS       SESSIONDAT      \ save all the F-PC system colors;
2  &> NORMVAL   SESSIONDAT      \ 8 color attributes, the normal and the
2  &> REVVAL    SESSIONDAT      \ reverse attribute
2  attrib       SESSIONDAT      \ current color attribute

ALSO HIDDEN                     \ XBSEG is in HIDDEN vocabulary

SESSIONSEGMENT xbseg            \ save line editor session segment

ONLY FORTH ALSO DEFINITIONS

