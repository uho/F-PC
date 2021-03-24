\\ LISTED.SEQ   An execution list structure as suggested by J”rg Staben

  This file implements a list execution structure.  J”rg sent me a file
that implements this same functionality, but in his design, the first
function added to the list gets placed at the end of the list, so you
have to traverse the list backwards to execute it. He solve this problem
by using recursion, that is recursing down the list till the end is
reached, then executing each function as he falls out of the list.  While
this is an interesting method of solving the tail first problem, I felt
it would be better to add new functions to the end of the list, and
execute the list functions with iteration until the list end if reached.

  He actually implemented the list structure to solve a precieved
problem. I use the DEFERred word chain concept to create lists in F-PC.
While this is a simple way to create a list, it is much less flexible
than J”rg's proposed list function.  J”rg however missed one capability
available in the DEFERed word chain, which is the ability to either place
a new function at the end of the chain, or at the head.  While this is
not the usual way chains are built, I have had reasons to insert
functions into the head of a DEFERed word chain.  So, I have added an
additional function to his list, FIRST:, which is like ADD:, but prepends
a new function rather than appending a function.

{

: list:         ( -<name>- )    \ compile time: define a new list head name
                ( -- lista )    \ runtime:      return list head variable
                create 0 , ;            \ define new list as empty

: >listend      ( lista -- a2 )         \ travers to end of lista
                begin   dup @ ?dup
                while   nip
                repeat  ;

: first:        ( lista -<name>- )      \ insert name into beginning of lista
                ' swap dup @ here rot ! , , ;

: add:          ( lista -<name>- )       \ add name to lista
                >listend first: ;

: kill:         ( lista -<name>- )      \ remove name from lista
                ' >r
                begin   ?dup
                while   dup @ 2+ @ r@ =       \ if CFA matches
                        if      dup @ @ over !
                        else    @
                        then
                repeat  r>drop ;

: show:         ( lista -- )            \ display a lista
                @
                begin ?dup
                while   dup 2+ @ >name .id @
                repeat  ;

: do:           ( lista -- )            \ perform the lista
                @
                begin ?dup
                while   dup 2+ perform @
                repeat  ;

\s      ****** Some example code to demonstrate how lists work ******

: george ." george " ;          \ some dummy words to show how lists work
: bobby  ." bobby  " ;
: bingo  ." bingo  " ;
: oingo  ." oingo  " ;
: boingo ." boingo " ;

list: billy                     \ define a list called BILLY

billy add: george               \ add a few words to the end of list BILLY
billy add: bobby
billy add: bingo
billy first: oingo              \ insert OINGO into the head of BILLY
billy add: boingo               \ append BOINGO to the end of BILLY

cr billy show:                  \ show what is in the list
cr billy do:                    \ execute the list

billy kill: bingo               \ remove BINDO from the list
cr billy show:                  \ show results

billy first: bingo              \ but BINTO back at the head of the list
cr billy show:                  \ show the results

billy add: bobby                \ add multiple BOBBYs to the end of the list
billy add: bobby
billy add: bobby
cr billy show:                  \ show the new list

billy kill: bobby               \ remove ALL copies of BOBBY from the list
cr billy show:                  \ show the results

