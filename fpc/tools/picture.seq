\\ PICTURE.SEQ           Pictured numeric output
.options /opt /noinit
  Provide pictured numeric output for F-PC and TCOM.

  The picture string can contain any ascii characters, which will be passed
through to the output, with the exception of three special characters:

        #       convert one digit of the number
        ?       convert one digit of the number if not 0, else hold a blank
        &       convert remaining digits, if any are remaining in number

  "D. used in the form <double-number> "<picture>" "D. (quote-dee-dot).

  ".  used in the form <single-number> "<picture>" ". (quote-dot).

  $D. used in the form <double-number> <counted-string> $D. (string-dee-dot).

  $.  used in the form <single-number> <counted-string> $. (string-dot).

{

: (".)          ( d1 a1 n1 -- a2 n2 )   \ build the pictured quote string
                <# bounds swap 1-       \ of double number to a string result
                do     i c@
                        case    '#' of  #               endof
                                '?' of  2dup d0=
                                        if      bl hold
                                        else    #
                                        then            endof
                                '&' of  begin   2dup or
                                        while   #
                                        repeat          endof
                                        hold
                         endcase
             -1 +loop    #> ;

: ($.)          ( d1 a1 -- a2 n1 )      \ counted quote string conversion of
                count (".) ;            \ double number to a string result

: "D.           ( d1 a1 n1 -- )         \ display pictured quote string
                (".) type ;             \ of a double number

: ".            ( n1 a1 n1 -- )         \ display pictured quote string
                0 -rot (".) type ;      \ of a single number

: $D.           ( d1 a1 -- )            \ display pictured counted string
                ($.) type ;             \ of a double number

: $.            ( n1 a1 -- )            \ display pictured counted string
                0 swap ($.) type ;      \ of a single number





\S              *** Stop here, test code follows ***


: test          ( -- )                  \ a pictured number test word
                12345678. " [&##.##]" "D.       \ at least 4 digits, plus
                                                \ more if needed
                123       " [???.##]" ". ;      \ at least 2 digits, plus
                                                \ three leading digits or
                                                \ spaces to fill

