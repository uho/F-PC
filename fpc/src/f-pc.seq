\ F-PC.SEQ       Extend file for KERNEL.COM

CFGHNDL !HCB F-PC.CFG           \ Change the configuration file name

FLOAD TIMER.SEQ                 \ Timing and measurment words.
FLOAD TIMESTUF.SEQ              \ More timing words

0COMPILER                       \ reset the compiled line counter

WARNING OFF                     \ Don't warn me about any re-definitions.

.( Loading the extensions to KERNEL.COM, with all HEADERS PRESENT. ) CR

\ What follows is the file load commands required to add all of the
\ extensions to KERNEL.COM to make F-PC.EXE. Some of these files are not
\ required, and may be commented out. These files are marked at the right
\ edge of the column for easy identification.

FLOAD COMMENT.SEQ  .( .)   \ Allows multi-line comments in source.
FLOAD UTILS.SEQ    .( .)   \ Some low level utilities.
FLOAD UNHEAD.SEQ   .( .)   \ A NEW utility to remove some heads from F-PC

HWORDS-                    \ DON'T Throw away heads

FLOAD BRACES.SEQ   .( .)   \ Glen Haydons's comment tool uses { }    OPTIONAL
FLOAD VOCABS.SEQ   .( .)   \ Forths ONLY ALSO vocabulary structure.
FLOAD DEFERS.SEQ   .( .)   \ Adds DEFERS and UNDEFERS
FLOAD BUFSET.SEQ   .( .)   \ Automatically adjust read buffer size.
FLOAD VALIDATE.SEQ .( .)   \ old version checking for F-PC
FLOAD DECOM.SEQ    .( .)   \ decompiler,                        OPTIONAL
FLOAD DUMP.SEQ     .( .)   \ dump utility,                      OPTIONAL
FLOAD CASE.SEQ     .( .)   \ A CASE utility needed by PASM.SEQ
FLOAD PASM.SEQ     .( .)   \ Prefix/Postfix assembler for 8086/8088
FLOAD NEWLAB.SEQ   .( .)   \ new local label definition file
FLOAD LOADEXE.SEQ  .( .)   \ The load part of the SAVE-EXE mechanism
FLOAD SAVEEXE.SEQ  .( .)   \ The save part of the SAVE-EXE mechanism
\ Now we can save the system back to disk at any time.

FLOAD DBGFIX.SEQ   .( .)   \ Convert inline NEXT to JMP NEXT. for DEBUG
FLOAD DEBUG.SEQ    .( .)   \ high level debugger,               OPTIONAL
FLOAD PATHSET.SEQ  .( .)   \ Includes paths on files,           OPTIONAL
\ FLOAD MULTASK.SEQ  .( .) \ Multi tasking,                     OPTIONAL
FLOAD hyper.seq    .( .)   \ A simple hyper text tool
FLOAD SEARCH.SEQ   .( .)   \ String comparison & search stuff
FLOAD LARGEST.SEQ  .( .)   \ find the largest word in a list
FLOAD WORDS.SEQ    .( .)   \ WORDS,                             OPTIONAL
FLOAD IBMCURSR.SEQ .( .)   \ IBM cursor shape control words
FLOAD MONOCROM.SEQ .( .)   \ Monochrome support, always needed.
FLOAD COLOR.SEQ    .( .)   \ Support for Color                  OPTIONAL
FLOAD BLINKER.SEQ  .( .)   \ background blink on/off            OPTIONAL?
FLOAD COLORIZE.SEQ .( .)   \ Leon Dent's COLORIZER              OPTIONAL
FLOAD BOXTEXT.SEQ  .( .)   \ ability to draw boxes
FLOAD SAVESCR.SEQ  .( .)   \ Screen save and restore.

CAPS ON

FLOAD qvideo.seq   .( .)   \ SPEED UP SCREEN DISPLAY            OPTIONAL
FLOAD pertype.seq  .( .)   \ Imbedded display attributes in TYPE
FLOAD hello.seq    .( .)   \ Cold start initialization & introduction.
FLOAD ledit.seq    .( .)   \ Line editor utility
FLOAD view.seq     .( .)   \ Source VIEWing words               OPTIONAL
FLOAD status.seq   .( .)   \ Status line,                       OPTIONAL
FLOAD fl.seq       .( .)   \ File selection.
FLOAD wfl.seq      .( .)   \ WINDOW File selection              OPTIONAL
FLOAD needs.seq    .( .)   \ Allow optional loading of needed files.
FLOAD filstat.seq  .( .)   \ Display file loaded or open        OPTIONAL
FLOAD environ.seq  .( .)   \ Environment words.
FLOAD exec.seq     .( .)   \ DOS interface for things like DIR,COPY ect.
FLOAD emmptr.seq   .( .)   \ Expanded memory pointer support    OPTIONAL
FLOAD menus.seq    .( .)   \ menu driver for F-PC               OPTIONAL
FLOAD makedef.seq  .( .)   \ allow conversion from : to DEFER   OPTIONAL
FLOAD print.seq    .( .)   \ Print to a file words.             OPTIONAL

CR

true  #if        \ Do we want to load the SED editor??

headerless      \ entire editor is headerless, except for a few words in
                \ the file TOPEDIT.SEQ

FLOAD editstuf.seq .( .)   \ Allow loading editor NOT as an overlay
FLOAD sedcode.seq  .( .)   \ SED assembly definitions.
FLOAD seditor.seq  .( .)   \ My editor SED. Written by Tom Zimmer.
FLOAD sedit2.seq   .( .)   \ The second part of the editor body.

comment:

        If you DO NOT load these it will save you about 28 thousand
        bytes in the executable file.  Some of these can be individually
        removed without adversly effecting SED's operation.

        Place a \ symbol before any of the following files you don't want
        to load.

        Any functions not loaded will generate a NOT AVAILABLE message if
        you try to use them.

comment;

FLOAD prtctrl.seq  .( .) \ Printer control, generic                OPTIONAL
FLOAD printing.seq .( .) \ The printing part of SED.      Alt-P    OPTIONAL
FLOAD laserjet.seq .( .) \ HP LasetJet support                     OPTIONAL
FLOAD proprint.seq .( .) \ IBM PROPRINTER, use with PRTCTRL        OPTIONAL
FLOAD sedcase.seq  .( .) \ Case convert, Date      Alt-O_U, L, P   OPTIONAL
FLOAD seditwp.seq  .( .) \ Word wrap, reformat,    Alt-S_R Ctrl-B  OPTIONAL
FLOAD sedjust.seq  .( .) \ Left margin adjustments        Alt-L    OPTIONAL
FLOAD seddraw.seq  .( .) \ Character line drawing         F9       OPTIONAL
FLOAD sedsort.seq  .( .) \ Paragraph line sorting         F7       OPTIONAL
FLOAD sedcopy.seq  .( .) \ Cut Copy & Paste        Alt-X, C, V     OPTIONAL
FLOAD sedapnd.seq  .( .) \ Append text Use with SEDCOPY   Alt-A    OPTIONAL
FLOAD sedpage.seq  .( .) \ Goto page command for SED      Alt-G    OPTIONAL
FLOAD sedwind.seq  .( .) \ Window adjustment utility      Alt-S_W  OPTIONAL
FLOAD sedchars.seq .( .) \ Graphic char select            Alt-O_A  OPTIONAL
FLOAD sedshell.seq .( .) \ SHELL to DOS utility for SED   ESC-F-D  OPTIONAL
FLOAD htype.seq    .( .) \ Hyper text display TYPE                 OPTIONAL
FLOAD sedwhelp.seq .( .) \ Help within editor on words    Alt-H    OPTIONAL
FLOAD topedit.seq  .( .) \ Top level editing words
FLOAD helplink.seq .( .) \ Link in the F1 help keys                OPTIONAL
FLOAD editset.seq  .( .) \ Allow editor command key redefinition.
FLOAD sedmenu.seq  .( .) \ Menu utility for SED           ESC      OPTIONAL

headers

only forth also definitions

FLOAD newfile.seq  .( .) \ new file creation utility.              OPTIONAL
FLOAD editerr.seq  .( .) \ Automatically edit on load error.       OPTIONAL
FLOAD browsepr.seq .( .)   \ allow print re-direction to browser OPTIONAL
FLOAD MLOAD.SEQ    .( .)   \ mark lines in editor, leave & F9 loads

#endif

FLOAD sound.seq    .( .) \ Add TONE and change BEEP to use TONE    OPTIONAL
FLOAD scan.seq     .( .) \ word scanning utility used by REF       OPTIONAL
FLOAD ref.seq      .( .) \ a cross reference utility               OPTIONAL
FLOAD watcher.seq  .( .) \ add watch points to the debugger        OPTIONAL
FLOAD fwords.seq   .( .) \ Hi level file manipulation words.       OPTIONAL
FLOAD winstack.seq .( .) \ Pulldown .STACK Press both SHIFT keys   OPTIONAL
\     xexpect.seq  .( .) \ A line editor for EXPECT.               OPTIONAL
FLOAD nexpect.seq  .( .) \ A NEW line editor for EXPECT.           OPTIONAL
FLOAD listset.seq  .( .) \ Allow easy user list space adjustment   OPTIONAL

\ ***************************************************************************
comment:

  By not loading the following two files MOUSE.SEQ and MOUSEY.SEQ, you
can disable mouse support. It will save you about 3k or so of .EXE file
space.

comment;

FLOAD mouse.seq    .( .)   \ low level mouse interface           OPTIONAL
FLOAD mousey.seq   .( .)   \ Development application support     OPTIONAL
FLOAD macros.seq   .( .)   \ Add keyboard macros to F-PC.        OPTIONAL
FLOAD svsesdat.seq .( .)   \ Autosave of session information     OPTIONAL

warning on

behead                  \ discard all the headerless word in ONE SWELL FOOP

mark empty              \ Mark the end of the dictionary.
yhere fence !           \ Set fence to prevent FORGETing beyond YHERE

\ Default configuration parameters

\u autoediton           autoediton
\u backupon             backupon
\u fast                 fast
\u >color               ' >color is initcolor
\u colorizeon           colorizeon
\u white-on-black       white-on-black
\u blankoff             blankoff
\u decimalbase          decimalbase     \ default to DECIMAL on any error
\u size-save            size-save       \ save the .SIZ file of word sizes

.compstat

7400 =: #listsegs               \ give me a BIGGER dictionary
   0 =: #ovbytes                \ no overlays for now
   0 =: #ovsegs                 \ no overlays for now "again"

cr .used

