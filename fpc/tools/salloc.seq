\\ SALLOC           An example of memory allocation      by Tom Zimmer

  This file contains an example of how to do memory allocation in F-PC.

  It replaces the old SALLOC.SEQ file that recommended using ALLOC and
  DEALLOC, which have now been superceeded by POINTERs.

  WARNING:      You should NOT use ALLOC or DEALLOC any more, they
                conflict with the new POINTER memory management
                system built into F-PC.

{

16000. 2CONSTANT bytes_needed   \ Size of the array I need in bytes.

bytes_needed POINTER myseg      \ Define my POINTER called 'myseg' of the
                                \ needed bytes.

\ To put something into the array MYSEG, I might do something like the
\ following.  The "far-away" space for 'myseg' is automatically allocated
\ the first time 'myseg' is used in an application.

: string-save   ( | <string> --- )      \ accept a <string> to save
                ?cs: 0 word dup>r       \ moving FROM here
                myseg 0                 \ TO the beginning of MYSEG
                r> c@ 1+ CMOVEL ;       \ move the data with CMOVE LONG

\ And you can then display the text from MYSEG.

: type-string   ( --- )
                myseg 0                 \ FROM myseg
                countL                  \ get the count of the far-away string
                ?cs: here rot placeL    \ place the string at HERE
                here count type ;       \ display it

\ You could have also:  "MYSEG 0 COUNTL TYPEL"  to just type the string
\ while it resided in the far-away pointer buffer.

\ If you need to deallocate the space that you have allocated, you can use
\ UNPOINTER to release the space back to DOS.

: release-myseg ( --- )
                unpointer> myseg ;

\ YOU NEED TO BE **** EXTREAMLY **** CAREFUL NOT TO WRITE BEYOND THE END
\ OF A DEFINED POINTER.   WRITING BEYOND THE END OF A POINTER WILL CRASH
\ DOS, AND REQUIRE A REBOOT OF YOUR COMPUTER !!!!!!!!!!!!!!!!!!!!!!!!!!!

