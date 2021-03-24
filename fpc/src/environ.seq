\ ENVIRON.SEQ   Environment manipulation words          by Tom Zimmer

: evseg         ( --- n1 )      \ Return the segment of environment $.
                44 @ ;

: envsize       ( --- n1 )      \ Calculate the environment $ size.
                ?cs: evseg - 2047 min 16 * ;

: "envfind      ( a1 n1 --- n2 bool )   \ n2 is offset into environment
                false save!> caps       \  where string a1,n1 was found.
                evseg save!> SSEG       \ Set the search segment
                0 envsize search
                restore> SSEG           \ Restore the search segment
                restore> caps ;

: .env          ( --- )         \ print the environment string
                0 envsize bounds    cr
               ?do      evseg i c@l 0=
                        if      cr
                        else    evseg i c@l emit
                        then    evseg i @l 0= ?leave
                loop    ;

handle comspec$

: comspec@      ( --- )         \ extract the command spec
                " COMSPEC=" "envfind 0=
                if      ." Couldn't find Command Spec."
                        drop comspec$ off
                else    8 + envsize swap
                        comspec$ dup clr-hcb >nam -rot
                        do      evseg i c@l 0= ?leave
                                evseg i c@l over c! 1+
                                comspec$ c@ 1+ comspec$ c!
                        loop    drop
                then    ;

: .comspec      ( --- ) comspec@ comspec$ count type ;

create path$ 81 allot

: path@         ( --- )         \ extract the command spec
                " PATH=" "envfind 0=
                if      ." Couldn't find PATH."
                        drop path$ off
                else    5 + envsize swap
                        path$ dup clr-hcb >nam -rot
                        do      evseg i c@l 0= ?leave
                                evseg i c@l over c! 1+
                                1 path$ c+!
                        loop    drop
                then    ;

: .path         ( --- ) path@ path$ count type ;

handle me$

: me@           ( --- ) \ extract my own execution name string
                        \ returns a null ME$ if it fails
                me$ off dosver 3 >=     \ need DOS version 3 or greater
                if      me$ 2 "envfind
                        if      4 + envsize swap
                                me$ dup clr-hcb >nam -rot
                                do      evseg i c@l 0= ?leave
                                        evseg i c@l over c! 1+
                                        me$ c@ 1+ me$ c!
                                loop    drop
                        else    drop
                        then
                then            ;

: .me           ( --- ) me@ me$ count type ;

comment:

envsize       ( --- n1 )
        Return the maximum size the environment can be in bytes.

.env          ( --- )
        Print the environment string used by the system.

"envfind      ( a1 n1 --- n2 bool )
        Find the string a1 n1 in the environment, returning
        bool true if found, and n2 the offset into env$ where it
        was found. N2 is the offset to the BEGINNING of the
        string searched for.

comspec$
        Storage space for the command spec string.

comspec@      ( --- )
        Extract the command spec from the environment string.

.comspec      ( --- )
        Print the command spec.

me$
        Storage space for the execution string used to execute
        this forth currently running.

me@           ( --- )
        Extract the execution string from the environment, and
        place it in the string ME$

.me           ( --- )
        Print the execution string after extracting it.

comment;


