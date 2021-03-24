\ CONSTANT.SEQ  Allow defining a bunch of constant or variables

comment:

  A simple utility to allow the definition of a bunch of variables or
constants.

comment;

anew cons

: constants     ( | <values> <names> --- )      \ define a bunch of constants
                loading @ 0= abort" CONSTANTS can ONLY be used while loading"
                begin   begin   bl word c@ 0=           \ while word len=0
                                                        \ discard comments
                                here " \" ">$ dup c@ 1+ caps-comp 0= or
                                outbuf c@ 2 > and       \ while read length<>0
                        while   filltib                 \ refill tib
                        repeat
                        here " ;" ">$ dup c@ 1+ caps-comp
                        0<>                             \ word <> ";" and
                        here c@ 0<> and                 \ word length <> 0
                while   here number drop constant
                repeat  ;

: variables     ( | <names> --- )               \ define a bunch of variables
                loading @ 0= abort" VARIABLES can ONLY be used while loading"
                begin   >in @ >r
                        begin   bl word c@ 0=           \ while word len=0
                                                        \ discard line after "\"
                                here " \" ">$ dup c@ 1+ caps-comp 0= or
                                outbuf c@ 2 > and       \ while read length<>0
                        while   r>drop 0 >r             \ save beginning of buf
                                filltib                 \ refill tib
                        repeat
                        here " ;" ">$ dup c@ 1+ caps-comp
                        0<>                             \ word <> ";" and
                        here c@ 0<> and                 \ word length <> 0
                while   r> >in ! VARIABLE
                repeat  r>drop ;

\s      An example of how to use CONSTANTS and VARIABLES

constants
        34 george                       \ allows these type of comments only"\"
        26 bob
        18 john  ;                      \ don't forget the terminating ";"

VARIABLES
        tom     dick    harry
        jerry   bobby   robert  ;      \ again, don't forget the ";"


