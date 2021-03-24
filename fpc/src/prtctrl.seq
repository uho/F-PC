\ PRTCTRL.SEQ   Print control file                      by Tom Zimmer

\ Allows the editor to do BOLD, UNDERLINE, ect.

editor definitions

headers

0 value bldvar                  \ Bold state variable
0 value ulvar                   \ Underline state variable
variable compressvar            \ Compressed print variable
0 value 1onvar                  \ first of three user definable printer strings
0 value 2onvar
0 value 3onvar
0 value ?ptrans                 \ embeded printer code translation enabled?

variable null$  null$ off

null$ value pr-init$
null$ value pr-reset$
null$ value compresson$
null$ value compressoff$
null$ value boldon$
null$ value boldoff$
null$ value ulon$
null$ value uloff$
null$ value 1on$
null$ value 1off$
null$ value 2on$
null$ value 2off$
null$ value 3on$
null$ value 3off$

forth definitions

: teletype      ( --- )
                off> ?ptrans            \ disable print code translation
                null$ =: pr-init$       null$ =: pr-reset$
                null$ =: compresson$    null$ =: compressoff$
                null$ =: boldon$        null$ =: boldoff$
                null$ =: ulon$          null$ =: uloff$
                null$ =: 1on$           null$ =: 1off$
                null$ =: 2on$           null$ =: 2off$
                null$ =: 3on$           null$ =: 3off$ ;

headerless

editor definitions

: p$write       ( a1 --- )
                count prnhndl hwrite drop ;

: ?compressed   ( --- )
                compressvar @
                if      compresson$
                else    compressoff$
                then    p$write ;

: printer-init  ( -- )
                pr-init$ p$write
                ?compressed ;

: printer-reset ( -- )
                pr-reset$ p$write ;

: boldon        ( --- ) boldon$  p$write  on> bldvar ;
: boldoff       ( --- ) boldoff$ p$write off> bldvar ;
: ulon          ( --- ) ulon$    p$write  on> ulvar  ;
: uloff         ( --- ) uloff$   p$write off> ulvar  ;
: 1on           ( --- ) 1on$     p$write  on> 1onvar ;
: 1off          ( --- ) 1off$    p$write off> 1onvar ;
: 2on           ( --- ) 2on$     p$write  on> 2onvar ;
: 2off          ( --- ) 2off$    p$write off> 2onvar ;
: 3on           ( --- ) 3on$     p$write  on> 3onvar ;
: 3off          ( --- ) 3off$    p$write off> 3onvar ;

: lineendoff    ( --- )
                bldvar if boldoff then
                ulvar  if uloff   then
                1onvar if 1off    then
                2onvar if 2off    then
                3onvar if 3off    then ;

: prtfunc       ( c1 --- )      \ builds printer attribute control functions
                create c,
                does>   dup>r c@ over =         \ if escape code matches
                if      drop false
                        r@ 1+ perform abs 1+ 2* r@ + 1+ perform
                then    r>drop ;

'1' prtfunc ?1on       ' 1onvar , ' 1on  ,    ' 1off ,
'2' prtfunc ?2on       ' 2onvar , ' 2on  ,    ' 2off ,
'3' prtfunc ?3on       ' 3onvar , ' 3on  ,    ' 3off ,
'b' prtfunc ?bold      ' bldvar , ' boldon  , ' boldoff ,
'u' prtfunc ?underline ' ulvar ,  ' ulon ,    ' uloff ,

: ptype         ( a1 n1 --- )           \ type a string to the printer
                bounds
                ?do     i c@ '%' =              \ if char is "%",
                        ?ptrans and             \ and if escape code
                                                \ translation is enabled
                        if      i 1+ c@ bl or
                                ?underline ?bold ?1on ?2on ?3on
                                if      i c@ pemit
                                        1       \ skip to next character
                                else    2       \ skip an extra character
                                then
                        else i c@ pemit 1       \ skip to next character
                        then
               +loop    ;

forth definitions


