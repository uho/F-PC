\ CODEHIGH.SEQ  Call high level code from assembly & return

comment:

  Allow calling high level words from within a CODE definition.

  WARNING ! You must not assume your registers are preserved across a
group of high level words within the CODE definition. They ARE NOT !
If you need to preserve some registers, then doit yourself.

comment;

only forth also assembler definitions also

label hdoes     ( --- )
                pop ax          \  8 cycles
                xchg rp, sp     \  4 cycles
                push ax         \ 11 cycles
                xchg rp, sp     \  4 cycles
                sub ax, # 3     \  4 cycles
                jmp >nest       \ 16 cycles
                end-code

code hret       ( --- )
                xchg rp, sp     \ 4 cycles
                pop ip          \ 8 cycles
                pop es          \ 8 cycles
                pop ax          \ 8 cycles
                xchg rp, sp     \ 4 cycles
                add ax, # 2     \ 5 cycles
                jmp ax
                end-code

: >h            ( --- )
                xhere paragraph + dup xdpseg !          \ setup LIST space
                xdp off
                also forth
                >pre call hdoes a; xseg @ - , pre> ] ;

: h>            ( --- )
                previous
                compile hret  [compile] [ ; immediate

\s

Example         A simple example of the use of High level in a code word.

        CODE TST
                MOV AX, # 23
                PUSH AX
                >H DUP . H>     \ you MUST come back with "H>".
                POP AX
                NEXT
                END-CODE

