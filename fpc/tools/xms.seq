\\ XMS.SEQ      Extended memory manager interface.              clv 1993

ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

Purpose:Words to use a eXtended-Memory-Manager (for example HIMEM.SYS).
        Tested with HIMEM.SYS 2.60
        This code should not be used with a multitasker because it
        uses global variables to pass parameters.

see also:XMS.HLP for glossary and notes.
        FASTMOVE.SEQ and G@386.SEQ for real-mode-use of XM
        see part IMPORT on how to load it

Author: Harald Albrecht 9/89.
        Thanks to the Heinz-Heise-Verlag for the permission to distribute.

Adapted to F-PC by:
        1993 Claus Vogt, Mitglied der Forth-Gesellschaft
        Blowstr. 67, D-10783 Berlin, Germany, 0(049)30/216 89 38

Minor mods for F-PC by:
        1994 Tom Zimmer, integrate english help file right into this
        source file. Also added comment lines to prevent the test code
        at the end of the file from being loade automatically, but to
        tell the user what line to load if they want to see the XMS
        demo.

ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ



Author: 1993 Claus Vogt, Mitglied der Forth-Gesellschaft
        Blowstr. 67, D-10783 Berlin, Germany, 0(049)30/216 89 38

ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ
 \ mis94feb14   a) commented out 'needs pasm383.seq' , isn't really needed
        \ b) fixed a bug in XMMove
        \ c) added some extended move interface words: ùxmov-parm ,
        \ ù=XMOVLEN , ù=XMOVSRC , ù=XMOVDEST , ùREAL-ADDR , ùEMB-ADDR
        \ and XMOV
        \ d) added a test suite, that also shows the usage of the new
        \ words above
clv 01/27/93 14:59:33.52 : changed 80386-setup to 80286-setup
clv 12/01/92 12:31:56.51 : commented out PUSH/POP FS from CallXM & (XMabort)
                           G@386.SEQ will perhaps need these PUSH/POPs.
                           Works without with HIMEM.SYS

   *** General notes to use it: ***

   - XMInit has to be executed (installed into initstuff) first.
   - If you use one of these words without a XM-Manager (like HIMEM.SYS)
     present, your forth aborts with a message. If you don't want it
     to do so, check XMAvail first.
   - after each word XMResult is set to a non-zero one-byte value, if
     an error occured. Check XMResult or use ?XM to abort on errors.

{ \\ imports

\ NEEDS PASM386.SEQ       \ only a few 80386-instruction are used
        \ mis94feb14
\ NEEDS CODEBUG.SEQ       \ testing purposes
\ NEEDS DASM386.SEQ       \ testing purposes

\ PREFIX ASM.386          \ for 80386-Processor
PREFIX                \ for 80286-Processor

}

{ \ compiler setup
ONLY FORTH ALSO DEFINITIONS
DECIMAL
}

{ \ XMS-interface

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\  nach:X M M - UNIT zur Benutzung des Extended Memory
\                 c't /  Harald Albrecht 1989
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ}

}
XMAvail ( -- flag )

        Value is true if XM-Manager is present. Is set by XMINIT
{
FALSE VALUE XMAvail          \ BOOLEAN; { XMM ist installiert }

}
XMResult ( -- 8b )

        An errorflag of the last recent XM-Operation.
        If no error occured 8b is zero.
        See also ?XM.
{
FALSE VALUE XMResult         \ BYTE;    { Fehlermeldung XMM   }

2VARIABLE XMAddr \ addr of XMM-Handler

VARIABLE xAX       \ temporary registers for XMM
VARIABLE xBX
VARIABLE xDX
VARIABLE xDS
VARIABLE xSI

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Aufruf des XMM (Extended Memory Managers) ausfhren.
\ Die Parametervariablen BX, DX, SI sowie DS mssen vorher
\ entsprechend belegt sein. Nach dem Aufruf werden AX, BX
\ und DX zurckgesichert.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ

CODE CallXM ( 8b -- )  \ XMM-Handler aufrufen
       POP  AX             \ Funktionscode nach AH,
       XCHG AH, AL
\      PUSH FS             \ for 80386 for @G necessary ??. Comment for 80286
       PUSH ES
       PUSH SI
       PUSH DS
       MOV  BX, xBX        \ sowie die anderen Register
       MOV  DX, xDX        \ vorbereiten
       MOV  SI, xSI        \
       MOV  CX, DS         \ DS retten !
       MOV  ES, CX         \ nach ES, da noch ben”tigt.
\      PUSH CX             \ Den Inhalt von DS retten
       MOV  DS, xDS        \ DS neu laden
       ES:
       CALL FAR [] XMAddr \ XMM aufrufen
       POP  DS             \ DS als erstes wiederher-
       POP  SI
       POP  ES
\      POP  FS             \ for 80386 for @G necessary ??. Comment for 80286
       MOV  xAX AX        \ stellen und die Werte in
       MOV  xBX BX        \ den anderen Registern
       MOV  xDX DX        \ Pascal zur Verf. stellen
       NEXT END-CODE

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Falls kein XMM installiert ist, laufen alle Versuche,
\ XMM-Routinen auszufhren, in dieser Prozedur auf. Da in
\ CallXMM register gerettet wurden, muessen sie hier
\ wiederhergestellt werden.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ

: XMAbort  true abort" XMM not installed" ;

Label (xmAbort)
        POP  AX             \ remove ret-addr
        POP  AX
        POP  DS
        POP  SI
        POP  ES
\       POP  FS             \ for 80386 for @G necessary ??. Comment for 80286
        MOV AX, # ' XMAbort
        JMP AX
        END-CODE

}
XMInit  ( -- )

        Should be used before first XM-Operation.
        Is installed into initstuff to be executed each
        time you boot your system.
{
Code XMInit     \ initialize XMS-Manager-Interface
\       PUSH FS             \ for 80386 for @G necessary ??. Comment for 80286
        MOV AX, CS
        MOV XMAddr 2+  AX
        MOV AX, # (xmAbort)
        MOV XMADDR     AX
        MOV AX, # $4300  \ Per Multiplexintr. die
        INT $2F        \ Existenz des XMM feststel-
        CMP AL, # $80    \ len.
        0= IF
                MOV AX, # $4310  \ Adresse des Dispatchers
                PUSH ES
                INT $2F        \ ermitteln
                MOV XMAddr     BX
                MOV XMAddr  2+ ES
                POP ES
        THEN
        MOV AX, # $ffff
        MOV ' XMAvail >BODY  AX
        MOV AX, CS
        CMP XMAddr  2+  AX
        0= IF
                MOV AX, # (xmAbort)
                CMP XMAddr      AX
                0=  IF  INC WORD ' XMAvail >BODY  THEN
        THEN
        MOV AX, # 0
        MOV ' XMResult >BODY AX
\       POP  FS             \ for 80386 for @G necessary ??. Comment for 80286
        NEXT END-CODE

: %XMInit defers initstuff XMinit ;

' %XMInit Is initstuff



\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Fehlercodierung des XMM so umwandeln, daá der Fehlercode
\ in XMMResult zur Verfgung steht.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ

: ErrChk  xAX @ 1 =  IF  0  ELSE  xBX C@  THEN  =: XMResult ;

\ CONST XMMOk            = $00; { fehlerfrei ausgefhrt      }
\       XMMNotImpl       = $80; { ist nicht implementiert    }
\       XMMVDisk         = $81; { VDISK-Ger„t entdeckt       }
\       XMMA20Error      = $82; { A20-Fehler eingetreten     }
\       XMMGeneralDrvErr = $8E; { Genereller Treiberfehler   }
\       XMMUnrecDrvErr   = $8F; { Nicht-behebbarer Fehler    }
\       XMMNoHMA         = $90; { Kein HMA vorhanden         }
\       XMMHMAInUse      = $91; { HMA ist bereits vergeben   }
\       XMMToLow         = $92; { HMA fr Prg. nicht verfgb.}
\       XMMHMANotAlloc   = $93; { HMA nicht reserviert       }
\       XMMA20StillEn    = $94; { A20 ist immer noch frei    }
\       XMMOutOfMem      = $A0; { Kein Extended Memory mehr  }
\       XMMNoHandle      = $A1; { Kein Handle mehr frei      }
\       XMMInvHandle     = $A2; { Ungltiges Handle          }
\       XMMSrcHandleInv  = $A3; { Quell-Handle ungltig      }
\       XMMSrcOfsInv     = $A4; { Quell-Offset ungltig      }
\       XMMDestHandleInv = $A5; { Ziel-Handle ungltig       }
\       XMMDestOfsInv    = $A6; { Ziel-Offset ungltig       }
\       XMMLenInv        = $A7; { L„nge fr Move ungltig    }
\       XMMInvOverlap    = $A8; { Unerlaubte šberlappung     }
\       XMMParityErr     = $A9; { Parrity-Fehler aufgetreten }
\       XMMNotLocked     = $AA; { EMB ist nicht verriegelt   }
\       XMMLocked        = $AB; { EMB ist verriegelt         }
\       XMMLockCount     = $AC; { Verriegelungsz„hlerberlauf}
\       XMMLockFailed    = $AD; { Nicht zu verriegeln        }
\       XMMSmallerUMB    = $B0; { Kleinerer UMB verfgbar    }
\       XMMNoUMBAvail    = $B1; { Kein UMB mehr verfgbar    }
\       XMMInvUMB        = $B2; { Ungltige Segmentadresse   }

\ -- Prfen, ob Fehler aufgetreten
}
?XM     ( -- )

        Checks whether the most recently used XM-Operation leaved
        an errorflag ( see XMResult ). If so, aborts with a message.
{
: ?XM     XMResult 0= ?EXIT
        XMResult
        CASE
                $80 OF   true abort" XMS:Funktion nicht implementiert" ENDOF
                $81 OF   true abort" XMS:VDISK-Ger„t entdeckt"         ENDOF
                $82 OF   true abort" XMS:A20-Fehler eingetreten"       ENDOF
                $8E OF   true abort" XMS:Genereller Treiberfehler"     ENDOF
                $8F OF   true abort" XMS:Nicht-behebbarer Fehler"      ENDOF
                $90 OF   true abort" XMS:Kein HMA vorhanden"           ENDOF
                $91 OF   true abort" XMS:HMA ist bereits vergeben"     ENDOF
                $92 OF   true abort" XMS:HMA fr Prg. nicht verfgb."  ENDOF
                $93 OF   true abort" XMS:HMA nicht reserviert"         ENDOF
                $94 OF   true abort" XMS:A20 ist immer noch frei"      ENDOF
                $A0 OF   true abort" XMS:Kein Extended Memory mehr"    ENDOF
                $A1 OF   true abort" XMS:Kein Handle mehr frei"        ENDOF
                $A2 OF   true abort" XMS:Ungltiges Handle"            ENDOF
                $A3 OF   true abort" XMS:Quell-Handle ungltig"        ENDOF
                $A4 OF   true abort" XMS:Quell-Offset ungltig"        ENDOF
                $A5 OF   true abort" XMS:Ziel-Handle ungltig"         ENDOF
                $A6 OF   true abort" XMS:Ziel-Offset ungltig"         ENDOF
                $A7 OF   true abort" XMS:L„nge fr Move ungltig"      ENDOF
                $A8 OF   true abort" XMS:Unerlaubte šberlappung"       ENDOF
                $A9 OF   true abort" XMS:Parrity-Fehler aufgetreten"   ENDOF
                $AA OF   true abort" XMS:EMB ist nicht verriegelt"     ENDOF
                $AB OF   true abort" XMS:EMB ist verriegelt"           ENDOF
                $AC OF   true abort" XMS:Verriegelungsz„hlerberlauf"  ENDOF
                $AD OF   true abort" XMS:Nicht zu verriegeln"          ENDOF
                $B0 OF   true abort" XMS:Kleinerer UMB verfgbar"      ENDOF
                $B1 OF   true abort" XMS:Kein UMB mehr verfgbar"      ENDOF
                $B2 OF   true abort" XMS:Ungltige Segmentadresse"     ENDOF
                DUP . abort" XMS: unknown error"
        ENDCASE ;


\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Versionsnummer, interne Revisionsnummer sowie
\ Verfgbarkeit des HMA erfragen.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
XMSVersion   ( -- XMSVer Revision hma? )

        Checks if the High-Memory-Area ( FFFF:10 .. FFFF:FFFF)
        is available, and gives back the Version of the supported
        eXtended-Memory-Standard and the supporting program.
{
: XMSVersion   ( -- XMSVer Revision hma? )
  0 CallXM   xAX @  xBX @  xDX @ 1 = ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ HMA anfordern. TSR-Programme bitte den tats„chlichen
\ Bedarf anmelden, Applikation geben einfach $FFFF an.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
RequestHMA ( SpaceNeeded -- )

        Requests a portion of the HMA. Resident programs should
        use the really needed space, other applications may use
        $ffff.
{
: RequestHMA ( SpaceNeeded -- )  xDX !  1 CallXM  ErrChk ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ HMA wieder zurckgeben.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
ReleaseHMA      ( -- )

        Gives back the requested HMA to the system
{
: ReleaseHMA   2 CallXM  ErrChk ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Prozeduren zum Herumspielen mit der Adreáleitung A20...
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
GlobalEnableA20 ( -- )
GlobalDisableA20  ( -- )
LocalEnableA20  ( -- )
LocalDisableA20 ( -- )
IsA20On         ( -- flag )

        Routines to play around with the 21st Adress-Cable A20.
        The 8086 wraps around if you used adrresses above 1 Mb.
        To avoid compatibility-conflicts the AT-Hardware-Model
        supports this mirroring by a special cable. Your
        XMS-Manager enables adresses beyond the 1 Mb-adresses
        by reprogramming this 'cable'. In a few cases (I never
        saw one) it might be necessary to Disable the A20 to
        run an application which uses the wrap-around-effect
        of the A20.
{
: GlobalEnableA20   ( -- ) 3 CallXM ErrChk ;
: GlobalDisableA20  ( -- ) 4 CallXM ErrChk ;
: LocalEnableA20    ( -- ) 5 CallXM ErrChk ;
: LocalDisableA20   ( -- ) 6 CallXM ErrChk ;
: IsA20On ( -- flag )      7 CallXM ErrChk xAX @ 1 = ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Den gr”áten freien Speicherblock sowie den insgesamt
\ freien Speicherplatz ermitteln.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
XMSize?         ( -- Total Avail )

        Reports the Kilobytes of the biggest free xm-block (total)
        and the overall free xm-memory (avail).
{
: XMSize? ( -- Total Avail ) \ in kB
     8 CallXM  ( ErrChk ) 0 =: XMResult  xDX @  xAX @ ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Einen Extended Memory Block reservieren und ein Handle
\ zurckliefern, mit dem auf den Speicher zugegriffen
\ werden kann. Gewnschte Gr”áe in KBytes.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
XMAlloc         ( Size -- handle )

        Allocates Size kilobytes of xm and returns a handle.
        If you want its address, see XMLock .
{
: XMAlloc ( Size -- handle )
     xDX !  9 CallXM  ErrChk  xDX @ ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Den Speicherplatz (EMB) wieder an den EMM zurckgeben.
\ Der EMB darf nicht verriegelt sein.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
XMDealloc       ( Handle -- )

        Deallocates a block with is allocated by XMAlloc .
        It must not be locked with XMLock
{
: XMDealloc ( Handle -- )
   xDX !  $0a CallXM ErrChk ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Gr”áe des EMBs ver„ndern. Gr”áe in KByte
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
XMRealloc ( Handle NewSize -- )

        Changes a block (allocated by XMAlloc ) to NewSize kilobytes.
{
: XMRealloc ( Handle NewSize -- )
   xBX !   xDX !  $0f CallXM  ErrChk ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Speicherblock verschieben zwischen/in konventionellem
\ und Expanded Memory.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
\ The format of the used MoveStruct at StrucAddr is:
\ TYPE  MoveStruc = RECORD
\                  CASE INTEGER OF
\                    0: (BlockLength  : LONGINT;
\                        SourceHandle : WORD;
\                        SourceOffset : LONGINT;
\                        DestHandle   : WORD;
\                        DestOffset   : LONGINT);
\                    1: (xxxxxxxxxxx1 : ARRAY[1..6] OF BYTE;
\                        SourcePtr    : POINTER;
\                        xxxxxxxxxxx2 : WORD;
\                        DestPtr      : POINTER);
\                 END;
\
\

LXMMove          ( MStrucSeg MstrucOffs -- )

        Moves a block allocated with XMAlloc.
        For the format of the MoveStruc see above.
        The address of the MoveStruc is passed as SEG:OFFS

XMMove          ( MStrucaddr -- )

        Moves a block allocated with XMAlloc.
        For the format of the MoveStruc see above.
        The address of the MoveStruc is passed at offset
        within the F-PC-Data-Segment ( ?DS: ).
{
: LXMMove ( MStrucSeg MstrucOffs -- )
    xSI !  xDS !   $0b CallXM  ErrChk ;
: XMMove  ( MStrucaddr -- )               \ Format von MStruc ???
 \   ?CS: LXMMove ;                     \ wohl noch nie probiert???      \ mis94feb13
        ?CS: SWAP LXMMove ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Erweiterungen die die Benutzung von ùXMMove vereinfachen               \ mis94feb13
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ

CREATE xmov-parm   $10 ALLOT   xmov-parm $10 ERASE
        \ parameter block for extended memory move                       \ mis94feb13

\\ Note: Parameter block format:
        dword   length of block (bytes)
        word    source EMB handle
        dword   source offset
        word    destination EMB handle
        dword   destination offset

      If source and/or destination handle is zero, the corresponding
      offset is assumed to be a normal far pointer. The EMB need not
      be locked. The state of the A20 line is preserved.

      ( EXTENDING DOS, Second Edition, p.492)

{

: =XMOVLEN ( d -- )     \ store xms-move-len d into parameter block
        xmov-parm 2+ !  xmov-parm ! ;

: =XMOVSRC ( d n -- )   \ store source spec into xmov-parm parameter block
        sp@ xmov-parm 4 + 6 cmove 3drop ;

: =XMOVDEST ( d n -- )  \ store destination spec into xmov-parm
        sp@ xmov-parm 10 + 6 cmove 3drop ;

: REAL-ADDR ( seg ofs -- d n )
        \ convert real mode long address into the xms move
        \ parameter format, expected by ù=XMOVSRC and ù=XMOVDEST .
        0 ;

: EMB-ADDR  ( handle dofs -- d n )
        \ convert EMB 48-bit address into the xms move
        \ parameter format, expected by ù=XMOVSRC and ù=XMOVDEST .
        SWAP            \ convert Forth 32-bit number to Intel convention
        ROT ;

: XMOV  ( -- )
        ?CS: xmov-parm LXMMove ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Einen EMB verriegeln, so daá dieser nicht mehr
\ verschoben werden kann. Als Ergebnis wird die lineare
\ Adresse (32 Bit) zurckgeliefert.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
XMLock          ( Handle -- 2addr )

        Locks a XM-Block, for it can not be moved by the system.
        Returns a linear 32-bit-address, high word first.
{
: XMLock ( Handle -- 2addr )   \ returns linear (flat) 32bit-addr
    xDX !  $0c CallXM  ErrChk  xDX @  xBX @ ;  \ LockEMB := LONGINT(DX) SHL 16 + BX;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Zuvor mit LockEMB verr. Speicherbl. wieder freigeben.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
XMUnlock        ( Handle -- )

        Unlocks a XM-Block which is locked by XMLock.
        Use always XMUnlock it before XMDealloc.
{
: XMUnlock ( Handle -- )  xDX !  $0d CallXM ErrChk ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Informationen ber einen Extended Memory Block erfragen.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
XMInfo          ( Handle -- LockCount FreeHandles BlockLength )

        Returns information about an allocated XMBlock.
{
: XMInfo ( Handle -- LockCount FreeHandles BlockLength )
   xDX !  $0e CallXM ErrChk
   xBX 1+ C@    \ high byte
   xBX C@       \ low byte
   xDX @ ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Speicher "zwischen den Adaptern" anfordern. Gewnschte
\ Gr”áe in Paragraphen. "NewSize" gibt bei Fehlern die
\ L„nge des gr”áten freien UMBs an, ansonsten die Gr”áe
\ des gerade reservierten Blocks. Zurckgegeben wird als
\ Funktionsergebnis die Segmentadresse des Blocks.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
UMBAlloc        ( Size -- Size' segment error )

        Allocates a block of Size*16 bytes between the adapters,
        which is the adress-range from 640 kB to 1 Mb.
        If an error occured ( error<>0 ) Size' returns
        the length of the biggest free block and segment is
        undefined. Otherwise Size' equals Size and segment
        points to the allocated block.
{
: UMBAlloc ( Size -- Size' seg error )
    xDX !   $10 CallXM  ErrChk  xDX @  xBX @ XMResult @ ;

\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
\ Einen UMB wieder zurckgeben.
\ ÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ
}
UMBDealloc      ( Address -- )

        Gives the allocated UMB back to the system.
{
: UMBDealloc ( Address -- )  xDX !  $11 CallXM  ErrChk ;

cr .( XMS Support has been loaded, but NOT initialized!)
cr .( Type: ) loadline @ 2+ . .(  LOAD to see a demo of XMS support!) cr
\\ ***************** From here down is test code **************************
\ -- initializing:

XMInit

\ -- test and messages


CR .( XMS loaded )

XMAvail
#IF
        BASE @ HEX
        .( - xmsVersion :) xmsVersion ?XM .(  HMA avail?:) .
        swap  .(   rev:) .  .(  release:) .
        BASE !
        CR .(            - FreeXMSize :) XMSize? ?XM
        swap .(  Total[Kb]:) . .(  Avail[Kb]:) .
#ELSE
       .( - no XMS-Manager available )
#THEN

XMAvail 0=                                                               \ mis94feb14
#IF    CR .( aborting test... )
        \s
#ELSE  CR .( - test the XMOV extensions - mis94feb14 )                   \ mis94feb14

variable xmhndl
2048. pointer xtestbuf
xtestbuf 0=
#IF
        CR .( couldn't allocate real mode memory! aborting test... )
        \s
#ELSE
        xtestbuf 0 2048 0 lfill         \ clear test buffer
#THEN

2 XMAlloc xmhndl !
XMResult
#IF
        CR .( couldn't allocate extended memory block! aborting test... )
        CR .( see error code in XMResult. )
        \s
#THEN

2048. =XMOVLEN
yseg @ ' BASE >NAME     \ seg:ofs of 'BASE' in the header dictionary
REAL-ADDR =XMOVSRC      \ this memory is easy to dump
xmhndl @ 0.0            \ emb:0.0
EMB-ADDR =XMOVDEST
XMOV                    \ move it to extended memory

\ 2048. =XMOVLEN        \ same len to move back
xmhndl @ 0.0 EMB-ADDR =XMOVSRC
xtestbuf 0 REAL-ADDR =XMOVDEST
XMOV                    \ now move back to real mode memory

vocoff                  \ dump to see anything

CR .( source to move: header dictionary, beginning with the name BASE...)
YSEG @ ' BASE >NAME 48 LDUMP
CR .( moved to extended memory and back to XTESTBUF in real mode memory...)
xtestbuf 0 48 ldump

xmhndl @ XMDealloc

#THEN


\\ -- abort-test

XMAvail 0=
#IF
        .( - XMM is not available)
        .( Testing Abort: )
        CR FreeXMSize ?XM 2drop
#THEN


}
