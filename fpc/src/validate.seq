\\ VALIDATE.SEQ         Release Validation for F-PC     by Tom Zimmer

  Inform the user if this copy of F-PC is more than 1 year old, so they
can consider getting an update.

{

hidden definitions

$0C1207CA. 2constant reldate            \ hard coded release date for F-PC
create validmsg ,"  [REBUILT]"          \ This is a modified version of F-PC

: ?validate     ( -- )
        getdate split swap 0 2>r 31 um* 2>r 372 um* 2r> d+ 2r> d+
        reldate split swap 0 2>r 31 um* 2>r 372 um* 2r> d+ 2r> d+
        372. d+ d>
        if      cr
                cr ." This copy of F-PC is over 1 year old."
                cr ." You might want to consider getting it updated."
                cr ." To disable validation, type: HIDDEN UNVALIDATE"
                cr
                cr ." Contact: Tom Zimmer at: (408) 263-8859 for update information."
                cr
                cr ." Continue [Y/n]: " key upc 'N' =
                if      bye
                then
        then    ;

