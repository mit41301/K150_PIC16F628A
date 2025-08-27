EXPANSION PACK V2 FOR DIY PROGRAMMERS K128, 149, 150 and K182
-DRAFT COPY. POSSIBLY UPDATES TO FOLLOW

This expansion pack is copyright 2008 DIY ELECTRONICS, and Jim Robertson.
Use of this package is intended for genuine DIY programmers and no permission 
is granted for use with unauthorized copies that include the "enhanced k149" or 
copies that are made on RED PCBs. 

HIGHLIGHTS.

This Version 2 of the DIYPACK25 EXPANSION PACK has the following improvements:

FIRMWARE:

The firmware is updated to adhere far more closely to the latest revisions of
Microchips programming specs. It also changes some timings to mimic the 
timings used by Microchip even though there is not a clear reason for these
differences. The change in timings can only possibly benefit and do no harm 
so they have been adopted. The new firmware is a maintenance update to the 
original DIYPACK25-EP firmware and does not alter the protocol in anyway or
add further functionality to what has just been described.

CHIPDATA FILE:

The CHIPDATA file has been VERY extensively corrected and a few parts added 
and a nonexistent part removed (16C71A). 

As far as practical, entries have been standardized and fuse listings are now
in a more consistent format. This allows for easier location of options, easier
maintenance and expansion. (Some items had SIX or more different names!)


Items are now listed in the following format.

1) CONFIG WORD ADDRESS (Numerically)
2) ALPHABETICALLY

The order moves ACROSS the FUSE setting dialog form, not downward. 

For the PIC18 parts the FUSE bytes are combined into 16-bit words.

I.E. Microchip convention of Config1L and Config1H = FUSE1 in microbrn
and the chipdata file. Not ideal but this was forced on us due to the 
legacy design of micropro.


USERS PLEASE NOTE, the order of some of the FUSE selections has changed to 
be consistent with all other entries. The highest value setting is now the topmost 
setting in the FUSE settings drop down list boxes. Before a small number were 
listed from lowest to highest. Also, many of the duplicate settings are now 
included were as before they were not. This may not be true in every case, the 
chipdata file is not purely consistent, just substantially more so. The end 
result of this is that users must check there own selections as some settings 
are in different places in the drop down list boxes. 

In any case you should really aim to be setting all your config word ("FUSEs") 
options in your source code and not through micropro. 

Other changes to the CHIPDATA file include the addition of missing FUSE settings
and the deletion of incorrect settings copied over from similar but not entirely
the same, PICs and a range of other stuff too, BORV values, PIC18 code protect
address ranges etc.

FIXHEX3:

FIXHEX3 is a vital update from FIXHEX 1&2 These two earlier versions are not to 
be considered serviceable and should be deleted from your hard drive immediately!

FIXHEX1 contains a nasty bug that can cause it to write out corrupt lines. 

Take this actual example from the new 18F1320 based firmware under development.

Original output from MPASM:

:020000040030CA
:0300010006061ED2  <- Odd byte count line
:020005008081F8
:06000800008003A00000CF

Output from FIXHEX 1:

:020000040030CA
:04 0001 00 06 06 1F F B0 <- Look, it's missing a character and has changed 1Eh to 1Fh!
:020005008081F8
:06000800008003A00000CF

FIXHEX2 creates the following output:

:020000040030CA
:0400010006061EFFD2 <- Odd byte count successfully corrected. BUT....!
:020005008081F8
:06000800008003A00000CF

There is an addition problem with microbrn that was NOT KNOWN when FIXHEX2 
was created. Notice that the middle two lines ALSO HAVE ODD ADDRESSES...

:020000040030CA
:04 0001 <- Here         0006061EFFD2
:02 0005 <- And here     008081F8
:06000800008003A00000CF

Now, here's the killer. Microbrn will load these lines and not generate an error,
but the problem is that the lines are loaded incorrectly (an additional byte is 
inserted where it should not be) and completely corrupt the config word settings. 
This is a real disaster and I have no doubt that this was causing some PIC18F
projects to NOT WORK! 

I suspect this problem arose when microchip specified the new CONFIG directive to 
replace the depreciated __Config directive. 

Personally, I can only apologize for not picking this up earlier. I was shocked to 
discover this and have rushed to offer a fix-up ASAP. 

FIXHEX3

FIXHEX3 takes a new approach to the ODD BYTE COUNT AND ODD LINE ADDRESS 
problem. The code has been completely rewritten to load the entire HEX file into memory
and then save it out again. The HEX file parser reads in a BYTE orientated mode 
that accepts ODD BYTE COUNTS and ADDRESSES and writes its output in a EVEN word 
orientated mode that micropro/microbrn expect.

Here is an example of a FIXHEX3 correction to the above input problem.

:020000040030CA
:0E0000000006061E00808100008003A00000A4

This is how MPASM used to write PIC18 config values and it is what micropro 
expects and loads correctly. 

There are some other improvements to FIXHEX3 too. 

Settings are now saved and reloaded each time you run FIXHEX3.

Additionally it can remember the last loaded file and reload that and do the 
conversion according to your options automatically. This makes it far easier
and more convenient to use.

Another hidden advantage of FIXHEX3 is that it will write out to the highest 
address loaded (actually the next multiple of 16) and this can be used to 
snuff out the DATA EEPROM corruption that occurs with micropro when there 
is 
code in the address range of 4200h - 42FFh. 

To do this include the following in your source code.

         ORG  0xF000FE
         (Or 0x0007E or 0x003FE depending on DATA EE size.)
         
         DE      0xFF, 0xFF <- Or any values that you want.
         
The aim is to have something (anything) in the last DATA EEPROM location. This
will cause FIXHEX3 to write out the entire DATA EEPROM space with what you 
define in it and 0xFF for undefined locations. This then preserves the DATA 
EEPROM space the way it should be and not filled with junk in undefined 
locations. 
         
FIXHEX3 will automatically detect HEX files for the PIC18 devices and only 
alter these files. It is not applicable for the Baseline and Midline PIC
families and takes no action.

FINALLY

All this work has been done to give you a more serviceable programmer. I have
not asked for, or received any sort of remuneration for this additional work.
As such it is done on a all care but no responsibility basis and it is up to 
you to check that all is as you want it to be. Hopefully the changes to the 
chipdata file and the new FIXHEX3 will help you in your endeavors. Feedback via 
the DIY forum is welcome. 

Jim Robertson Oct 15th 2008

End of document. 
