\ HELLO.SEQ     Contains the sign on word for Forth.

only forth also definitions hidden also

: <.HELLO>      ( --- )
                blinkoff
." \d\n\n\n\s34\r  F-PC  \n"
." \n\tÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÜ"
." \n\t³ 80x8x Forth ÄÄÄ Version:" >attrib3 .fpcver# >norm
                                                ." \0 Date:"
        decimal     cdate 2@ form-date  count type
        ."   Time:" ctime 2@ drop dtbuf off   build-hm
                                  dtbuf count type decimal    ."   Û"
." \n\tÃÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÛ"
." \n\t³ Based on F83 by           ÄÄ Henry Laxen, Michael Perry,  Û"
." \n\t³ ** F-PC FINAL RELEASE **  ÄÄ Tom Zimmer & Robert L. Smith Û"
." \n\tÀÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÛ\n"
." \n\n\s13" >ATTRIB1 .FREE >NORM
." \n\n\s18\1 Bewildered?   Press: \r F1 \1 for assistance "
." \n\n\t\t\1 Press \r ESC \1 to toggle the menubar on or off. "
        CR      ;

: .FILE         ( --- )
                .seqhandle ."  of " seqhandle ENDFILE D. ." bytes" ;

: <.CURFILE>    ( --- )
                CR TAB ." Current File = " seqhandle >HNDLE @ -1 <>
                IF      .FILE
                ELSE    ." \1 No file Open "
                THEN    CR ;

DEFER .HELLO    ' <.HELLO>      IS .HELLO
DEFER .CURFILE  ' <.CURFILE>    IS .CURFILE

: HELLO         ( --- )
                SP0 @  'TIB !
                >IN     OFF
                SPAN    OFF
                #TIB    OFF
                LOADING OFF
\u NOSETCUR     NOSETCUR OFF
                ONLY FORTH ALSO DEFINITIONS
                DEFAULTSTATE
                DEFAULT
                >in @ bl word swap >in ! c@ 0=
                if      .hello
                        .curfile
                then    interpret ;

' HELLO IS BOOT

: MARK          ( -- )
                CREATE YHERE ,
                XHERE PARAGRAPH +       \ absolute paragraph of new def
                DUP XDPSEG !            \ set new XHERE segment
                xseg @ - ,
                DOES> DUP 2+ dup @ +XSEG xdpseg ! xdp off
                2+ SWAP @
                (FRGET)  FORTH DEFINITIONS  ;

