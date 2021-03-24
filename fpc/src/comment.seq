\ COMMENT.SEQ   Allow multiple line comments in files.  by Tom Zimmer

' <run> value crunsave

: ?exec         ( --- )
                state @ abort" Can not be used while Compiling!" ;

: ?loading      ( --- )
                loading @ 0=
                if      crunsave is run
                        true abort" Use ONLY while loading"
                then    ;

variable comment$

: <comment:>    ( --- )
                loading @ 0=
                if      crunsave is run
                        true abort" COMMENT: missing a COMMENT; while loading"
                then
                bl word                                 \ get a word
                1+ comment$ @ count caps-comp 0=        \ terminating string?
                if      crunsave is run                \ then back to <RUN>
                        crunsave execute
                then    [compile] \ ;

: comment:      ( --- )         \ ignore all lines between COMMENT: & COMMENT;
                ?loading
                [compile] \
                " comment;" ">$ comment$ !
                @> run !> crunsave
                ['] <comment:> is run ;

: /*            ( --- )         \ skip all lines between /* and */
                ?loading
                [compile] \
                " */" ">$ comment$ !
                @> run !> crunsave
                ['] <comment:> is run ;

: <.comment:>   ( --- )
                loading @ 0=
                if      crunsave is run
                        true abort" .COMMENT: missing a COMMENT; while loading"
                then
                bl word 1+ " comment;" caps-comp 0=
                if      crunsave is run
                else    key? 0=
                        if      cr tib #tib @ 78 min type
                        then
                then    [compile] \ ;

: .comment:     ( --- )         \ ignore all lines between.
                ?loading
                [compile] \
                @> run !> crunsave
                ['] <.comment:> is run ;

comment:        <- this marks the start line of a multi line comment.

        this is a comment line somewhere in the middle.

         /-------- this marks the end line of a multi line comment.
comment;

: <#if>         ( --- )         \ ignore lines unless #else, or #endif
                loading @ 0=
                if      crunsave is run
                        true abort" #IF missing a #THEN while loading"
                then
                begin   bl word dup c@ 0= >r 1+
                        dup  " \ "     caps-comp 0= if #tib @ >in ! then
                        dup  " #endif" caps-comp 0=
                        over " #then"  caps-comp 0= or
                        swap " #else"  caps-comp 0= or dup
                        if      crunsave is run
                                >r crunsave execute r>
                        then    r> or
                until   ;

: #else         ( --- )
                ?loading                        \ you must be loading
                ?exec                           \ but NOT compiling
                @> run !> crunsave              \ Save previous value of RUN.
                ['] <#if> is run                \ Ignore following lines
                <#if> ;        immediate        \   and rest of this line.

: #if           ( f1 --- )      \ ignore lines following if false
                ?loading
                ?exec
                0=
                if      @> run !> crunsave      \ Save previous value of RUN.
                        ['] <#if> is run        \ Ignore following lines
                        <#if>                   \   and rest of this line.
                then    ;       immediate

: #endif        ?loading ; immediate    \ just a noop for the #if TRUE case.
: #then         ?loading ; immediate

\s      ************* NOT LOADING BEYOND THIS LINE ***********

        \ Here is an example of how the interpreted compiler directives
        \ work

cr cr .( press a T for true ) key bl or 116 =   \ wait for a key

#if .( TRUE)  #else  .( FALSE)  #then .(  always )

