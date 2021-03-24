\ SEDMENU.SEQ   Visual menu selection tool for SED      by Tom Zimmer

editor definitions

defined teletype nip #if        \ only if print control stuff loaded

: dobold        ( --- )
                '%' schr 'B' schr ;

: dounderline   ( --- )
                '%' schr 'U' schr ;

#endif

: aonly         ( --- )
                savescr cursor-off
                ['] noop save!> dobutton
                14 6 64 9 box&fill
                ."  You MUST use the \1 Alt \0 key sequence for MACROS. "
                bcr
                ." \s10Press a \r KEY \0 to continue."
                key drop
                restore> dobutton
                restscr cursor-on ;

newmenu file$
?browse 0=
#if
\u sednew       menuline"  Open or make another file  Ctrl-O " sednew
                menuline"  Save changes & continue    Ctrl-U " updt
                menuline"  Write as a different file   Alt-W " wr->fl
\u togglefiles  menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u togglefiles  menuline"  Rotate through open files  Alt-F1 " togglefiles
\u flipfiles    menuline"  Flip directly to file    Shift-F1 " flipfiles
\u bindex       menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u bindex       menuline"  Build HYPER.NDX from all [*.TXT]  " bindex
\u docompile    menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u docompile    menuline"  Command to DOS            Ctrl_Ù " docompile
\u pmenu        menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u pmenu        menuline"  Print menu                  Alt-P " pmenux
\u wordbrowse   menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
                menuline"  Quit, save & unlink one       F10 " sesc
                menuline"  Discard changes & unlink  Alt-F10 " squt
\u sescALL      menuline"  eXit & save all edits   Shift-F10 " sescALL
#else
\u wordbrowse  menuline"  Link to word under cursor (browse)   F9 " wordbrowse
\u togglefiles menuline"  Rotate through open links        Alt-F1 " togglefiles
\u togglefiles menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
               menuline"  Unlink one level                    F10 " sesc
\u sescALL     menuline"  Quit & unlink all levels      Shift-F10 " sescALL
#then
endmenu

?browse 0=
#if
newmenu edit$
                  menuline"  Mark begin/end/clear       F3 " smrk
                  menuline"  Cut . . . . . . . . . . Alt-X " excutx
                  menuline"  Duplicate (copy)        Alt-C " exportx
                  menuline"  Paste . . . . . . . . . Alt-V " importx
\u append         menuline"  Append                  Alt-A " appendx
                  menuline" Ä        ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ   Ä" noop
                  menuline" Ä  Use Shift-Alt to set File  Ä" noop
\u paste_datetime menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u paste_datetime menuline"  Time & date paste     Alt-O_P " paste_datetime
\u justify        menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u justify        menuline"  Reformat paragraph     Ctrl-B " jstfy
#else
newmenu xxx$
#then
defined 'word@cur nip
#if
                  menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
                  menuline"  Edit   word at CURSOR   Alt-E " wordedit
                  menuline"  Browse word at CURSOR   Alt-B " wordbrowse
                  menuline"  Help   word at CURSOR   Alt-H " wordhelp
#then
\u edflook      ' true alias zedding    \ if making ZED, don't load some
                                        \ of the following lines.

                  menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
                  menuline"  1 cursor to scrn  top      F2 " tscrn
\- zedding        menuline"  2 cursor to upper mid  Alt-F2 " tmscrn
\- zedding        menuline"  3 cursor to lower mid  Alt-F4 " bmscrn
                  menuline"  4 cursor to scrn  bot      F4 " bscrn
endmenu

newmenu select$
            menuline"  Find                           F6 " sloon
            menuline"  Next find . . . . . . . .  Alt-F6 " slooa
            menuline"  Backwards find           Shift-F6 " sloob
?browse 0=
#if
            menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
            menuline"  Replace  (use F6-Find first)   F8 " srepn
            menuline"  Again replace  . . . . . . Alt-F8 " srepa
            menuline"  Every replace            Shift-F8 " repall
#then
\u alt-go   menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u alt-go   menuline"  Goto page/line/paragraph    Alt-G " alt-g
\u edflook  menuline"  Scan files for text        Alt-F4 " edflook
endmenu

?browse 0=      \ If we are building a small browser, don't load these
#if
newmenu hyper$
\u wordbrowse menuline"  Link to word under cursor (browse)  F9 " wordbrowse
              menuline"  Unlink one level                   F10 " sesc
\u worddef    menuline"  Defered (link into DEFERed)     Alt-F9 " worddefer
\u nbrowse    menuline"  Enter a word name to browse     Alt-F9 " nbrowse
\u wordfrom   menuline"  From where was word loaded     Ctrl-F9 " wordfrom
\u browsetgl  menuline"  Browse mode toggle (on/off)   Shift-F9 " browsetgl
\u nxtbrowse  menuline"  Next occurance of word browse    Alt-N " nxtbrowse
\u hsrcschr   menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u hsrcschr   menuline" ÄÄÄùTurnÿtheÿcursorÿwordÿintoÿaÿLink ÄÄÄ" noop
\u hsrcschr   menuline" ÄÄÄ  ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u hsrcschr   menuline"  Source link                     Alt-F3 " hsrcschr
\u hdstschr   menuline"  Destination link                Alt-F5 " hdstschr
endmenu

newmenu advanced$
\u sedchar       menuline"  Insert special char   Alt-O_A " insany
\u tabxp         menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u tabxp         menuline"  Xpand tabs to spaces  Alt-O_X " tabxp
\u ssort         menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u lines         menuline"  Draw character lines       F7 " drawlin
\u ssort         menuline"  Sort paragraph lines   Alt-F7 " sortlin
\u l>ucase       menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u l>ucase       menuline"  UPPERCASE this line   Alt-O_U " l>ucase
\u l>lcase       menuline"  Lowercase this line   Alt-O_L " l>lcase
\u ccasetgl      menuline"  Character case toggle Alt-O_C " ccasetgl
\u wcasetgl      menuline"  Word case toggle      Alt-O_W " wcasetgl
\u adjust-window menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u adjust-window menuline"  Adjust window size    Alt-S_W " adjwind
\u split-wind    menuline"  Split window toggle   Alt-S_S " splitwind
\u zoom-window   menuline"  Zoom window toggle    Alt-S_Z " zoomwind
                 menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
                 menuline"  Make a macro            Alt-M " aonly
                 menuline"  Repeat a macro          Alt-R " aonly
                 menuline"  Do a macro      Alt-1 - Alt-8 " aonly
endmenu

newmenu layout$
\u lmset   menuline"  Left  margin = current col   Ctrl-L " lmset
\u rmset   menuline"  Right margin set            Alt-S_R " rmset
\u ljust   menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u ljust   menuline"  Column move right             Alt-L " ljust
\u lzero   menuline"  Delete leading spaces   Shift-Alt-L " lzero
\u spltln  menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u spltln  menuline"  Split line at cursor         Ctrl-N " spltln
\u joinln  menuline"  Join this line to next        Alt-J " joinln
\u tabset  menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u tabset  menuline"  Tab increment set             Alt-T " tabset
\u dobold  menuline" ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ" noop
\u dobold  menuline"  Bold       on/off                   " dobold
\u dobold  menuline"  Underline  on/off                   " dounderline
endmenu

newmenu help$
\u helpF1    menuline"  Help me use SED       F1 " helpF1
\u 'word@cur menuline"  Current word HELP  Alt-H " wordhelp
\- zedding   menuline"  Show names toggle  Alt-8 " ecmdtgl
endmenu
#then

newmenubar editorbar       \ menubar for the editor
                +," File  "
\u edit$        +," Edit  "
                +," Select "
\u layout$      +," Layout "
\u hyper$       +," hYper  "
\u advanced$    +," Advanced "
\u help$        +," Help  "
endmenu

create editlist         \ menu list for the editor
                file$ ,
\u edit$        edit$ ,
                select$ ,
\u layout$      layout$ ,
\u hyper$       hyper$ ,
\u advanced$    advanced$ ,
\u help$        help$ ,

: editcolumn    ( --- n1 )
                first.textcol 2+ ;

\- colsave      0 value colsave

: sedmenu       ( --- )
                colsave =: mcol                 \ restore my column
                savemenu                        \ save previous menu parms
                editorbar =: menubar            \ set pointer to MENUBAR
                editlist  =: menulist           \ and to the menulist
                ['] statusline is mline         \ set the menu line func
                ['] editcolumn is mcolumn       \ and the menu column func
                ['] doachar    is doother       \ and the func to handle keys
                                                \ not on the menu.
                ['] mbutton save!> sbutton      \ install normal menu handler
                menu                            \ call the menu procedure
                restore> sbutton                \ restore editor menu handler
                restmenu                        \ restore previous menu parms
                mcol =: colsave ;               \ save my column for later

' sedmenu is sedesc

forth definitions

