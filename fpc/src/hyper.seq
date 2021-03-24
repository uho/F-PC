\ HYPER.SEQ     Simple ALL Vocabulary search for FPC     by Tom Zimmer

\ a couple of definition search words. The first HFIND searches all possible
\ vocabularies, the second VFIND searches a specific vocabulary for a word.

\ HYPERTEXT find word searches all vocabularies. First vocs in search order
\ then FORTH, then all vocs in VOC-LINK in reverse defined order until found.
: hfind         ( a1 -- a2 f1 )         \ a1 = counted word
                                        \ a2 = the CFA if a1 found
                                        \ a2 = a1 if a1 was not found
                                        \ f1 boolean TRUE if found
                ?uppercase find ?dup ?exit              \ leave if found
                dup>r ['] forth >body                   \ check FORTH first
                over.swap.hash.@ (find) ?dup 0=
                if      voc-link @                      \ then the rest
                        begin   r@ over #threads 2* -
                                over.swap.hash.@ (find)
                                if      nip nip
                                        true true
                                else    drop
                                        @ dup 0=
                                then
                        until
                then    r>drop ;

\ Search a specific vocabulary for the word specified.
\ The counted string a1 is moved to here, 2 blanks are appended, and it
\ is conditionally converted to uppercase. Vocabulary CFA a2 is then
\ searched for the word
: vfind         ( a1 a2 --- a3 f1 )     \ a1 = counted word
                                        \ a2 = vocabulary CFA
                                        \ a3 = the CFA if a1 found
                                        \ a3 = a1 if a1 was not found
                                        \ f1 boolean TRUE if found
                swap here over c@ 1+ cmove
                $2020 here count + !
                here ?uppercase swap >body
                over.swap.hash.@ (find) ;

