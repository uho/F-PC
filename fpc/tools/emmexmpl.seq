\ EMMEXMPL.SEQ          Example EMM usage               by Tom Zimmer

comment:

        This file contains an example of how to use the Expanded Memory
        inferface defined in the file EXPANDED.SEQ.  This file also
        illustrates the SEQUENCE of the commands required to access
        EMM.

comment;

\ EMM status code evaluator, prints the error code for any error received.

: ?emm          ( --- )                 \ test the most recent emm operation
        @> emm-status 0= if exit then   \ leave, there is no problem.
        @> emm-status >r
        r@ $80 = abort" Serious EMM software problem."
        r@ $81 = abort" Serious EMM hardware problem."
        r@ $82 = abort" The EMM system is BUSY."
        r@ $83 = abort" EMM can't find the handle specified."
        r@ $84 = abort" Invalid function code passed to EMM."
        r@ $85 = abort" No handles are currently available."
        r@ $86 = abort" A mapping context restoration error detected."
        r@ $87 =
        r@ $88 =
              or abort" Requested pages exceeds available pages."
        r@ $89 = abort" Could not allocate ANY pages."
        r@ $8A = abort" Logical page to map is not in allocated range."
        r@ $8B = abort" Physical page specified is not valid."
        r@ $8C = abort" The mapping context save area is full."
        r@ $8D = abort" Attempt to save context of already saved handle."
        r@ $8E = abort" Attempt to restore context without saving first."
        r@ $8F = abort" Subfunction passed is not defined."
        r> cr ." Received EMM error code " u. abort ;

0 value mxemm
0 value emmhandle
0 value emmver

: emm-sample    ( --- )
( 1 )   emm-present?                            \ is there any EMM in system?
        0= abort" No Expanded memory available!"

( 2 )   emm-page-frame ?emm                             \ set the page frame

( 3 )   emm-get-version ?emm 255 and                    \ get the EMM version
        cr ." Expanded Memory Version " dup h. =: emmver \ save EMM version

        \ check the EMM version to be sure it is compatible
( 4 )   emmver $30 < abort" The EMM version must be 3 or higher!"

( 5 )   emm-total-pages ?emm                            \ What is total pages
        cr ." Total Pages of EMM " .

( 6 )   emm-avail-pages ?emm                            \ get available EMM
        cr ." Available Pages of EMM " dup . =: mxemm   \ set MAX EMM value

        \ check here to see if there is enough EMM available
( 7 )   mxemm 2 < abort" Need at least 2 pages of EMM!"

        cr ." Allocating " mxemm . ." Pages of EMM."
( 8 )   mxemm emm-alloc-pages ?emm =: emmhandle         \ allocate the pages

( 9 )
[ comment:
        Access the EMM in your program using EMM-MAP-PAGES. The
        parameters required by EMM-MAP-PAGES are: Logical Page,
        Physical Page, and EMM Handle. The Logical page is a value in
        the range zero (0) to the number of allocated pages minus one
        (1). The Physical page is a value in the range zero (0) to
        three (3), where each page is 16k bytes starting at offset
        $0000, $4000, $8000 & $C000 from the Page Frame Base address.
        Up to these four physical pages (64k bytes) can be accessed
        without having to perform another EMM call to get more pages.
        The EMM Handle is the handle number returned by the
        EMM-ALLOC-PAGES call. Be sure to use ?EMM or its equivelant
        after each EMM call to verify operation is continuing according
        to plan.
comment;
]
        cr ." Writing patterns to EMM, one moment..."
        mxemm 0                                   \ fill EMM with pattern
        ?do     i 0 emmhandle emm-map-pages ?emm  \ get a page & chk for err
                ?emm: 0 4000                      \ start of EMM for 16k len
                7 flip                            \ attribute in high byte
                i $30 +                           \ changing pattern value
                255 and                           \ clip to character
                or                                \ or in attribute
                LFILLW                            \ fill WORDS with pattern
        loop

        cr ." Done creating patterns"
        cr ." Now I will move the patterns to the screen as fast as I can."
        cr ." Press a key to see the patterns"
        key drop                                  \ wait for user

        0 0 emmhandle emm-map-pages ?emm          \ get first page
        video-seg @ 0 ?emm: 0 4000 cmovel         \ save current screen

        mxemm 0                                   \ move pages to screen FAST
        ?do     i 0 emmhandle emm-map-pages ?emm  \ get a page
                ?emm: 0 video-seg @ 0 4000 cmovel \ move it to screen
        loop

        0 0 emmhandle emm-map-pages ?emm          \ get first page again
        ?emm: 0 video-seg @ 0 4000 cmovel         \ restore screen

        cr
        cr ." Pretty FAST HUH?"
        cr ." Deallocating " mxemm . ." Pages of EMM Handle "
        emmhandle .

( 10 )  emmhandle emm-dealloc-pages ?emm          \ deallocate the space
        cr ." That's ALL Folks!"
        cr ;


