\ COLORIZE.SEQ      Color Words                 by Leon Dent

comment:

        This is a modified version of the CWORDFPC from Leon Dent.  I
        have modified it after changing the F-PC kernel to allow this
        to be easily installed.

                                mods by Tom Zimmer  10/31/88

comment;

ONLY FORTH ALSO HIDDEN DEFINITIONS ALSO

LTMAGENTA VALUE COLONCOLOR        \  COLON DEFINITIONS
BROWN     VALUE CONSTANTCOLOR     \  CONSTANTS
YELLOW    VALUE VALUECOLOR        \  VALUES
GREEN     VALUE VARIABLECOLOR     \  VARIABLES
LTCYAN    VALUE DEFERCOLOR        \  DEFERED WORDS
LTBLUE    VALUE CODECOLOR         \  CODE WORDS
LTRED     VALUE DOESCOLOR         \  CREATE DOES> WORDS
BLUE      VALUE USERBGCOLOR       \  BACKGROUND FOR USER WORDS

: CLR-COLON      COLONCOLOR     >FG >NORMBG  ;
: CLR-CONSTANT   CONSTANTCOLOR  >FG >NORMBG  ;
: CLR-VALUE      VALUECOLOR     >FG >NORMBG  ;
: CLR-VARIABLE   VARIABLECOLOR  >FG >NORMBG  ;
: CLR-UVARIABLE  VARIABLECOLOR  >FG USERBGCOLOR >BG ;
: CLR-DEFER      DEFERCOLOR     >FG >NORMBG  ;
: CLR-UDEFER     DEFERCOLOR     >FG USERBGCOLOR >BG ;
: CLR-OTHER      DUP C@ 232 <>
                 IF   CODECOLOR >FG >NORMBG
                 ELSE DUP DOES? NIP      \ DROP SECOND ELEM ON STK
                      IF  DOESCOLOR >FG >NORMBG
                      ELSE >NORM  THEN
                 THEN ;

\ end of color mods

\ TINT2.SEQ      Last part of Color Words   by Leon Dent

\ COLOR NAMES
\ BLACK  BLUE  GREEN  CYAN  RED  MAGENTA BROWN  LTGRAY

\ All following, blink in Background.
\ DKGRAY  LTBLUE  LTGREEN  LTCYAN  LTRED LTMAGENTA  YELLOW   WHITE

: COLOR-CLASS  0MAX MAX-CLASSES MIN EXEC:
   ( 0 )     CLR-COLON           ( 1 )     CLR-CONSTANT
   ( 2 )     CLR-VARIABLE        ( 3 )     CLR-UVARIABLE
   ( 4 )     CLR-DEFER           ( 5 )     CLR-UDEFER
   ( 6 )     CLR-VALUE           ( 7 )     CLR-OTHER   ;

FORTH DEFINITIONS

FALSE VALUE ?COLORIZE

: COLOR-INFO    ( --- )
                vmode-var @ 7 = ?exit   \ leave if monocrome
                ?colorize 0=
                if      cr ."  Type COLORIZEON to see words in COLOR" exit
                then
                attrib c@ >r cr
                clr-colon     ."  COLON "
                clr-constant  ."  CONSTANT "
                clr-value     ."  VALUE "
                clr-variable  ."  VARIABLE "
                clr-uvariable ."  USER-VARIABLE "
                cr
                clr-defer     ."  DEFERED "
                clr-udefer    ."  USER-DEFERED "
                codecolor >fg
                >NORMBG       ."  CODE "
                doescolor >fg
                >NORMBG       ."  CREATE-DOES " >norm
                              ."  OTHER "
                cr
                ."  Each word type is displayed in color as shown above."
                r> attrib c! cr ;

' COLOR-INFO IS PREWORDS

: COLORIZE  ( CFA -- )
            DUP  @REL>ABS  DEFINITION-CLASS COLOR-CLASS DROP  ;

                                        \ Boot time initialization of
: COLORIZE-INIT ( --- )                 \ COLORIZER by video mode
                DEFERS INITSTUFF
                vmode-var @ 7 <> =: ?COLORIZE ;

' COLORIZE-INIT IS INITSTUFF

: C.ID          ( NFA --- )
                ?COLORIZE
                IF      SAVE> ATTRIB
                        DUP NAME> COLORIZE %.ID
                        RESTORE> ATTRIB
                ELSE    %.ID
                THEN    ;

' C.ID IS .ID

: COLORIZEOFF   ( --- )
                FALSE =: ?COLORIZE ;

: COLORIZEON    ( --- )
                vmode-var @ 7 <>
                IF      ['] C.ID IS .ID
                        TRUE =: ?COLORIZE
                ELSE    COLORIZEOFF
                THEN    ;

ONLY FORTH ALSO DEFINITIONS

