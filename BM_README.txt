THE PURPOSE OF THIS WORK:

I wanted to explore writing some simple standalone programs
for the VM/370 environment that you can IPL from the card
reader. I'm using the current Community Edition of VM/370 and
the CMSUSER VM (you can use any virtual machine you like).


All of this work is built on the knowledge I obtained from:

Tommy Sprinkle's "Bare Metal Programming on IBM 370" Tutorial
http://tommysprinkle.com/txxos/

Tommy does such a great job of explaining in great detail how to
get started with S/370 Bare Metal Programming I will not attempt
to explain any of the basics here. I will refer you to his work.

----------------------------------------------------------------

The AWS tape in this REPO is in VMFPLC2 format.
Just ATTACH a tape to CMSUSER (or whatever machine you are using)
as 181 and issue the following to load to disk:

VMFPLC2 LOAD * * D    (or whatever disk you want).

----------------------------------------------------------------

The BMLIB MACLIB will need to be incorporated into the 
MACLIB statement in your PROFILE EXEC
  
----------------------------------------------------------------

Look at the SABUILD EXEC to see how a standalone module is
created. Essentially the ABSLOAD bootstrap code (file ABSLOAD IPL) 
is concatenated to the TEXT output from the assembly of your bare
metal standalone program. This file (with file type SAIPL) is then
PUNCHed to the 00C virtual reader. After this you are free to 
perform an IPL 00C to IPL your program.

----------------------------------------------------------------

Anytime you want to make changes to the bootstrap code (normally
you don't need to) perform the following steps:
a) Edit ABSLOAD ASSEMBLE
b) ASSEMBLE ABSLOAD
c) LOAD ABSLOAD ( START
d) ERASE ABSLOAD IPL fm
e) RENAME FILE PUNCH fm ABSLOAD IPL fm
f) Issue the SABUILD EXEC to rebuild your standalone program

----------------------------------------------------------------

In order to run the programs that access the interval timer, you
will need to use ECPSVM NO in your Hercules config and then in 
CMSUSER issue CP SET TIMER REAL before trying to IPL those programs. 
A CP Q SET should indicate 'TIMER REAL' and 'ASSIST OFF'.

----------------------------------------------------------------

In order to run ITIMRCLK you need a dedicated 3270 device at 
address X'0C1'. Perform the following:

a) Launch an emulator session
b) See 0C1 connected in the Hercules console.
c) /disable 0C1  (on Hercules console)
d) /att 0C1 to CMSUSER  (on Hercules console)
e) SABUILD ITIMRCLK (or LOADRDR ITIMRCLK if you built it previously)
f) IPL 00C

