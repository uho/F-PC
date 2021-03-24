\\ VMOVE.SEQ            Vocabulary Word Move            by Dr. C. H. Ting

Modified version of Dr. Ting's ZAP word, moves a word from the CONTEXT
vocabulary (the search vocabulary) to the CURRENT vocabulary (the
definitions vocabulary).

Useful for moving words around in the Forth system between vocabularies.

                        Modiofications by Tom Zimmer 04/01/93

{

root definitions

I: name? ( yLink -- f, 0 if name does not match that in HERE )
        l>name                          \ from lfa to nfa
        dup y@                          \ compare count and 1st byte
        here @ xor                      \   to that in HERE
        $7f1f and                       \ mask off irrelevant bits
        if drop 0 exit then             \ first test failed, exit
        here c@ 2 <
        if drop -1 exit then            \ short name match, exit with -1
        2+ here 2+                      \ long name, scan all chars
        here c@ 1- 0 do
                over yc@ over c@        \ compare one byte at a time
                xor $7f and             \ mask off the terminating bit
                if 2drop 0 undo exit then   \ mismatch, exit
                1+ swap 1+ swap         \ go to the next byte
        loop    2drop
        -1 ;                            \ all bytes match, return true.

: vmove ( -<name>- )
        current @ context @ =
        if      cr ." VMOVE, moves a word from the CONTEXT vocabulary to"
                cr ." the CURRENT vocabulary, you MUST setup your vocabulary"
                cr ." search and definition order first!" abort
        then
        bl word ?uppercase              \ get next word in input stream
        context @ hash @                \ head of this thread in context voc
        dup name?                       \ is the word last in the tread?
        if                              \ last word need special care
           dup >r y@                    \ lfa of next word in the thread
           here context @ hash !        \ delete this word from context voc
                                        \ by pointing context to next word
        else                            \ not last word, search thread
           begin                        \ save current link
             dup y@                     \ get lfa of the next word
             dup 0=                     \ if 0, tread is exhausted
             if
                here count type         \ print error message
                ."  not linked" cr
                2drop exit              \ and give up.
             then
             name? 0=                   \ Is next link the target word?
           while y@   \ dup l>name .id  \ No yet. continue searching
           repeat                       \ tos is link prior to target word
           dup y@ dup >r                \ save lfa of target word
           y@ swap y!                   \ delete this word from thread
        then
        here current @ hash             \ get target thread in current voc
        dup @ r@ y!                     \ link this word to current voc
        r> swap !                       \ update thread in current vocabulary
        ;

only forth also definitions


