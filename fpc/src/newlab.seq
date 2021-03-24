\ NEWLAB.SEQ   New local label mechanism   by Tom Zimmer & Jerry Boutelle
comment:
  NEWLAB is designed to handle the very flexible needs of F-PC, TCOM and
any target processor being brought up on TCOM or F-PC.  The local label
mechanism it replaces did not know how to handle all of the various types
of forward references that might need to be resolved. This new method,
defines a resolution type for each forward reference record, and calls a
procedure provided by the current assembler for each type of reference
being resolved.  This places the burden of actually resolving the
reference on the assembler where it belongs.

  NEWLAB uses an F-PC "POINTER" array to hold the datum for each local
label, and each forward reference that occurs.  The array is split into
two parts, the local label records, which consist of three bytes for each
local label, times 512 labels is 1536 bytes. The reference records are
four bytes each, and are allocated as forward references are encountered,
and released to a free chain as forward references are resolved. There
are 256 forward reference records, totaling 1k bytes.


  The local label and forward reference records appear as follows:

                           LOCAL LABEL RECORD

      ÚÄ
      ³ 1 res-ptr   A pointer to the resolution chain,
      ³             0 if the label has already been resolved.
      ³ 2 res-addr  The resolved address of this local label
      ÀÄ

        ..... The above record is repeated for each local label up to
              MAX-LLAB-1 the maximum number of local labels allowed.

        The label record area of the local label array is then fixed in
        size as MAX-LLAB times 3, or 1536 bytes for MAX-LLAB=512 local
        labels.

                        FORWARD REFERENCE RECORD

      ÚÄ
      ³ 1 ref-link  Link of next forward reference of this label
      ³ 1 ref-type  Resolution type for this forward reference
      ³ 2 targ-adr  The target address for this forward reference
      ÀÄ

        .... The above record is repeated and linked for each forward
             reference.

        The reference record area is also fixed in size as MAX-LREF times
        4, or 1024 bytes for MAX-LREF=255 references.  Since a byte is
        used as a reference link pointer, the maximum number of unresolved
        forward references outstanding at one time is limited to 255.



FORWARD REFERENCE TYPES

  Forward reference types are byte values in the range 0 to 255 decimal.
The value zero is reserved to indicate a reference that was not properly
setup by the assembler.  This might happen if an illegal mnemonic was used
in the TCOM compiler, where no actual code was compiled for a line which
contains a local label. Here is an example:


                MAV AX, mytable         \ get first table entry
                1PUSH   END-CODE

LABEL mytable   345 , 567 , 789 ,       \ make a table

  In this example, MAV is not a legal instruction, but TCOM doesn't know
that, so it turns "MAV" into a local label like "mytable". When the local
lable for "mytable" comes along, two local labels are now defined on the
same line, which will cause an error:

                 " Label or symbol incorrectly assembled"

comment;

only forth also assembler definitions also

\ **********************************************************************
\ a field defining word, makes words that add a value to top stack item
\ **********************************************************************

I: +field       ( n1 | name -- n1 )     \ define a reference record field
                create dup c,
                ;code   pop bx
                        pop ax
                        mov bl, 0 [bx]
                        sub bh, bh
                        add ax, bx
                        1push           end-code

\ **********************************************************************
\ Define the fields for local label records and forward reference records
\ **********************************************************************

0                       \ LOCAL LABEL fields start at offset zero
+field >llptr    1+     \ move to the local label reference pointer field
+field >lladr    2+     \ move to the local label target address field
constant b/llab         \ bytes per local label

0                       \ REFERENCE fields start at offset zero
+field >rlink   1+      \ move to the reference link field (one byte)
+field >rtype   1+      \ move to the reference type field (one byte)
+field >taddr   2+      \ move the the target address field (two bytes)
constant b/lref         \ bytes per label forward reference

\ **********************************************************************
\ *********** Runtime specifiable limits of local labels ***************
\ **********************************************************************
\
\  The formula:
\
\          MAX-LLAB B/LLAB * MAX-LREF B/LREF * +
\
\  must be less than 65535, and MAX-LREF may not be larger than 255
\  unless the reference record ">RLINK" field is expanded to two bytes
\  from the current one byte.
\
\ **********************************************************************

   8 value MAX-LNEST    \ maximum local label nesting in macros
 512 value MAX-LLAB     \ maximum number of local labels during a compile
 256 value LOC-LLAB     \ maximum LOCAL labels, the rest are global
 255 value MAX-LREF     \ maximum forward references outstanding
                        \ *** MUST BE LIMITED TO 256 CURRENTLY ***

\ **********************************************************************
\ Half of the labels are created as local, that is only half of the labels
\ are cleared by CLEAR_LABELS or LLAB-INIT. As defined above, labels with
\ a number between 512 and 1023 are always global labels, and may not be
\ DEFINED more than ONCE during a program compile.
\ **********************************************************************

\ **********************************************************************
\ Create the label segment "pointer". Automatically allocates any space
\ needed, the first time LABSEG is used.
\ **********************************************************************

max-llab b/llab *
max-lref b/lref * +
    dup value b/llset           \ bytes per local label set
max-lnest 1+ um*                \ let local labels recurse eight times
        pointer labseg          \ the local label array

\ **********************************************************************
\ some local label pseudo constants, initialized by LLAB-INIT
\ **********************************************************************

0 value llab-start      \ start of label record area = 0
0 value lref-start      \ start of label reference area
0 value llab-size       \ label record area size in bytes
0 value lref-size       \ label reference area size in bytes

\ **********************************************************************
\ 8 deep round robin of the sets of local labels
\ **********************************************************************

forth definitions

\ 0 value llset_cnt       \ debugging tool, keeps track of push/pop sequence

\+ llset_cnt    : +llset_cnt    ( n1 -- )
\+ llset_cnt            llset_cnt + !> llset_cnt ;

: llset+        ( -- )
\+ llset_cnt    1 +llset_cnt
                ['] labseg >body @ 0=
                abort" Attempt to use LLSET+ without initializing labels"
                b/llset max-lnest * >r
                labseg 0  labseg b/llset      r@ cmovel>
                labseg r> labseg 0       b/llset cmovel ;

: llset-        ( -- )
\+ llset_cnt     -1 +llset_cnt
                b/llset max-lnest * >r
                labseg       0 labseg r@ b/llset cmovel
                labseg b/llset labseg 0       r> cmovel ;

assembler definitions

\ **********************************************************************
\ Some VALUE variables
\ **********************************************************************

0 value free-lref       \ next free reference record
0 value curlabel        \ the current local label
0 value curref          \ current forward reference record number

\ **********************************************************************
\ Translate the local label number or forward reference number into a
\ physical segment and offset into the POINTER array.
\ **********************************************************************

: llab>line     ( n1 -- seg off )       \ locate the local label n1
                [ forth ]
                dup 0 max-llab within 0=
                abort" Local label out of range"
                labseg swap b/llab * llab-start + ;

I: lref>line    ( n1 -- seg off )       \ locate reference given ref#=n1
                [ forth ]
                labseg swap b/lref * lref-start + ;

\ **********************************************************************
\ Words to allocate ane release forward reference records
\ **********************************************************************

I: relref       ( n1 -- )               \ add lref record to free chain
                [ forth ]
                free-lref over lref>line >rlink c!L !> free-lref ;

I: getref       ( -- n1 )               \ get one lref from the free chain
                [ forth ]
                free-lref dup
                lref>line c@L !> free-lref ;

\ **********************************************************************
\ Initialize the local label and forward reference arrays
\ **********************************************************************

: llab-init     ( -- )                  \ initialize the local label array
                [ forth ]
                ['] labseg >body @ 0=
        if      max-lnest 0
            do  max-llab b/llab * dup !> llab-size      \ label area size
                                  dup !> lref-start     \ start of ref area
                max-lref b/lref * dup !> lref-size +    \ ref area size
                                  dup !> b/llset        \ size of label set
                max-lnest 1+ um* sizeof!> labseg        \ set total lab size
                labseg llab-start llab-size -1 lfill    \ -1 labels
                llab-size 2/ !> llab-size               \ only clear half
                                                        \ of the labels later
                labseg lref-start lref-size  0 lfill    \ zero references
                max-lref 0                              \ release all records
                do      i relref                        \ to the free chain
                loop    llset+                          \ next group
             loop
\+ llset_cnt    off> llset_cnt                          \ clear set counter
        then    -1 !> curlabel                          \ clear current label
                -1 !> curref                            \ clear current ref
                labseg llab-start llab-size -1 lfill ;  \ -1 labels

: llab-zero     ( -- )                  \ zero out all local labels PERIOD!
                [ forth ]               \ locals and globals
                llab-init                               \ first norm init
                labseg llab-start llab-size 2* -1 lfill \ -1 labels
                labseg lref-start lref-size     0 lfill \ zero references
                -1 !> curlabel                          \ clear current label
                -1 !> curref                            \ clear current ref
                max-lref 0                              \ release all records
                do      i relref                        \ to the free chain
                loop    ;

: 01lab         ( n1 -- )               \ zero out a local label record
                a;
                [ forth ]
                llab>line b/llab -1 lfill ;

I: 0ref         ( n1 -- )               \ zero out this reference record
                [ forth ]
                lref>line b/lref 0 lfill ;      \ clear link, type & taddr

\ **********************************************************************
\ used by the assembler to install the information needed to resolve
\ a forward reference.
\ **********************************************************************

: !lref         ( targ-adr ref-type -- )        \ store targ-adr & ref-type
                [ forth ]                       \ into the current ref rec
                curref 0<                       \ was there a forward ref?
                if      2drop                   \ no, discard parameters
                else    curref lref>line 2>r    \ yes, fill in ref record
                        2r@ >rtype c!L          \ set the reference type
                        2r> >taddr  !L          \ set the target address
                        -1 !> curref            \ reset reference number
                then    ;

' !lref is asm!lref             \ link into PASM

\ **********************************************************************
\ Reference a numbered local label
\ **********************************************************************

: $             ( n1 -- a1 )            \ reference local label n1 and
                                        \ return a1 the resolved address
                [ forth ]
                dup !> curlabel         \ set current label
                llab>line 2>r           \ translate to physical label
                2r@ >lladr @L -1 <>     \ if not -1 then resolved
        if      2r@ >lladr @L           \ just return the resolve addr
                -1 !> curref            \ clear any current reference
        else    curref -1 <>
            if  curref lref>line >rtype c@L     \ get previous reference type
                0= abort" Label or symbol incorrectly assembled"
            then                        \ check previous label was setup ok
                getref !> curref        \ get a new reference record,
                                        \ mark it as the current ref record
                curref 0ref             \ zero out the reference record
                2r@ >llptr c@L          \ get and save current ref link
                curref 2r@ >llptr c!L   \ link reference record to locl label
                curref lref>line >rlink c!L
                                        \ install link from prev ref record
                [ assembler ]
                here                    \ return the target "HERE"
                [ forth ]
        then    2r> 2drop ;

\ **********************************************************************
\ Local label resolution words
\ **********************************************************************

defer resolve_ref       ( a1 n1 -- )

' resolve_8086 is resolve_ref

I: res_1ref      ( n1 -- n1 )            \ resolve one forward reference
                [ forth ]
                dup  lref>line                  \ translate to physical ref
                2dup >taddr  @L -rot            \ get the target address
                     >rtype c@L                 \ and the reference type
                resolve_ref ;                   \ go resolve the reference

\ **********************************************************************
\ Define a numbered local label at the current target "HERE"
\ **********************************************************************

: $:            ( n1 -- )
                >r a; r>                \ force compilation of prev instr
                [ forth ]
                -1 !> curref            \ clear any current reference
                dup !> curlabel
                llab>line 2>r                   \ translate to physical label
                2r@ >lladr 2dup @L -1 <>
                if      "" Branch label xxx is already resolved"
                        curlabel 0 <# # # # #> drop 2 pick 13 + 3 cmove
                        "err            \ with inserted label #
                then
                [ assembler ]
                here -rot !L                    \ install resolved targ addr
                [ forth ]
                begin   2r@ >llptr c@L dup 255 <>       \ while ref rec<>255
                while   res_1ref                        \ res one reference
                        dup lref>line >rlink c@L        \ get next reference
                        2r@ >llptr c!L                  \ link to prev ref
                        dup  0ref                       \ zero the ref record
                             relref                     \ release the ref rec
                repeat  2r> 2drop drop ;

\ **********************************************************************
\  Some linkage into the assembler to perform needed initialization
\ **********************************************************************

0 value ll-global?              \ are we using local labels globally?

: lab-start     ( -- )
                [ forth ]
                defers asmstuff
                ll-global? 0=
                if      llab-init               \ in case labels used
                then    ;

' lab-start is asmstuff                 \ install label startup code

: #ll-errs?     ( n1 -- )       \ DUMMY to check for unresolved labels
                [ forth ]
                save> base decimal
                loc-llab umin 0 \ only check the LOCAL labels
                ?do     i llab>line >llptr c@L 255 <>
                        if      "" Branch label xxx was not resolved"
                                i 0 <# # # # #> drop 2 pick 13 + 3 cmove
                                "err            \ with inserted label #
                        then
                loop    restore> base ;

: ll-errs?      ( -- )
                loc-llab #ll-errs? ;

: lab-end       ( -- )
                [ forth ]
                defers end-asmstuff
                ll-global? 0=
                if      ll-errs?        \ check for local label errors
                then    ;

' lab-end is end-asmstuff               \ install label finishup code

\ **********************************************************************
\ Select whether local labels are to be used globally
\ **********************************************************************

' LLAB-INIT ALIAS CLEAR_LABELS

forth definitions

' LLAB-ZERO ALIAS CLEAR_ALL_LABELS

: LOCAL_REF     ( --- )
                OFF> LL-GLOBAL? ;       LOCAL_REF
                                        \ default to LOCAL references only

: GLOBAL_REF    ( --- )
                ON> LL-GLOBAL? ;

only forth also definitions

\S *********************************************************************

\ **********************************************************************
\ a utility to display the label record and forward reference record
\ chain of local label record n1.
\ **********************************************************************

: .lab          ( n1 -- )               \ display a local label record
                [ forth ]
                llab>line 2>r
                cr ." Address  = " 2r@ >lladr @L h.
                2r> >llptr c@L
                begin   cr ." Ref rec  = " dup h.
                        dup 255 <>
                        ?keypause
                while   lref>line 2>r
                        ."  at target = " 2r@ >taddr @L h.
                        ."  of type   = " 2r@ >rtype c@L h.
                        ."  links to  = " 2r> >rlink c@L dup h.
                repeat  drop ;


