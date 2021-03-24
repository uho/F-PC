\ SAVEEXE.SEQ   A SAVE EXE routine. Extracted from F83Y by Tom Zimmer

headerless

\ An empty .EXE header table.

CREATE  EHMT
( +0)   $5A4D ,         \ EHADR         .EXE File marker
( +2)   $00 ,           \ EHLMRV        File mod 512 including header
( +4)   $00 ,           \ EH512Z        Number of 512 byte blocks in file
( +6)   $00 ,           \ Number of relocation table items
( +8)   $02 ,           \ Size of header in segments
( +A)   $00 ,           \ Minimum segments needed by program
( +C)   $0FFFF ,        \ Additional segments needed by program, infinity
( +E)   $0FFF0 ,        \ SS            Stack segment, 100h below code strt
( +10)  $0FFFC ,        \ Offset for stack pointer.
( +12)  $00 ,           \ Word chekcsum, adds up to -1 ( DOS doesn't care! )
( +14)  SEXE ,          \ Offset to put in IP when passing control
( +16)  $0FFF0 ,        \ CS            Code segment, 100h below code strt
( +18)  $1C ,           \ Displacement in bytes to first relocation item
( +1A)  $00 ,           \ Overlay #, or zero (0) for resident code

( +1C)  $00 ,           \ Null relocation item, and fill to two (2) segments
( +1E)  $00 ,

comment:

XCKSUM  checksums a block of memory using word addition ( cnt must be even )

SUVEC  startup vector, for a long jump to HEX 100 to set up
   CS correctly.  Currently the .EXE header has CS set at
   0FFF0h which fakes out the loader to set CS to the same
   as the Program Sement Prefix.  This makes the long jump
   unnecessary, but we put it in so we could easily make
   the .EXE header more conventional.

SEXE  entry point specified by .EXE header.  Sets the seg part
   of SUVEC, moves FORTH headers up to seg after DS (YSEG),
   does long jump thru SUVEC to start system.

EHMT  empty .EXE header.  Entries 0Eh and 16h are SS and CS,
   set to -10h, somewhat questionable.  If they are changed,
   10h and 14h must be changed to compensate.

comment;

  $20        CONSTANT EHZ
 $100 EHZ -  CONSTANT EHADR
 EHADR   2+  CONSTANT EHLMRV
 EHADR   4 + CONSTANT EH512Z
 EHADR $12 + CONSTANT EHCKSM
 EHADR $10 + CONSTANT EHSP

comment:

        Constants for EXE header.  Header is put immediately before
        100h for write-out.  See DOS 2.0 appendix H for explanation.

        EHADR   header address
        EHZ     header size
        EHLMRV  load module remainder
        EH512Z  # 512 blocks in entire file
        EHCKSM  entire file checksum so file words total to FFFFh
        EHSP    startup SP

comment;

VARIABLE SAVEERR        \ Did an error occur while writting

HEX

: WRITE-EXE ( handle --- bool )         \ bool = TRUE on ERROR while writting
     >R                                 \ Save the file HANDLE
        XHERE PARAGRAPH + XDPSEG !      \ Round up to next even paragraph
        XDP OFF                         \ boundry, and reset XDP.
        HERE DPSAVED !                  \ Save DP for later restoral
        EHMT EHADR EHZ CMOVE            \ Move empty header to before 100H
        HERE 100 + EHSP !               \ Startup Stack Pointer to HERE+20H
        HERE 100 +
        05F + U2/ 8 / DPSTART !    \ Save start segment in DPSTART
        XDPSEG @ XSEG @ -               \ Calculate LIST length in segments
        DUP XDPSEGLEN !                 \ LIST dictionary segment offset
        DPSTART @ +                     \ add LIST start segment
        YSTART !                        \ Save start segment in YSTART
        WITHHEADS @
IF      YDP @ 1F + U2/ 8 /              \ HEAD length in segments
ELSE    0
THEN    YSTART @ +                      \ = total length in segments
        EHADR U2/ 8 / -                 \ Subtract Header segments
        DUP 20 MOD                      \ Remainder of 512 byte pages
        10 * EHLMRV !                   \ save BYTE remainder in EHLMRV
        1F + U2/ 10 / EH512Z !          \ Set # of full pages into EH512Z

        0 EHCKSM !              \ dummy fill checksum value with a NULL

     R> YDP @ >R >R
        WITHHEADS @ 0=
IF      YSTART OFF
        YDP OFF
THEN

        EHADR                           \ Start address
        HERE 100 +
        05F + 0FFF0 AND            \ end address is total length
        EHADR -                         \ subtract space below header
        dup negate SAVEERR !
     R@ HWRITE                          \ Write CODE space
        saveerr +!

     R@ XDPSEG @ XSEG @ - 100 /MOD      \ Calc # of 4k byte sectors
        SWAP >R dup>r 0                \ 100 hex * 16 decimal = 4096 decimal
        ?DO     DUP 0 1000 ROT I 100 * +XSEG
                EXHWRITE 1000 - saveerr +!
        LOOP    R> 0 R> 10 * dup negate saveerr +!
                2SWAP
                100 * +XSEG
                EXHWRITE saveerr +!

        WITHHEADS @
IF      0                               \ From segment offseg 0
        YDP @ 1F + 0FFF0 AND            \ HEAD length in bytes
        dup negate saveerr +!
     R> YSEG @ EXHWRITE                 \ Write HEAD space
        saveerr +!
ELSE r>drop
THEN R> YDP !
        YSTART OFF                      \ Reset YSTART
        SAVEERR @ ;

DECIMAL

headers

handle exehcb

: <save-exe>    ( | name --- )
                DECIMAL                 \ Save system in DECIMAL number base
                more? 0=
                if      cr ." File to save? " query
                then
                exehcb !hcb " EXE" ">$
                exehcb $>ext
                exehcb hcreate abort" Could not create file"
                0 save!> loading
                exehcb write-exe        ( -- f1 )
                exehcb hclose or        ( -- f1 )
                abort" Write ERROR, Disk probably FULL!"
                restore> loading ;

: save-exe      ( | name --- )
\+ behead       behead          \ remove beheadable words before save
                withheads on
                <save-exe> ;

' save-exe alias FSAVE ( | name --- )    \ a pseudonym for SAVE-EXE

: turnkey       ( | name --- )
                [ hidden ]
\+ %unvalidate  %unvalidate     \ don't validate turnkeyed applications
                [ forth ]
                #ovbytes 0=     \ only re-adjust CODE if no overlays
                if      here paragraph
                        4096 paragraph + #codesegs min =: #codesegs
                then
                xhere drop xseg @ - 100 + =: #listsegs
                off> #headsegs
                withheads off
                <save-exe>
                bye ;

only forth definitions also

comment:

WEXE    ( HANDLE ) write .EXE file given HANDLE of opened file.
        Copies header from EHMT to below 100h, fills out EHSP,
        EHMLRV, EH512Z, sets DTA, computes YSEG (headers) checksum,
        plus checksum from 0E0h to YSTART, puts NOT in EHCKSM,
        writes 0E0h to YSTART, then sets DTA for YSEG, writes
        out FORTH lists, and headers.  "lobz" is the size of the first
        write, 0E0h to YSTART.  Image is written with YSTART containing
        offset where the header segment data begins.  YSTART non-
        zero indicates the segment hasn't been moved to its correct
        location for running.

SAVE-EXE  like SAVE-SYSTEM, but makes a .EXE file.

comment;

