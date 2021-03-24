\ OBJECT.SEQ  From Forth Dimmensions, Volume 10, number 2  by Rick Hoselton

comment:

        Some object oriented words slightly modified by Tom Zimmer
        for use in F-PC.


        OFFSET  #BYTES  METHOD format
        0       2       next older borther METHOD pointer
        2       2       MESSAGE number
        4       n       METHOD's code


        OFFSET  #BYTES  OBJECT format
        0       2       father OBJECT address
        2       2       youngest son OBJECT address + 4
        4       2       next older brother OBJECT address + 4
        6       2       youngest METHOD address
        8       n       optional local data

comment;

only forth also definitions

anew objectstuff

true  #if       \ Do we want to use the CODE version ov ACTION?

code action     ( obj msg --- )
                pop ax
                pop bx
                add bx, # 6
                begin
                        mov bx, 0 [bx]
                        cmp ax, 2 [bx]
             0= until
                add bx, # 4
                mov ax, bx
                jmp ax          end-code

#else

: action        ( obj msg --- )
                swap    6 +
                begin   @ 2dup 2+ @ =
                until   4 + nip execute ;

#then

variable 'msg
variable 'object

: act           ( pfa msg --- )
                2dup 'msg @ ! 'object ! action ;

: me            ( --- ?? )
                'object @ ;

: >object       ( rel-addr --- addr )
                me + ;

: >super        ( rel-addr --- addr )
                me @ + ;

: link,         ( addr --- )
                here over @ , swap ! ;

: object>       ( --- )
                'object link,
                0 ,
                2 >super   link,
                6 >super @  ,  ;

create master
                master 'object !
                object>
                2 >object 6 erase

: (method)      ( --- msg )
                create here does> act ;

: ?create       ( --- msg )
                >in @   defined
                if      nip >body
                else    drop >in ! (method)
                then    ;

: (method:)     ( --- )
                ?create
                6 >object  link, ,
                ,JUMP
                >NEST HERE - HERE 2- !  \ link into JUMP the addr of nest
                XHERE PARAGRAPH +
                DUP XDPSEG !
                XSEG @ - ,
                XDP OFF
                !csp ] ;

(method:) anchor ." I don't understand" ;

' anchor >body 2+ 'msg !

(method:) method:       ( --- )
                (method:) ;

master method: object:  ( --- )
                create  object> ;

: .method       ( link --- )
                cr dup 6 u.r  dup @ 6 u.r
                2+ @ dup 6 u.r  2 spaces body> >name .id ;

master method: .methods ( --- )
                base @ hex  6 >object
                begin   @ ?dup
                while   dup .method
                repeat  base ! ;

: .me           ( n --- )
                cr spaces me body> >name .id ;

master method: (.sons)  ( n --- )
                dup .me  2 >object
                begin   @ dup
                while   2dup 4 - (.sons)
                repeat  2drop ;

master method: .sons    ( --- )
                0  me (.sons) ;

master method: .one     ( --- )
                4 .me ;


cr .( Type 140 load & 157 load, to load the demonstration words.)
\s
\ Demonstration stuff starts here.

\               ********** Vehicle ************

master object: vehicle

vehicle method: #wheels  8 >super @ ;

vehicle object: boat    0 ,
vehicle object: car     4 ,
vehicle object: tricycle 3 ,

car object: green-monster
boat object: queen-mary

\ green-monster #wheels .
\ queen-mary    #wheels .

\s

\               ********** Automobile **********

master object: automobile

automobile method: object: ( n1 --- )
                create object>          \ build links
                0 ,                     \  8 = odometer milage
                0 ,                     \ 10 = odometer milage at last fillup
                0 ,                     \ 12 = gas in tank
                  , ;                   \ 14 = miles-per gallon

automobile method: drive  ( n1 --- )
                14 >object @ 12 >object @ * min \ gas * mpg = range
                dup  8 >object +!               \ increment odometer
                dup 14 >object @ / negate 12 >object +!
                ." I'm driving " . ."  miles " ;

automobile method: tell-gas ( --- )
                12 >object @ cr
                ." I have " . ." gallons in my tank " ;

automobile method: fill-gas ( n1 --- )
                12 >object +!  me tell-gas ;


 7 automobile object: racer             \ High performance, seven MPG.
23 automobile object: slow-poke         \ Low performance, twenty-three MPG.

                                        \ Some privacy for SLOW-POKE
                                        \ This shows that different objects
                                        \ can use the same MESSAGE name to
                                        \ produce different results.
slow-poke method: tell-gas cr ." It's a secret! " ; \ <ÄÄÄÄÄÄÄÄÄÄÄÄ¿
                        \                                          ³
 18 racer fill-gas      \                                          ³
100 racer drive         \                                          ³
                        \                                          ³
    racer tell-gas      \ This command doesn't seem to work        ³
                        \ after doing the above special stuff??? >ÄÙ

slow-poke tell-gas      \ This works just great though???

