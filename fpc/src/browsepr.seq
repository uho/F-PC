\ TOBROWSE.SEQ          direct printed text to the browser

comment:

  This file allows re-direction of printed output to a file called
BROWSE.PRN, which is then subsequently passed to the borwser for your
perusal. You can say for example:

                >B WORDS DUP <enter>

Which will send a list of all words containing DUP to a file. The browser
is then started on the file so you can examine them. Or,

                >B REF HOPEN <enter>

Browse a list of all places where HOPEN is used. Or,

                >B FLOOK HDELETE SRC\*.SEQ <enter>

Browse a list of all the places in all files in the SRC directory where
HDELETE is used.

comment;

\       ******** LOAD FILE ONLY IF WE HAVE THE EDITOR ********

defined editor nip 0=
#if
        cr .( Can't load BROWSEPR.SEQ, no editor available)
        \s                              \ Discard the rest of the file
#then


only forth also editor also hidden definitions also

@> errfix value oldfix
2variable #prbytes
2variable #prsave

: outfix        ( --- )
                ['] qtypeL  is typeL
                ['] (emit)  is emit
                ['] (print) is pemit
                oldfix is errfix ;    \ remove from errfix

comment:
  ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
  ³ In the following definition, QTYPE is used "on purpose", to assure ³
  ³ the typing actually goes to the screen, and not to the redirected  ³
  ³ TYPE which is going to a printer file.                             ³
  ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ
comment;

10 array numsave_buf

: ?.prbytes     ( -- )
                #prbytes 2@ #prsave 2@ d>
                if      savecursor cursor-off
                        pad 9 - numsave_buf 10 cmove    \ save numb convert
                        save> hld                       \ preserve HLD
                        false      save!> printing
                        ibm-at?        2>r
                        #out @ #line @ 2>r
                                base @  >r decimal
                        52 9 at #prbytes 2@ (UD.) >attrib1
                        6 over - spcs swap 0MAX
                                ?CS: -ROT qtypeL
                                ?CS: -ROT qtypeL
                        spcs 2  ?CS: -ROT qtypeL >norm
                              r> base !
                             2r> #line ! #out !
                             2r> ibm-at
                        restore> printing
                        restore> hld                    \ restore HLD
                        numsave_buf pad 9 - 10 cmove    \ restore num convert
                        #prbytes 2@ 256. d+ #prsave 2!
                        restcursor
                then    ;

: prtypeL       ( SEG a1 n1 --- )
                dup 0 #prbytes 2@ d+ #prbytes 2!
                PRNTYPEL ?.prbytes ;

: premit        ( c1 --- )
                (print)
                #prbytes 2@ 1. d+ #prbytes 2!
                ?.prbytes ;

forth definitions

: >browser      ( | <command_line> --- )  \ browse results of command line
                " BROWSE.PRN" ">$ dup>r
                $pfile abort" Failed to create BROWSE.PRN"
                savescr savecursor
                20 8 61 10 box&fill
                ."  \1 Building BROWSE.PRN file...   "
                restcursor
                0.0 #prbytes 2!
                0.0 #prsave  2!
                @> errfix =: oldfix
                ['] pemit  save!> emit
                ['] prtypeL save!> typeL
                ['] outfix is errfix
                ['] premit save!> pemit
                printing on cr interpret cr cr printing off
                restscr pclose
                restore> pemit
                restore> typeL
                restore> emit
                oldfix =: errfix
                r> $file abort" Couldn't re-open BROWSE.PRN"
                on> newbrowse
                on> ?browse
                off> seding
                off> newfl
                off> edready
                <ed> ;

' >browser alias >b

only forth also definitions

