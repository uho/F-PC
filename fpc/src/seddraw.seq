\ SEDDRAW.SEQ   Line drawing utility for SED            by Tom Zimmer

editor definitions

variable marginsave
variable pendown
variable 2line
         2line off

: achr          ( --- )
                create ,
                does> @
                false save!> imode
                schr lchr
                showcur
                restore> imode ;

\ t-top, b-bottom, l-left, r-right, m-middle, 1-1line

$0DA achr tl1   $0BF achr tr1   $0D9 achr br1   $0C0 achr bl1
$0B3 achr lr1   $0C4 achr tb1   $0C2 achr tm1   $0C1 achr bm1
$0C3 achr lm1   $0B4 achr rm1   $0C5 achr mm1
$0C9 achr tl2   $0BB achr tr2   $0BC achr br2   $0C8 achr bl2
$0BA achr lr2   $0CD achr tb2   $0CB achr tm2   $0CA achr bm2
$0CC achr lm2   $0B9 achr rm2   $0CE achr mm2

: .?lines       ( --- )
                2line @
                if      ." ="
                else    ." -"
                then    ;

: oneline       2line off window.left statusline at
                >rev .?lines >norm showcur ;

: twoline       2line on  window.left statusline at
                >rev .?lines >norm showcur ;

: ?12line       2line @ if twoline else oneline then ;

: ?pd           ( --- ) pendown @ ;

: ?grafchar     ( n1 --- f1 )
                screenchar + dup >r 0MAX
                linebuf + 1+ c@ $0B3 $0DA between r> 0=
                if      drop 0 then ;

: ?around       ( --- f1 )  0
( left )       -1 ?grafchar if 1 or then
( right)        1 ?grafchar if 2 or then
( up   )        curline 1 >
                if <suln> 0 ?grafchar if 4 or then <sdln> then
( down )        curline lastline <
                if <sdln> 0 ?grafchar if 8 or then <suln> then ;

\                0   1   2   3   4   5   6   7
: chars         ( n1 --- )
                exec:
                tb1 tb1 tb1 tb1 lr1 br1 bl1 bm1
                lr1 tr1 tl1 tm1 lr1 rm1 lm1 mm1
                tb2 tb2 tb2 tb2 lr2 br2 bl2 bm2
                lr2 tr2 tl2 tm2 lr2 rm2 lm2 mm2 ;

: drchr         ( --- )
                ?around 15 and 2line @ if 16 + then chars ;

: ?drchr        ( --- )         \ redraw if already graphics
                0 ?grafchar     \ graphics char?
                if      drchr   \ re-draw it
                then    ;

: fixaround     ( --- ) \ check and adjust bordering chars.
        screenchar      if      lchr ?drchr rchr then
        curline 1 >     if      upln ?drchr dnln then
        curline lastline <
                        if      dnln ?drchr upln then
        screenchar 1+ rmargin @ <
                        if      rchr ?drchr lchr then showcur ;

: penup/down    ( --- )
                pendown @ 0= dup pendown ! >norm showcur
                if      drchr fixaround
                        window.left statusline at >rev .?lines ." Pen DOWN"
                else    window.left statusline at >ul  .?lines ." Pen UP  "
                then    >norm showcur ;

: drawinit      ( --- )
                rmargin @ marginsave ! mxlln rmargin !
                pendown off penup/down window.left 8 + statusline at
                >ul ."  " >rev ."  ARROWS=draw, "
      ." - 1line, = 2line, INS=PenUP/DOWN, DEL=clear, ESC=done."
                >ul edeeol >norm showcur ;

: clearchar     ( --- )
                false save!> imode
                bl schr
                restore> imode
                lchr fixaround ;

: goright       ( --- ) rchr ?pd if drchr fixaround then ;

: goleft        ( --- ) lchr ?pd if drchr fixaround then ;

: godown        ( --- ) dnln ?pd if drchr fixaround then ;

: goup          ( --- ) upln ?pd if drchr fixaround then ;

: lines         ( --- )         \ line drawing commands
                ?browse ?exit
                drawinit ?12line drchr fixaround
        begin   showcur key dup 27 ( ESC) <>  over 193 ( F7) <> and
                  \ while not   ESCAPE    and not  F7.
        while       '-' over = if   drop oneline    ?drchr else
                    '=' over = if   drop twoline    ?drchr else
        211 over = over 8 = or if   drop clearchar         else
( pendown )         210 over = if   drop penup/down        else
( right )           205 over = if   drop goright           else
( left  )           203 over = if   drop goleft            else
( up    )           200 over = if   drop goup              else
( down  )           208 over = if   drop godown            else
                drop then then then then then then then then
        repeat  drop marginsave @ rmargin ! ;

' lines is drawlin

forth definitions

