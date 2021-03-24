\ PERTYPE         Backslash Type, Imbedded Attribute Switches

comment:                                        by Tom Zimmer

  This utility allows the creation of special ." strings that contain
imbedded escape sequences to select various display operations.

  The leading character is "\", followed by:

        0               Normal display attributes
        1 to 8          Attributes 1 through 8
        :xx             Delay xx tenths of a second
        <               Save the current cursor position     (not nestable)
        >               Restore the previous cursor position (not nestable)
        @xxyy           Move cursor to xx,yy
        B               Beep the speaker
        D               Dark (clear screen)
        N               Newline (CRLF)
        R               Select Reverse video
        Sxx             Display xx spaces
        T               Perform a Tab
        \               Display a \ character
        `               Display a " character
        |               Display a linefeed character

  The performance of this version of ." will be somewhat slower than
a normal TYPE, as the string must be scanned for the "\" escape character.

comment;

: packed_asc>#  ( n1 --- n2 )           \ convert packed ascii pair to a #
                $3030 - split swap 10 * + ;

: \%spaces      ( a1 n1 --- a2 n2 )     \ display a number of spaces
                over @> sseg swap @L packed_asc># spaces
                2 /string ;

: \%tenths      ( a1 n1 --- a2 n2 )     \ delay a number of tenths of second
                over @> sseg swap @L packed_asc># tenths
                2 /string ;

: \%at          ( a1 n1 --- a2 n2 )     \ move cursor to new location
                over @> sseg swap
                2dup @L packed_asc># >r
                  2+ @L packed_asc># r> swap at
                4 /string ;

0 value \x                              \ a place to save cursor x & y
0 value \y

: \at-save      ( -- )                  \ save the cursor position
                @> #out  =: \x
                @> #line =: \y ;

: \at-rest      ( -- )                  \ restore the cursor position
                \x \y at ;

: "emit         ( -- )  '"' femit ; \ emit a "
: \emit         ( -- )  '\' femit ; \ emit a \
: lfemit        ( -- )   10  emit ; \ emit a Linefeed

                \ escape chars for the functions in the following definition
create \chars   ," R0TSN12345678BD@\`|<>:"

: do\char       ( a1 n1 c1 -- a2 n2 )   \ handle one escape sequence
                upc                     \ convert char case
                ?cs: save!> sseg
                \chars count rot scan drop \chars - 1- 22 min
                restore> sseg
                exec:   >rev      >norm     tab       \%spaces  cr
                        >attrib1  >attrib2  >attrib3  >attrib4  >attrib5
                        >attrib6  >attrib7  >attrib8  beep      dark
                        \%at      \emit     "emit     lfemit
                        \at-save  \at-rest  \%tenths  >norm ;

: \typeL        ( seg a1 n1 -- )        \ type an interpreted string in SEG
                save> attrib
                rot save!> sseg
                begin   2dup '\' scan nip ?dup
                while   over >r - dup>r over >r @> SSEG -ROT typeL
                        r> r> r> swap 2+ /string
                                        \ 2+ removes 2 extra chars
                        over @> sseg swap 1- c@L
                        do\char
                repeat  @> SSEG -ROT typeL
                restore> sseg
                restore> attrib ;

: \type         ( a1 n1 -- )    \ type an interpreted string in CODE space
                ?cs: -rot \typeL ;

: (\.")         ( -- )

                2r@ 2dup c@l >r 1+ r@ \typeL r> 1+ xeven r> + >r ;

' (\.") is (.")         \ link new (\.") into (.")

