\ DEFERS.SEQ    Adds the ability to make a defered word chain. Tom Zimmer

comment:

  The words which follow, give you the ability to make a chain of procedures
that will be performed in sequence when a normal DEFERed word is executed.

  DEFERS chains in the current contents of the specified defered word.  The
new definition is then installed into the defered word with IS.

        DEFER JUNK      ' NOOP IS JUNK

        : JUNK1         DEFERS JUNK .. MYJUNK1 .. ;

        ' JUNK1 IS JUNK

        : JUNK2         DEFERS JUNK .. MYJUNK2 .. ;

        ' JUNK2 IS JUNK

  When JUNK is executed, the word NOOP will be performed, then MYJUNK1,
followed by MYJUNK2.

  UNDEFER can be used to remove entries from the chain, but must be used
on the word defined later than the word you want to remove:

        UNDEFER JUNK2 <enter>

  The above command removes JUNK1 from the chain.

comment;

: DEFERS        ( T1 -- )
                ' DUP @REL>ABS [ ' KEY @REL>ABS ] LITERAL = >R
                >BODY @ R> IF UP @ + @ THEN X, ; IMMEDIATE

: UNDEFER       ( T1 -- )
                ' DUP @REL>ABS [ ' KEY @REL>ABS ] LITERAL = >R
                >BODY R> IF @ UP @ + THEN DUP @
                DUP @REL>ABS [ ' DEFERS @REL>ABS ] LITERAL <>
                ABORT" Can only UNDEFER through a : (colon) definition."
                >BODY @ +XSEG 0 @L SWAP ! ;


