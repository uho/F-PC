# F-PC - a Forth system optimized for IBM-PC, XT & AT machines running DOS

This repository holds an installation of the 1994 16Bit DOS Forth system 
F-PC by Tom Zimmer for conservation, reference and experimentation.

## Inspect it

You can find introductory texts and the system executable `F-PC.EXE` in the
directory `fpc`. The subdirectory `fpc/src` has the complete system source code
including the F-PC meta compiler.

## Run it

If you want to run F-PC on a modern computer you can do so by using a
DOS emulator such as [DOSBox](https://www.dosbox.com/) or 
[DOSEMU](http://www.dosemu.org/). 
If you use [Docker](https://www.docker.com/) you can 
run F-PC with the supplied shell script

    ./f-pc

that essentially uses DOSEMU inside Docker and launches F-PC on the 
`fpc` directory.

## Download the original

You can download the original 
[F-PC 3.6 archive](http://www.forth.org/library/eforth_SOC/eforth_SOC_source/fpc/fpc36.zip) 
from the [Public Domain Forths](http://www.forth.org/eforth.html) page
at the [Forth Interest Group](http://www.forth.org) web site,
F-PC related files from the
[Taygeta Forth Archive](ftp://ftp.taygeta.com/pub/Forth/Applications/fpc).

----

Feedback, enhancements and questions are welcome.

May the Forth be with you.

Ulrich Hoffmann

### The original F-PC README

>   12/18/94             Release Notes FPC version 3.6000
> 
>   What is F-PC you may ask. F-PC is a Forth system optimized for the
> IBM-PC, XT & AT machines. F-PC can use all of your machines memory for
> programs and data. F-PC uses stream (text) files for its source, and
> includes a built-in WordStar-like text editor with pull down menus. Many
> tools are provided to make writing programs with F-PC fun and efficient.
> A very good Laser printed users manual is available for a small
> additional cost from OFFETTE ENTERPRISES. F-PC is a public domain
> package.
> 
>   FPC 3.6000 is the final update to fpc 3.50.  The only significant
> changes are a minor bug fix in the software floating point package, and
> the removal of the one year version old check.  This final version
> performs no out of date check.
> 
>   MAKE SURE YOU HAVE A COUPLE OF MEGABYTES OF FREE SPACE ON YOUR HARD DISK,
> type the following command and follow the instructions:
> 
>         A:INSTALL <enter>       be sure to put disk #1 in drive A:
> 
>   INSTALL will ask you where to install F-PC. A directory will then be
> created to hold the F-PC files. If this bothers you, read INSTALL.TXT.
> 
> Comments to:    Tom Zimmer              (408) 263-8859  home
>                 292 Falcato Drive       (408) 432-4643  work
>                 Milpitas, Ca. 95035
