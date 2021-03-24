\ EDITSET.SEQ   Set the editor key assignments          by Tom Zimmer

comment:

  The file EDITSET.SEQ contains the command key definitions for the
SED editor. You can change these assignments, to redefine how the
editor works.

  See the end of EDITSET.SEQ for a description of the functions
available in the editor.

comment;

editor definitions

        \ control key function table

control @ ctlset kerr           \               ** Not used **
control A ctlset lwrd           \ Control A     left word
control B ctlset jstfy          \ Control B     paragraph justify
control C ctlset pdn            \ Control C     page down
control D ctlset rchr           \ Control D     right character
control E ctlset upln           \ Control E     up line
control F ctlset rwrd           \ Control F     right word
control G ctlset fdel           \ Control G     forward delete
control H ctlset bdel           \ Control H     Back Space
control I ctlset stab           \ Control I     TAB
control J ctlset Ctrl-J         \ Control J     Spawn DOS shell, Ctrl-Enter
control K ctlset kerr           \ Control K     ** Not used **
control L ctlset lmset          \ Control L     set left margin
control M ctlset browse-nln     \ Control M     Enter handler
control N ctlset spltln         \ Control N     split line at cursor
control O ctlset sednew         \ Control O     open a new file
control P ctlset kerr           \ Control P     ** Not used **
control Q ctlset kerr           \ Control Q     ** Not used **
control R ctlset pup            \ Control R     page up
control S ctlset lchr           \ Control S     left character
control T ctlset wdel           \ Control T     word delete
control U ctlset updt           \ Control U     save changes and continue
control V ctlset itgl           \ Control V     insert toggle on/off
control W ctlset sclup          \ Control W     scroll screen up
control X ctlset dnln           \ Control X     down line
control Y ctlset ldel           \ Control Y     line delete
control Z ctlset scldn          \ Control Z     scroll screen down
control [ ctlset sedesc         \ ESCape        ESC menu handler
control \ ctlset tobslash       \ Control \     to next '\' character
control ] ctlset shomem         \ Control ]     display computers memory map
control ^ ctlset kerr           \ Control ^     ** Not used **
control _ ctlset kerr           \ Control _     ** Not used **


127 fnset fdel          \ Control Backspace
143 fnset sbtab         \ Shift-TAB     Back TAB
158 fnset appendx       \ Alt-A         Append to file
176 fnset wordbrowse    \ Alt-B         Browse word under cursor
174 fnset exportx       \ Alt-C         Copy to file
160 fnset kerr          \ Alt-D         ** Not used **
146 fnset wordedit      \ Alt-E         Edit word under cursor
161 fnset kerr          \ Alt-F         ** Not used **
162 fnset alt-g         \ Alt-G         Goto- line,page,paragraph
163 fnset wordhelp      \ Alt-H         show help on word under cursor
151 fnset kerr          \ Alt-I         ** Not used **
164 fnset joinln        \ Alt-J         Join lines
165 fnset kerr          \ Alt-K         ** Not used **
166 fnset lftjust       \ Alt-L         left justification
178 fnset NOOP          \ Alt-M         Not usable, Make macro
177 fnset nxtbrowse     \ Alt-N         next browse
152 fnset alt-O         \ Alt-O         Other-case conversion, tab expand
153 fnset pmenux        \ Alt-P         Print menu
144 fnset shom          \ Alt-Q         Home to top of file
147 fnset NOOP          \ Alt-R         Not usable, Repeat a macro
159 fnset sedset        \ Alt-S         Setup-right margin, window size
148 fnset tabset        \ Alt-T         Set TAB increment
150 fnset wudel         \ Alt-U         Word undelete
175 fnset importx       \ Alt-V         Paste from file
145 fnset wr->fl        \ Alt-W         Write memory to a new file
173 fnset excutx        \ Alt-X         Cut to a file
149 fnset lundel        \ Alt-Y         Line undelete
172 fnset send          \ Alt-Z         Goto End of file

248 fnset kerr          \ Alt 1         ** Not used **
249 fnset kerr          \ Alt 2         ** Not used **
250 fnset kerr          \ Alt 3         ** Not used **
251 fnset kerr          \ Alt 4         ** Not used **
252 fnset kerr          \ Alt 5         ** Not used **
253 fnset kerr          \ Alt 6         ** Not used **
254 fnset kerr          \ Alt 7         ** Not used **
255 fnset ecmdtgl       \ Alt 8         show edit command toggle
128 fnset (--)char      \ Alt 9         standard stack framework "( -- ) \"
129 fnset kerr          \ Alt 0         ** Not used **

130 fnset kerr          \ Alt -         ** Not used **
131 fnset kerr          \ Alt =         ** Not used **

199 fnset shoml         \ HOME  key     beginning of line
207 fnset sendl         \ END   key     end of line

200 fnset upln          \ Up    Arrow   up a line
208 fnset dnln          \ Down  Arrow   down a line
203 fnset lchr          \ Left  Arrow   left a character
205 fnset rchr          \ Right Arrow   right a character

201 fnset pup           \ PgUp          page up
209 fnset pdn           \ PgDn          page down

210 fnset itgl          \ Insert key    insert mode toggle on/off
211 fnset fdel          \ Delete key    delete char under cursor

243 fnset lwrd          \ Control Left Arrow    word left
244 fnset rwrd          \ Control Right Arrow   word right
247 fnset shom          \ Control HOME          beginning of file
245 fnset send          \ Control END           End of file
132 fnset sclup         \ Control PgUp          Scroll screen up
246 fnset scldn         \ Control PgDn          Scroll screen down

187 fnset helpF1        \ F1=HELP
187 =: helpkey          \ Assign F1 as the HELPKEY value.
                        \ If you set another key as HELP, then
                        \ change the 187 to the new value of help.

188 fnset tscrn         \ F2            top of screen
189 fnset smrk          \ F3            set mark
190 fnset bscrn         \ F4            bottom of screen
191 fnset sgetl         \ F5            get line from mark
192 fnset sloon         \ F6            search/find (prompts for string)
193 fnset drawlin       \ F7            line drawing mode toggle
194 fnset srepn         \ F8            replace (prompts for string)
195 fnset wordbrowse    \ F9            browse word under cursor
196 fnset sesc          \ F10           save and exit

212 fnset flipfiles     \ Shift F1      select one of all open files
213 fnset kerr          \ Shift F2      ** Not used **
214 fnset kerr          \ Shift F3      ** Not used **
215 fnset kerr          \ Shift F4      ** Not used **
216 fnset kerr          \ Shift F5      ** Not used **
217 fnset sloob         \ Shift F6      search backwards
218 fnset sortlin       \ Shift F7      sort paragraph case sensitive
219 fnset repall        \ Shift F8      replace all occurances
220 fnset browsetgl     \ Shift F9      toggle browse on/off
221 fnset sescall       \ Shift F10     exit all edit/browses

222 fnset kerr          \ Control F1    ** Not used **
223 fnset kerr          \ Control F2    ** Not used **
224 fnset kerr          \ Control F3    ** Not used **
225 fnset kerr          \ Control F4    ** Not used **
226 fnset kerr          \ Control F5    ** Not used **
227 fnset sloow         \ Control F6    search word under cursor
228 fnset kerr          \ Control F7    ** Not used **
229 fnset kerr          \ Control F8    ** Not used **
230 fnset wordfrom      \ Control F9  word was loaded from
231 fnset kerr          \ Control F10   ** Not used **

232 fnset togglefiles   \ Alt F1   rotate through all open files
233 fnset tmscrn        \ Alt F2   goto top middle of screen
234 fnset hsrcschr      \ Alt F3   insert source hyper link
235 fnset bmscrn        \ Alt F4   goto bottom middle of screen
236 fnset hdstschr      \ Alt F5   insert destination hyper link
237 fnset slooa         \ Alt F6   search/find again
238 fnset sortlin       \ Alt F7   sort paragraph lines
239 fnset srepa         \ Alt F8   replace again
240 fnset worddefer     \ Alt F9   Edit word through defered word
241 fnset squt          \ Alt F10  discard edit and quit

\ At the moment I do not know of any keys that will generate the
\ following values.
\                                                       Tom Zimmer

133 fnset kerr          \ 133           ** Not used **
134 fnset kerr          \ 134           ** Not used **
135 fnset kerr          \ 135           ** Not used **
136 fnset kerr          \ 136           ** Not used **
137 fnset kerr          \ 137           ** Not used **
138 fnset kerr          \ 138           ** Not used **
139 fnset kerr          \ 139           ** Not used **
140 fnset kerr          \ 140           ** Not used **
141 fnset kerr          \ 141           ** Not used **
142 fnset kerr          \ 142           ** Not used **
154 fnset kerr          \ 154           ** Not used **
155 fnset kerr          \ 155           ** Not used **
156 fnset kerr          \ 156           ** Not used **
157 fnset kerr          \ 157           ** Not used **
167 fnset kerr          \ 167           ** Not used **
168 fnset kerr          \ 168           ** Not used **
169 fnset kerr          \ 169           ** Not used **
170 fnset kerr          \ 170           ** Not used **
171 fnset kerr          \ 171           ** Not used **
179 fnset kerr          \ 179           ** Not used **
180 fnset kerr          \ 180           ** Not used **
181 fnset kerr          \ 181           ** Not used **
182 fnset kerr          \ 182           ** Not used **
183 fnset kerr          \ 183           ** Not used **
184 fnset kerr          \ 184           ** Not used **
185 fnset kerr          \ 185           ** Not used **
186 fnset kerr          \ 186           ** Not used **
197 fnset kerr          \ 197           ** Not used **
198 fnset kerr          \ 198           ** Not used **
202 fnset kerr          \ 202           ** Not used **
204 fnset kerr          \ 204           ** Not used **
206 fnset kerr          \ 206           ** Not used **
242 fnset kerr          \ 242           ** Not used **

forth definitions

\S      Don't compile the rest of this file.


        Function Descriptions for the above key definitions


Alt-O           Other options- case conversion, tab expand etc.
Ctrl-J          DOS command shell spawn
^cc             Insert the value of any key following this command.
alt-O           Other-case conversion, tab expand
alt-g           Goto- line,page,paragraph
appendx         Append to file
bdel            Delete character before cursor
browse-nln      Enter handler
browsetgl       toggle browse on/off
bscrn           Move to bottom of screen.
dnln            Move down a line
drawlin         Enter line drawing mode until ESC pressed.
excutx          Delete all marked lines of text, MARK sets start.
exportx         Copy the marked portion to a file
fdel            Delete current character under cursor
flipfiles       select one of all open files
hdstschr        insert destination hyper link
hsrcschr        insert source hyper link
importx         Paste the file TEMP.SEQ at the cursor
itgl            Toggle between insert and overwrite mode
joinln          Join this line and next line.
jstfy           paragraph justify
kerr            Key error, beeps
l>lcase         Convert the current line to lower case
l>ucase         Convert the current line to uppercase
lchr            Move left  a character
ldel            Delete all of current edit line
lftjust         left justification
lmset           Set the left margin to current cursor position
lundel          Undelete the last line deleted, upto 50 lines.
lwrd            Move left  a word
nln             Return, go down to next line.
pastedatetime   Paste the date and time at the cursor.
pdn             Move down one screen full
pmenux          Enter print menu.
pup             Move up   one screen full
rchr            Move right a character
repall          Replace all occurances of found text with replace text
rwrd            Move right a word
sbtab           Move back to previous tab position.
scldn           Scroll the screen down, leaving cursor on same screen line.
sclup           Scroll the screen up,   leaving cursor on same screen line.
sedesc          ESC menu handler
sednew          open a new file
sedset          Setup-right margin, window size
send            Move to the end       of the file.
sendl           Move to the end       of the current line.
sesc            Terminate edit and save changes
sescall         exit all edit/browses
sgetl           Get a line from MARK to current cursor position.
shelp           Opens the Help browser
shom            Move to the beginning of the file.
shoml           Move to the beginning of the current line.
slooa           Search again for same text.
sloob           Search backwards for last text searched for.
sloon           Search for name with prompt for text.
smrk            Set the MARK for copy lines, or export.
sortlin         Sort the current paragraph lines on the current column.
spltln          Split current line at cursor into two lines
squt            Terminate the edit, and discard all changes.
srepa           Replace again with same text.
srepn           Replace searched text with prompt for text.
stab            Insert spaces up to next tab setting
tabset          Set the tab increment to multiples of the current column.
tabxp           Expand tabs in entire file to spaces.
togglefiles     rotate through all open files
tscrn           Move to top of screen.
updt            Save changes to disk and continue editing
upln            Move up a line
wdel            Delete word forward starting at cursor
wordbrowse      browse word under cursor
worddefer       Edit word through defered word
wordedit        Edit word under cursor
wordhelp        show help on word under cursor
wr->fl          Write all of current file to a new filename
wudel           Undelete the most recent word deleted.

