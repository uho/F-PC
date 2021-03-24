\ SAVESYS.SEQ   SAVE-SYSTEM and                         by Tom Zimmer

comment:

  This file allows saving the Forth memory image as a .COM file. This
.COM file can include the Forth HEADs, or they may optionally be omitted.
The word SAVE-SYSTEM will save Forth with HEADs.

comment;

decimal

ONLY FORTH ALSO DEFINITIONS

: SAVE          ( Addr len {filename} --- )
                seqhandle+ !HCB
                seqhandle+ HDELETE   DROP
                seqhandle+ HCREATE   ABORT" Save Create ERR!"
                seqhandle+ HWRITE 0= ABORT" Save Write  ERR!"
                seqhandle+ HCLOSE    ABORT" Save Close  ERR!" ;

: XLENGTH       ( --- D1 )
                XHERE PARAGRAPH + XSEG @ - 16 *D ;

variable withheads      withheads on

: <SAVE-SYSTEM> ( {filename} -- )
                XHERE PARAGRAPH + XDPSEG !
                HERE 0 XLENGTH D+ YHERE 0 D+ 20. D+ SP@ 0 D>
                ABORT" Too big. Use SAVE-EXE"
                HERE DPSTART ! XSEG @ 0 ?CS: HERE XLENGTH DROP DUP ALLOT CMOVEL
                withheads @
                if      HERE YSTART  !
                        YSEG @ 0 ?CS: HERE YHERE        DUP ALLOT CMOVEL
                then
                XMOVED OFF
                XHERE PARAGRAPH + XSEG @ - XSEGLEN !
                256 HERE SAVE
                DPSTART @ DP !
                YSTART OFF
                XMOVED ON ;

: save-system   ( {filename} --- )
                withheads on
                <save-system> ;

: turnkey       ( {filename} --- )
                xhere drop xseg @ - 10 + =: #listsegs
                off> #headsegs
                withheads off
                <save-system> ;

