\ SPREAD.SEQ            A Forth Spreadsheet             by Craig A. Lindley

comment:

  This spreadsheet was published in FORTH Dimensions Volume 7, Number 1
(May/June) and 2 (July/August 1985).  It was written by Craig A. Lindley
of Manitou Springs, Colorado.

  Originally written for F83, it runs with minimal modifications for F-PC.
Changes are flaged with "**** change ****"

  Typed-in and modified for F-PC by Tom Zimmer. 05/17/89

  Most but not all comments from the original listing have been typed into
this listing, so please refer to the original article for further
information.

comment;


anew spreadsheet_program


\ screen 1      spreadsheet -

cr .( Spreadsheet Compiling)


warning off


\ screen2       spreadsheet - case statment

\ This screen contained a CASE statment, which is already defined in F-PC.



\ screen 3      spreadsheet - constants

26 constant row_max             \ number of spreadsheet rows
26 constant col_max             \ number of spreadsheet columns
12 constant col_name_len        \ maximum length of column name
17 constant row_name_len        \ maximum length of row    name
21 constant col_org             \ column origin of data on display
 3 constant row_org             \ row    origin of data on display
 6 constant bytes/cell          \ number of bytes per cell









\ screen 4      spreadsheet - variables

variable mode_flag              \ auto calculate flag
variable order_flag             \ calculation order flag
variable format_flag            \ numeric output format flag
variable cur_col                \ top left display column number
variable cur_row                \ top left display row    number
variable col_disp               \ column displacement from CUR_COL on display
variable row_disp               \ row    displacement from CUR_ROW on display
variable dict_mark              \ beginning of formula area
variable op_stack 44 allot      \ operator stack for algebraic equation
                                \ compilation


\ screen 5      spreadsheet - high level array definitions

\ create 2D array depth bytes deep
: array         ( #rows #cols depth -- )        \ compile time
                ( row# col# -- element_addr )   \ run     time
                create 2dup swap c, c, * * dup here
                swap erase allot
                does> dup c@ 3 roll * 2 roll + over
                        1+ c@ * + 2+ ;

\ create 1D string array depth characters deep
: $array        ( #rows depth -- )              \ compile time
                ( row# -- string_addr )         \ run     time
                create dup c, * dup here swap blank allot
                does>  dup c@ rot * + 1+ ;


\ screen 6      spreadsheet - array definitions

\ define a 2D array for spreadsheet data structure
\ each cell contains 6 bytes
\ 2 for formula execution address (if any)
\ 4 for double number value storage

row_max col_max bytes/cell array cells

\ define a string array for holding the row names
row_max row_name_len $array row_names

\ define a string array for holding the column names
col_max col_name_len $array col_names










\ screen 7      spreadsheet - misc word definitions

\ "IBM_key" was defined here but is not needed.

: d#in          ( -- d1 )               \ input double number from keyboard
                pad 1+ 20 2dup blank expect
                span @ pad c! pad number ;

: #in           ( -- n1 )               \ input single number from keyboard
                d#in drop ;


\ screen 8      spreadsheet - misc word definitions

: pos1          ( -- )                  \ position cursor on command line one
                0 21 2dup at eeol at ;  \ and erase line

: pos2          ( -- )                  \ position cursor on command line two
                0 22 2dup at eeol at ;  \ and erase line

: y/n           ( -- bool )             \ bool = TRUE if yes, FALSE if no
                pos1 ." Are You Sure ?: "       \ display message
                key upc ascii Y = ;             \ return flag

: mark_cell     ( row# col# -- )        \ make a cell on the display
                2dup at ascii < emit swap 11 + swap at ascii > emit ;

: unmark_cell   ( row# col# -- )        \ unmark a cell on the display
                2dup at space swap 11 + swap at space ;


\ screen 9      spreadsheet - misc word definitions

: cell_ptr      ( -- cell_addr )        \ return the address of the cell
                cur_row @ row_disp @ +  \ pointed at by <   > display marker
                cur_col @ col_disp @ +
                cells ;

: cal_cell_disp_loc     ( -- col row )  \ calculate location on display of
                col_disp @ 13 * col_org +       \ cell display markers
                row_disp @      row_org + ;

: place_cell_marker     ( -- )          \ place cell markers around cell
                cal_cell_disp_loc mark_cell ;

: erase_cell_marker     ( -- )          \ erase cell markers from around cell
                cal_cell_disp_loc unmark_cell ;








\ screen 10     spreadsheet - misc word definitions

: (fd.)         ( d1 -- a1 n1 )         \ dollar/cents formating word,
                tuck dabs               \ formats d1 and includes leading "$"
                <# # # ascii . hold #s rot
                sign ascii $ hold #> ;

: fd.r          ( d1 width -- )         \ format d1 in dollars/cents in a
                                        \ right justified field of width
                >r (fd.) r> over - spaces type ;

: format#       ( d1 -- )               \ format d1 in one of two formats
                format_flag @           \ as dollars/cents or a normal number
                if      10 fd.r
                else    10  d.r
                then    ;


\ screen 11     spreadsheet - display word definitions

: dis_data      ( -- )                  \ display all cell data for 4 columns
                cur_col @ dup 4 + swap
                do      i col_max = ?leave
                        cur_row @ dup 15 + swap              \ and 15 rows
                        do      i row_max = ?leave
                                j cur_col @ - 13 * 22 +
                                i cur_row @ -       3 + at
                                i j cells 2+ 2@ format#
                        loop
                loop    ;

\ screen 12     spreadsheet - display word definitions

\ display the spreadsheet border on the screen

: dis_border    ( -- )                  \ display spreadsheet border
                18 3
                do      20 i at 4 0
                        do      ." ³" 12 spaces
                        loop    ." ³"
                loop
                80 0
                do      i 2 at ascii - emit loop
                80 0
                do      i 18 at ascii - emit loop ;










\ screen 13     spreadsheet - display word definitions

\ display the spreadsheet menu of options on the right side of display

: dis_menu      ( -- )                  \ display menu
                74  3 at ." Menu:"
                74  4 at ." C)ol"       74  5 at ." A)gain"
                74  6 at ." D)ata"      74  7 at ." E)qu."
                74  8 at ." F)orm"      74  9 at ." G)oto"
                74 10 at ." M)ode"      74 11 at ." N)ew"
                74 12 at ." O)rder"     74 13 at ." P)ref"
                74 14 at ." Q)uit"      74 15 at ." R)ow" ;


\ screen 14     spreadsheet - display word definitions

: dis_row_labels        ( -- )          \ label the rows on display
                cur_row @ dup 15 + swap
                do      i row_max = ?leave
                        18 i cur_row @ - 3 + at
                        i 2 .r
                loop    ;

: dis_row_names         ( -- )          \ display row names from array
                cur_row @ dup 15 + swap
                do      i row_max = ?leave
                        0 i cur_row @ - 3 + at
                        i row_names row_name_len type
                loop    ;

\ screen 15     spreadsheet - display word definitions

: dis_col_labels        ( -- )          \ label the columns on display
                        cur_col @ dup 4 + swap
                        do      i col_max = ?leave
                                i cur_col @ - 13 * 27 + 2 at
                                i ascii A + emit
                        loop    ;

: dis_col_names         ( -- )          \ display column names
                        7 1 at ." Title"
                        cur_col @ dup 4 + swap
                        do      i col_max = ?leave
                                i cur_col @ - 13 * 21 + 1 at
                                i col_names col_name_len type
                        loop    ;









\ screen 16     spreadsheet - display word definitions

: dis_status    ( -- )                  \ display spreadsheet status
                48 19 at ." Row: "
                cur_row @ row_disp @ + .
                60 19 at ." Column: "
                cur_col @ col_disp @ + ascii A + emit
                47 20 at ." Mode: " mode_flag @
                if      ." Auto  " else ." Normal" then
                61 20 at ." Order: " order_flag @
                if      ." C/R" else ." R/C" then
                pos2
                pos1 ." Command: "
                place_cell_marker ;


\ screen 17     spreadsheet - display word definitions

: dis_row_change        ( -- )          \ display info that changes with
                dis_row_names           \ a row change
                dis_row_labels          \ col names, labels and data
                dis_data ;

: dis_col_change        ( -- )          \ display info chat changes with
                dis_col_names           \ a col change
                dis_col_labels          \ col names, labels and data
                dis_data ;

\ screen 18     spreadsheet - display word definitions

: dis_screen    ( -- )                  \ display entire spreadsheet screen
                dark 31 0 at
                ." Forth Spreadsheet"   \ display title
                dis_border              \ draw borders
                dis_menu                \ display operation menu
                dis_col_labels          \ label columns (A-Z)
                dis_col_names           \ display column names
                dis_row_labels          \ label rows    (0-25)
                dis_row_names           \ display row    names
                dis_data                \ display appropriate data
                                        \ for data window being displayed
                0 row_disp !            \ set mark to origin
                0 col_disp !
                dis_status ;            \ display status











\ screen 19     spreadsheet - cell calculation words

: calculate     ( cell_addr -- )        \ calc formula of cell if it has one
                @ ?dup if execute then ;

: calc_c/r      ( -- )                  \ calculate columns then rows
                row_max 0
                do      col_max 0
                        do      j i cells calculate
                        loop
                loop    ;

: calc_r/c      ( -- )                  \ calculate rows then columns
                col_max 0
                do      row_max 0
                        do      i j cells calculate
                        loop
                loop    ;


\ screen 20     spreadsheet - cell calculation words

: calc_cells    ( -- )                  \ calculate according to previously
                order_flag @            \ specified order
                if      calc_c/r
                else    calc_r/c
                then    ;

: order         ( -- )                  \ prompt user for calculation order
                pos1 ." Specify calculation order"
                pos2 ." Row/Col(0) or Col/Row(1): "
                key ascii 1 =
                if      true
                else    false
                then    order_flag ! ;

\ screen 21     spreadsheet - cell marker positioning words

: left_arrow    ( -- )                  \ move cell marker left one cell
                col_disp @ 0=
                if      cur_col @ 0<>
                        if      -1 cur_col +!
                                dis_col_change
                        then
                else    erase_cell_marker
                        -1 col_disp +!
                then    place_cell_marker ;








\ screen 22     spreadsheet - cellmarker positioning words

: right_arrow   ( -- )                  \ move cell marker right one cell
                col_disp @ 3 =
                if      cur_col @ 4 + col_max <>
                        if      1 cur_col +!
                                dis_col_change
                        then
                else    erase_cell_marker
                        1 col_disp +!
                then    place_cell_marker ;

\ screen 23     spreadsheet - cell marker positioning words

: up_arrow      ( -- )                  \ move cell marker up one cell
                row_disp @ 0=
                if      cur_row @ 0<>
                        if      -1 cur_row +!
                                dis_row_change
                        then
                else    erase_cell_marker
                        -1 row_disp +!
                then    place_cell_marker ;


\ screen 24     spreadsheet - cell marker positioning words

: down_arrow    ( -- )                  \ move cell marker down one cell
                row_disp @ 14 =
                if      cur_row @ 15 + row_max <>
                        if      1 cur_row +!
                                dis_row_change
                        then
                else    erase_cell_marker
                        1 row_disp +!
                then    place_cell_marker ;



















\ screen 25     spreadsheet - cell marker positioning words

: first_col     ( -- )                  \ go to column A immediately
                0 cur_col !
                dis_col_change ;

: last_col      ( -- )                  \ go to column W immediately
                col_max 4 - cur_col !
                dis_col_change ;

: top_row       ( -- )                  \ go to row 0 immediately
                0 cur_row !
                dis_row_change ;

: bottom_row    ( -- )                  \ go to row 11 immediately
                row_max 15 - cur_row !
                dis_row_change ;

\ screen 26     spreadsheet - cell marker positioning words

: left_4_cols   ( -- )                  \ move marker left four columns
                4 0 do left_arrow loop ;

: right_4_cols  ( -- )                  \ move marker right four columns
                4 0 do right_arrow loop ;


\ screen 27     spreadsheet - algebraic functions

vocabulary algebra

algebra also definitions

\ The COL_ID function assigns n1 to <name> at compile time.
\ It expects row number as n1 at run time.
\ Subsequent usage of <name> fetches the double value of the cell
\ to the stack.

: col_id                                \ column_id high level defining word
                ( n1 | <name> -- )      \ compile time
                ( n1 --- cell_value )   \ run     time
                create ,
                does> @ cells 2+ 2@ ;












\ screen 28     spreadsheet - algebraic functions

: assign_id     ( | <many_names> -- )   \ loop used to assign value to
                col_max 0               \ the alphabetic columns
                do      i col_id
                loop    ;

assign_id  A B C D E F G H I J K L M N O P Q R S T U V W X Y Z

\ for example 1 A returns teh couble int value of cell 1 A.
\ Column ids A-Z return values of 0-25 respectively


\ screen 29     spreadsheet - algebraic functions

: opp@          ( -- addr )             \ return operand stack position
                op_stack dup @ + ;      \ first location is stack pointer

: >op           ( cfa prec -- )         \ store CFA and precedence to
                4 op_stack +! opp@ 2! ; \ top of operand stack

: op>           ( -- )                  \ pop CFA and precedence off operand
                opp@ 2@                 \ stack and compile into dictionary
                -4 op_stack +!
                drop x, ;               \ *** changed *** "," to "x,"

: prec?         ( -- prec )             \ return precedence from top
                opp@ @ ;                \ of operand stack

: ]a            ( -- )                  \ end algebraic compilation
                begin   prec?           \ pop remaining operands off stack
                while   op>             \ and compile then select FORTH voc.
                repeat  forth ; immediate


\ screen 30     spreadsheet - algebraic functions

\ create a high level definition that performs algebraic compilation.
\ See article for details of operation.

: infix         ( n1 | <name> <name2> -- )      \ create a new algebraic
                ' create swap , , immediate     \ operator
                does> 2@
                begin   dup prec? > not
                while   >r >r op> r> r>
                repeat  >op ;

: d*            ( d1 d2 -- d3 )         \ double multiplication
                dup rot * rot rot um* rot + ;

: d/            ( d1 d2 -- d3 )         \ double division
                swap over /mod >r swap um/mod swap drop r> ;

: dmod          ( d1 d2 -- d3 )         \ double modulus
                d/ drop 0 ;

\ screen 31     spreadsheet - algebraic functions

\ create the new algebraic operators with assigned precedence

7 infix d* *
7 infix d/ /
6 infix d+ +
6 infix d- -
5 infix dmod mod

: )missing      ( -- )                  \ missing ")" error handler
                true abort" Missing )" ;

: (             ( -- )                  \ prec = 1 cfa=)missing message
                                        \ lowest precedence, push on
                                        \ operand stack.
                ['] )missing 1 >op ; immediate


\ screen 32     spreadsheet - algebraic functions

: )             \ ( -- )                \ right paren causes all items on
                [ forth ]               \ operand stack to be compiled
                begin   1 prec? <       \ until a left paren is found
                while   op>
                repeat                  \ left paren should have a precedence
                1 prec? =               \ of one or error message results
                if      -4 op_stack +!
                else    true abort" Missing ("
                then    ; immediate

forth definitions

: a[            ( -- )                  \ start algebraic compilation
                0 op_stack ! algebra ;  \ reset operand stack and select
                immediate               \ algebra vocabulary

\ screen 33     spreadsheet - input words

: input_row_names       ( -- )          \ input row names
                pos1 ." Input Row Names"
                pos2 ." Starting with Row: "
                #in cur_row !
                row_max cur_row @
                do      pos2 ." Row " i 2 .r ." : "
                        i row_names row_name_len
                        2dup blank expect
                        span @ 0= ?leave
                        i 5 mod 0=
                        if      i cur_row !
                                dis_row_change
                        else    dis_row_names
                        then
                loop ;


\ screen 34     spreadsheet - input words

: input_col_names       ( -- )          \ input column names
                pos1 ." Input Col Names"
                pos2 ." Starting with Col: "
                key upc ascii A - cur_col !
                col_max cur_col @
                do      pos2 ." Col " i ascii A + emit ." : "
                        i col_names col_name_len
                        2dup blank expect
                        span @ 0= ?leave
                        i 4 mod 0=
                        if      i cur_col !
                                dis_col_change
                        else    dis_col_names
                        then
                loop ;


\ screen 35     spreadsheet - input words

: get#          ( -- n1 )               \ get a number, scale if needed
                d#in
                format_flag @
                if      dpl @ 3 min
                        case
                                -1 of 100 d*    endof
                                 0 of 100 d*    endof
                                 1 of  10 d*    endof
                                 2 of noop      endof
                                 3 of 10 d/     endof
                                drop
                        endcase
                then    ;

\ screen 36     spreadsheet - input words

: input_cell_data       ( -- )          \ input data to cells
                pos1 ." Input Cell Data"
                pos2 ." Data: " get#
                cell_ptr 2+ 2!
                mode_flag @
                if      pos2 ." Calculating"
                        calc_cells
                then    dis_data ;









\ screen 37     spreadsheet - input words

: input_equ     ( -- )                  \ input equations into dictionary
                pos1 ." Input Cell Equation"
                pos2 ." Eqquation: "
                tib 127 blank
                " : formula a[ " tib swap cmove
                tib 13 + dup 127 expect
                span @ +
                "  ]a [ cell_ptr 2+ ] literal 2! ; last @ name> cell_ptr !"
                -rot swap rot cmove
                span @ 70 + #tib !
                >in off
                algebra
                interpret
                forth ;


\ screen 38     spreadsheet - high level commands

: quit_calc     ( -- )                  \ exit spreadsheet
                y/n abort" BYE" ;

: new           ( -- )                  \ clear existing spreadsheet
                y/n
                if      0 0 cells
                        row_max col_max bytes/cell * * erase
                        0 row_names row_max row_name_len * erase
                        0 col_names col_max col_name_len * erase
                        dict_mark perform
                        0 row_disp !
                        0 col_disp !
                        dis_screen
                then    ;


\ screen 39     spreadsheet - high level commands

: mode          ( -- )                  \ set the auto calculation mode
                pos1 ." Set auto calculation mode"
                pos2 ." Normal=0 or Auto=1: "
                key ascii 1 =
                if      true
                else    false
                then    mode_flag ! ;

: perform_calc  ( -- )                  \ force a re-calculation of
                calc_cells              \ entire spreadsheet
                dis_data ;






\ screen 40     spreadsheet - high level commands

: format        ( -- )                  \ select the number display format
                pos1 ." Select input number format"
                pos2 ." Normal=0 or Dollars/Cents=1: "
                key ascii 1 =
                if      true
                else    false
                then    format_flag !
                dis_data ;


\ screen 41     spreadsheet - high level commands

: again_repl    ( -- )                  \ replicate column data
                cell_ptr 2+ 2@
                pos1 ." Column replicate cell data"
                pos2 ." Number of columns: "
                #in ?dup
                if      0
                        do      right_arrow
                                2dup cell_ptr 2+ 2!
                        loop
                        2drop
                        dis_data
                then    ;


\ screen 42     spreadsheet - high levle commands

: go_to         ( -- )                  \ goto a specified row/col
                pos1 ." Row(0-25): "
                #in dup 0 row_max within
                if      cur_row !
                        pos2 ." Column(A-W): "
                        key upc ascii A - dup
                        0 col_max 3 - within
                        if      cur_col !
                                0 col_disp !
                                0 row_disp !
                                dis_screen
                        else    drop
                        then
                else    drop
                then    ;










\ screen 43     spreadsheet - operator input processing

: command_in    ( c1 -- )               \ do a user command
                upc             \ *** changed ***
                                \ UPC moved here from screen 45
                                \ for compatibility with F-PC
                case
                        ascii A of again_repl           endof
                        ascii C of input_col_names      endof
                        ascii D of input_cell_data      endof
                        ascii E of input_equ            endof
                        ascii F of format               endof
                        ascii G of go_to                endof
                        ascii M of mode                 endof
                        ascii N of new                  endof
                        ascii O of order                endof
                        ascii P of perform_calc         endof
                        ascii Q of quit_calc            endof
                        ascii R of input_row_names      endof
                                   drop beep
                endcase ;


\ screen 44     spreadsheet - operator input processing

: control_in    ( c1 -- )               \ do a user movement command
                case
                        199 ( Home )    of top_row          endof
                        200 ( Up )      of up_arrow         endof
                        201 ( PgUp )    of left_4_cols      endof
                        203 ( Left )    of left_arrow       endof
                        205 ( Right )   of right_arrow      endof
                        207 ( End )     of bottom_row       endof
                        208 ( Down )    of down_arrow       endof
                        209 ( PgDn )    of right_4_cols     endof
                        243 ( ^Left )   of first_col        endof
                        244 ( ^Right )  of last_col         endof
                            ( any other )  drop beep
                endcase ;















\ screen 45     spreadsheet - main program

: spreadsheet   ( -- )                  \ main program word
                dis_screen
                begin   key             \ *** changed ***
                                        \ switched to F-PC's key, moved UPC
                                        \ to screen 43
                        dup 198 >
                        if      control_in
                        else    command_in
                        then
                        dis_status
                again   ;

mark no_formulas                        \ dict_mark has CFA of word to
' no_formulas dict_mark !               \ delete formulas

warning on                              \ turn warning messages back on

cr .( Spreadsheet Compile complete. )

