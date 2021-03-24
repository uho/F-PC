\ NEEDS.SEQ     Conditionally load a file               by Tom Zimmer

only forth also definitions hidden also

: ?loaded       ( name --- f1 )
                save> context                   \ save CONTEXT vocabulary
                files                           \ select FILES vocabulary
                bl word ?uppercase
                dup pathset drop                \ prepend the path
                find nip ?dup 0=                \ did we find word with path
                if      here >pathend" here c!  \ if not strip path off
                        here count cmove
                        here count + 2 blank
                        here find nip           \ and look for it without path
                then    restore> context ;

                        \ print a list of all files that have been loaded.
: needs         ( | name --- )
                >in @ >r
                ?loaded 0=              \ if we dont have it
                if      r@ =: >in
                        fload           \ then loadit
                        false save!> warning
                            loaded,     \ reset to previous file for view.
                        restore> warning
                then    r>drop ;

/*              \ This can be loaded if you need it.

: unwanted      ( | name --- )
                ?loaded
                if      cr cr seqhandle count type
                        ."  <-- must be loaded before --> "
                        seqhandle+ count type cr abort
                then    ;

*/

only forth also definitions


