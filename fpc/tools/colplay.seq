\ COLPLAY.SEQ  Testsource fr eine Farbauswahlbox  ms  12/18/92 10:16:49.66
comment:
          COLPLAY.SEQ enth„lt eine einfaches Farbwahltool fr die visuelle
        Farbselektion der F-PC Systemfarben. Das Wort SYSCOLORS zeigt die
        acht Farben der COLORS Tabelle und die Attribute NORMVAL und REVVAL
        an. Es kann jeweils eine Farbe mit dem Cursorbalken zum Ver„ndern
        selektiert werden. Die gltigen Tastenbelegungen werden in der
        Auswahlbox angezeigt. Der Code kann als Overlay-Code compiliert
        werden, um den Code-, List- und Headerspeicher dieser Worte fr
        wichtigere Sachen aufzusparen. Auáer SYSCOLORS sind noch die Worte
        ORIG-PAL, MY-PAL und MY-LCDPAL definiert, deren Bedeutung Sie leicht
        erraten werden.

          Es gibt eine ge„nderte Version der originalen Datei COLOR.SEQ
        mit dem Namen %COLOR.SEQ. Sie enth„lt einige kleine nderungen des
        Speicherformats in die Tabelle COLORS, die fr das Farbwahltool
        n”tig sind. Wenn das System den originalen Code enth„lt, patcht
        COLPLAY diesen Code entsprechend. Wenn man COLPLAY aber fest in
        sein System integrieren m”chte, sollte man FPC recompilieren und
        dabei COLOR durch %COLOR ersetzen.

comment;

ONLY FORTH ALSO HIDDEN ALSO FORTH DEFINITIONS

\- \GR FALSE DIRECTIVE \GR
\- \EN TRUE  DIRECTIVE \EN

DEFINED  hn!  NIP  0=   \ do we need to patch the original code ?
#IF

\- PATCH  VOCABULARY  PATCH
                \ make the vocabulary PATCH if we don't already have one
                \ PATCH is a good idea from akg
HIDDEN DEFINITIONS

: colortbl      ( n1 --- a1 )   \ Adresse des n1-ten Eintrages in Colors
        1- 0MAX 2* COLORS + ;

: hn!   ( n1 a1 -- )    \ store n1 in High-Nibble of Byte at a1
        dup @ $0f and rot 16 * $f0 and or swap c! ;

: hn@   ( a1 -- n1 )    \ fetch High-Nibble of Byte at a1 as n1
        @ $f0 and 16 / ;

: ln!   ( n1 a1 -- )    \ store n1 in Low-Nibble of Byte at a1
        dup @ $f0 and rot $0f and or swap c! ;

: ln@   ( a1 -- n1 )    \ fetch Low-Nibble of Byte at a1 as n1
        @ $0f and ;


BLUE    1 colortbl hn!   GREEN   1 colortbl ln! \ set the default attribute
RED     2 colortbl hn!   WHITE   2 colortbl ln! \ forground and background
GREEN   3 colortbl hn!   WHITE   3 colortbl ln! \ colours .
RED     4 colortbl hn!   WHITE   4 colortbl ln!
BLUE    5 colortbl hn!   GREEN   5 colortbl ln!
RED     6 colortbl hn!   WHITE   6 colortbl ln!
GREEN   7 colortbl hn!   WHITE   7 colortbl ln!
RED     8 colortbl hn!   WHITE   8 colortbl ln!


PATCH  DEFINITIONS

: >1BGFG        ( --- ) 1 colortbl c@ attrib c! ;
: >2BGFG        ( --- ) 2 colortbl c@ attrib c! ;
: >3BGFG        ( --- ) 3 colortbl c@ attrib c! ;
: >4BGFG        ( --- ) 4 colortbl c@ attrib c! ;
: >5BGFG        ( --- ) 5 colortbl c@ attrib c! ;
: >6BGFG        ( --- ) 6 colortbl c@ attrib c! ;
: >7BGFG        ( --- ) 7 colortbl c@ attrib c! ;
: >8BGFG        ( --- ) 8 colortbl c@ attrib c! ;

PATCH  @>  >1BGFG       FORTH  !>  >1BGFG
PATCH  @>  >2BGFG       FORTH  !>  >2BGFG
PATCH  @>  >3BGFG       FORTH  !>  >3BGFG
PATCH  @>  >4BGFG       FORTH  !>  >4BGFG
PATCH  @>  >5BGFG       FORTH  !>  >5BGFG
PATCH  @>  >6BGFG       FORTH  !>  >6BGFG
PATCH  @>  >7BGFG       FORTH  !>  >7BGFG
PATCH  @>  >8BGFG       FORTH  !>  >8BGFG

\GR     CR  TAB .( Der Code von '>1BGFG' bis '>8BGFG' wurde "gepatcht", )
\GR     CR  TAB .( d.h. nachtr„glich ver„ndert !)

\EN     CR  TAB .( the code of the words '>1BGFG' to '>8BGFG' has been patched! )

#THEN   \ end of patch section

HIDDEN DEFINITIONS

: normal        ( --- a1 )      \ Adresse von NORMVAL
        ['] NORMVAL >BODY ;
: reverse       ( --- a1 )      \ Adresse von REVVAL
        ['] REVVAL >BODY ;

8  value corgx
3  value corgy

0 value coloradr

defer showbox

: <showbox>     ( --- )         \ Auswahlbox ausgeben
\GR     corgx corgy 2dup 56 12 d+ box&fill
\GR     >attrib2 corgx corgy 2 2 d+ at."  Michel's Farbauswahltool "
\GR     >attrib1 at." " at." Auswahl: " 2drop
\GR     coloradr case
\GR             normal   of ." Normal-Farbe" endof
\GR             reverse  of ." reverse Normalfarbe" endof
\GR             colors - 2/ 1+ ." Tabellenfarbe:" .
\GR             endcase >norm
\GR     corgx corgy 30 1 d+
\GR     at."       ÚÄÄÄÄÄ¿"
\GR     at."       ³    ³"
\GR     at." ÚÄÄÄÄÄÅÄÄÄÄÄÅÄÄÄÄÄ¿"
\GR     at." ³    ³    ³    ³"
\GR     at." ÀÄÄÄÄÄÁÄÄÄÄÄÁÄÄÄÄÄÙ"
\GR     at."  = + Vordergrundfarbe"
\GR     at."  = - Vordergrundfarbe"
\GR     at."  = + Hintergrundfarbe"
\GR     at."  = - Hintergrundfarbe" 2drop ;

\EN     corgx corgy 2dup 56 12 d+ box&fill
\EN     >attrib2 corgx corgy 2 2 d+ at."  Michel's color select tool "
\EN     >attrib1 at." " at." selection: " 2drop
\EN     coloradr case
\EN             normal   of ." normal color" endof
\EN             reverse  of ." reverse normal color" endof
\EN             colors - 2/ 1+ ." table color:" .
\EN             endcase >norm
\EN     corgx corgy 30 1 d+
\EN     at."       ÚÄÄÄÄÄ¿"
\EN     at."       ³    ³"
\EN     at." ÚÄÄÄÄÄÅÄÄÄÄÄÅÄÄÄÄÄ¿"
\EN     at." ³    ³    ³    ³"
\EN     at." ÀÄÄÄÄÄÁÄÄÄÄÄÁÄÄÄÄÄÙ"
\EN     at."  = + forground color"
\EN     at."  = - forground color"
\EN     at."  = + background color"
\EN     at."  = - background color" 2drop ;

' <showbox> is showbox

defer showcolor

: %.fg          ( n1 --- )      \ n1=Farbwert
        case
\GR             $00 of ." SCHWARZ"              endof
\GR             $01 of ." BLAU"                 endof
\GR             $02 of ." GRšN"                 endof
\GR             $03 of ." ZYAN"                 endof
\GR             $04 of ." ROT"                  endof
\GR             $05 of ." MAGENTA"              endof
\GR             $06 of ." BRAUN"                endof
\GR             $07 of ." HELLGRAU"             endof
\GR             $08 of ." DUNKELGRAU"           endof
\GR             $09 of ." HELLBLAU"             endof
\GR             $0A of ." HELLGRšN"             endof
\GR             $0B of ." HELLES ZYAN"          endof
\GR             $0C of ." HELLROT"              endof
\GR             $0D of ." HELLMAGENTA"          endof
\GR             $0E of ." GELB"                 endof
\GR             $0F of ." WEISS"                endof

\EN             $00 of ." black"                endof
\EN             $01 of ." blue"                 endof
\EN             $02 of ." green"                endof
\EN             $03 of ." cyan"                 endof
\EN             $04 of ." red"                  endof
\EN             $05 of ." magenta"              endof
\EN             $06 of ." brown"                endof
\EN             $07 of ." light gray"           endof
\EN             $08 of ." dark gray"            endof
\EN             $09 of ." light blue"           endof
\EN             $0A of ." light green"          endof
\EN             $0B of ." light cyan"           endof
\EN             $0C of ." light red"            endof
\EN             $0D of ." light magenta"        endof
\EN             $0E of ." yellow"               endof
\EN             $0F of ." white"                endof
                drop
         endcase  ;

: %.bg          ( n1 --- )      \ n1=Farbwert
        dup>r %.fg r@ $08 and
        if space r@ case
\GR             $08 of ." bzw. SCHWARZ"         endof
\GR             $09 of ." bzw. BLAU"            endof
\GR             $0A of ." bzw. GRšN"            endof
\GR             $0B of ." bzw. ZYAN"            endof
\GR             $0C of ." bzw. ROT"             endof
\GR             $0D of ." bzw. MAGENTA"         endof
\GR             $0E of ." bzw. BRAUN"           endof
\GR             $0F of ." bzw. HELLGRAU"        endof

\EN             $08 of ." or black"             endof
\EN             $09 of ." or blue"              endof
\EN             $0A of ." or green"             endof
\EN             $0B of ." or cyan"              endof
\EN             $0C of ." or red"               endof
\EN             $0D of ." or magenta"           endof
\EN             $0E of ." or brown"             endof
\EN             $0F of ." or light gray"        endof
                drop
\GR              endcase space ." BLINKEND"
\EN              endcase space ." blinking"
        then r>drop ;



: <.color>      ( n1 --- )      \ n1=Attributbyte
                                \ Namen der Farben ausgeben
\GR     #out @ >r dup $0f and space %.fg ."  auf "
\EN     #out @ >r dup $0f and space %.fg ."  on "
                $f0 and $10 / %.bg 50 r> + #out @ - spaces ;


: <showcolor>   ( n1 --- )      \ Testzeile mit gewaehlter Farbe ausgeben
        save!> attrib corgx corgy 2 6 d+ at."                   "
                                         at."    () / ()=       "
                                         at."      |           "
                                         at."      ==<          "
                                         at."                   "
                                         at attrib @ <.color>
        restore> attrib ;

' <showcolor> is showcolor

: changecolor   ( n1 --- n2 n3) \ n1=Eingangsfarbwert
                                \ n2=Ausgangsfarbwert n3=Tastenwert
        dup showcolor key dup>r case
                                208 of dup 1- $0f and swap $f0 and or endof
                                200 of dup 1+ $0f and swap $f0 and or endof
                                203 of dup $10 - $f0 and swap $0f and or endof
                                205 of dup $10 + $f0 and swap $0f and or endof
                                        drop
                                endcase    \ n2
        r@ 13 = r@ 27 = or if r> else r>drop 0 then ;


: newcolset     ( --- n1 f1 )   \ Rueckgabe: Farbwert f1=true fuer Speichern
        showbox coloradr @ begin changecolor ?dup until 13 = ;

: <setcolor>    ( a1 --- )      \ a1=Adresse des zu aendernden Farbwertes
        !> coloradr newcolset if coloradr ! else drop then >norm ;

: setcolor      ( a1 --- )      \
        savecursor savescr <setcolor> restscr restcursor >norm ;

defer syscolorbox

: at.color.dn   ( n1 n2 -- n3 n4) \ Farbe ausgeben dann Coordinatenzeile
                                  \ inkrementieren
        at." " attrib @ <.color> ;

: <syscolorbox> ( --- )         \ Anzeige der Systemfarben in einer Box
        corgx corgy 2dup 65 17 d+ box&fill
        >attrib2 corgx corgy 2 2 d+
\GR     at."  Michel's Farbauswahltool "
\GR     3 + >attrib1
\GR     at." NORMAL  "
\GR     at." REVERS  "
\GR     9 1 do at." FARBE " i . loop
\EN     at."  Michel's color select tool"
\EN     3 + >attrib1
\EN     at." normal  "
\EN     at." reverse "
\EN     9 1 do at." color " i . loop
        2drop corgx corgy 12 6 d+ >norm
        at.color.dn >rev
        at.color.dn
        9 1 do i colortbl @ attrib ! at.color.dn loop 2drop >norm
\GR     corgx corgy 30 1 d+ 2dup at ." \8 Fertig = \1ESC\8\s09"
\GR                      1+ 2dup at ." \8 Farbe „ndern = \1<ÄÙ\8\s03"
\GR                      1+ 2dup at ." \8 vorige Farbe  = \1  \8  "
\GR                      1+      at ." \8 n„chste Farbe = \1  \8  " ;
\EN     corgx corgy 30 1 d+ 2dup at ." \8 done = \1ESC\8\s11"
\EN                      1+ 2dup at ." \8 change color = \1<ÄÙ\8\s03"
\EN                      1+ 2dup at ." \8 prev color = \1  \8\s05"
\EN                      1+      at ." \8 next color = \1  \8\s05  " ;

' <syscolorbox> is syscolorbox

variable selected

: ?cursorpos    ( --- )         \ positioniert Cursor neben selektierter
        corgx corgy 11 7 d+ selected @ + at ;  \ Farbe

: <syscolor>    ( --- )         \
        big-cursor syscolorbox -1 selected !
        begin ?cursorpos key dup 27 <>
        while case 200 of selected dup @ 1- dup -2 = if drop 8 then swap ! endof
                   208 of selected dup @ 1+ dup 9 = if drop -1 then swap ! endof
                   13  of selected @ dup 0> if colortbl <setcolor> else
                          if normal else reverse then <setcolor> then
                          syscolorbox endof
                   beep drop
              endcase
        repeat drop >norm ;

: syscolors     ( --- )         \ Farbauswahltool fr alle Systemfarben
        savecursor savescr <syscolor> restscr restcursor >norm ;

: orig-Pal      ( --- )
        BLUE    1 colortbl hn!   GREEN   1 colortbl ln! \ set the default attribute
        RED     2 colortbl hn!   WHITE   2 colortbl ln! \ forground and background
        GREEN   3 colortbl hn!   WHITE   3 colortbl ln! \ colours .
        RED     4 colortbl hn!   WHITE   4 colortbl ln!
        BLUE    5 colortbl hn!   GREEN   5 colortbl ln!
        RED     6 colortbl hn!   WHITE   6 colortbl ln!
        GREEN   7 colortbl hn!   WHITE   7 colortbl ln!
        RED     8 colortbl hn!   WHITE   8 colortbl ln!
        $07 !> NORMVAL     \ VALUE FOR NORMAL VIDEO
        $78 !> REVVAL      \ VALUE FOR REVERSE VIDEO
        >norm colorset ;

: my-Pal        ( --- )
        BLUE    1 colortbl hn!   LTGRAY  1 colortbl ln! \ set the default attribute
        LTRED   2 colortbl hn!   RED     2 colortbl ln! \ forground and background
        GREEN   3 colortbl hn!   WHITE   3 colortbl ln! \ colours .
        LTRED   4 colortbl hn!   BLACK   4 colortbl ln!
        BLUE    5 colortbl hn!   BROWN   5 colortbl ln!
        DKGRAY  6 colortbl hn!   BLUE    6 colortbl ln!
        GREEN   7 colortbl hn!   BROWN   7 colortbl ln!
        BROWN   8 colortbl hn!   YELLOW  8 colortbl ln!

        BLACK     normal   hn!   RED       normal   ln!
        BLACK     reverse  hn!   LTGRAY    reverse  ln!
        >norm colorset ;

: my-lcdPal     ( --- )
        YELLOW  1 colortbl hn!   WHITE   1 colortbl ln! \ set the default attribute
        RED     2 colortbl hn!   WHITE   2 colortbl ln! \ forground and background
        GREEN   3 colortbl hn!   WHITE   3 colortbl ln! \ colours .
        RED     4 colortbl hn!   WHITE   4 colortbl ln!
        BLUE    5 colortbl hn!   WHITE   5 colortbl ln!
        RED     6 colortbl hn!   WHITE   6 colortbl ln!
        LTGRAY  7 colortbl hn!   WHITE   7 colortbl ln!
        RED     8 colortbl hn!   WHITE   8 colortbl ln!

        BLACK     normal   hn!   LTGRAY    normal   ln!
        LTBLUE    reverse  hn!   DKGRAY    reverse  ln!

        >norm colorset ;

cr .( palette editor loaded )
cr .( please try     5 SETCOLOR  )

