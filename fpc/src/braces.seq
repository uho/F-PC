\ BRACE.SEQ     Braces used to enclose source code        by Glen B. Haydon

: <}>           ( --- )
                loading @ 0=
                if      crunsave is run
                        true abort" } missing a { while loading"
                then
                begin   bl word dup c@ 0= >r 1+ " {" comp 0= dup
                        if      crunsave is run
                                >r crunsave execute r>
                        then    r> or
                until   ;

: }             ( --- )         \ Skip all text between } and { .
                ?loading
                @> run !> crunsave
                ['] <}> is run <}> ; immediate

                        \ new char to start F-PC source files with by T.J.Z
' }    alias \\
                        \ make this a dummy word that does nothing
' noop alias {

}            NEW STYLE SOURCE CODE FOLLOWS
                  by Glen B. Haydon

  The code at the top allows the use of this style for the rest
of my source. You will note that I am starting the title on line 11
with the close brace. Thence through the source the implementation
will be put in braces and the rest of the source is narative such
as this.

             Additional comments by Tom Zimmer

  Files that contain source code, and normal forth type imbeded comments
will work as before. You can use "\" or "comment:" and "comment;" to create
comments as if nothing has changed.

  Files that start out with "\\", rather than the normal "\" in the first
line, specify files that are going to contain comments except for those
lines contained in "{" and "}" which will be treated as Forth source.

Examples:
\ ***************************************************************************

\ NORMAL.SEQ       This file starts out as a normal Forth source file.

\ Normal Forth source code follows


\ ***************************************************************************

\\ TEXTFILE.SEQ    This file contains comments except for { } notation.

This is a file containing comments and documentation except as folows

{

\ Normal Forth source code is placed here

}

  More comments to end of file, automatically drops out of comment mode
at the end of the file.

