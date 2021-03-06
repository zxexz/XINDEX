.. title: OSEHRA XINDEX

==============
OSEHRA XINDEX
==============

This source tree holds a reference copy of M components for XINDEX, the
tool designed for reporting cross-references and compliance with the SAC.  It
is maintained by OSEHRA, the Open Source Electronic Health Record Alliance.

-------
Purpose
-------

It has been found that XINDEX doesn't run or report on all VistA components
that can be contained within a KIDS build. This creates errors of omission
when trying to find other code references or compliance with the SAC.

Opportunity was taken during this development to incorporate VistA community
feedback and enhancements on XINDEX.

---------------
Note on Release
---------------
As of August 2019, the enhanced XINDEX has two builds: XT*7.3*10001 and
XT*7.3*10003. The latter build contains minor bug fixes for bugs/omissions
discovered after the final release of XT*7.3*10001. You can install
XT*7.3*10001 and then XT*7.3*10003 by themselves or install the multibuild that
contains both of them. It's completely safe to re-install.

* `XT*7.3*10001 <https://github.com/OSEHRA-Sandbox/XINDEX/releases/download/XT-7.3-10001/XT-7p3-10001T4.KID>`_
* `XT*7.3*10003 <https://github.com/OSEHRA-Sandbox/XINDEX/releases/download/XT-7.3-10003/XT-7p3-10003.KID>`_
* `Multibuild <https://github.com/OSEHRA-Sandbox/XINDEX/releases/download/XT-7.3-10003/XT-7p3-10001-10003-MB.KID>`_

This README is for all of XINDEX. `Release notes specifically for XT*7.3*10003
<Packages/Toolkit/Documentation/xt-7p3-10003/README.rst>`_
are detailed in a separate document in the documentation folder.

--------------------
Install Instructions
--------------------
From the links above, grab the KIDS build. Install that KIDS build into a VistA or RPMS system.
Here's an example of an install transcript for the Multibuild (\*\* means type the following \*\*)::




    $ **$gtm_dist/mumps -r ^%XCMD 'S DUZ=1 D DUZ^XUP(DUZ),HOME^%ZIS,^XPDIL,^XPDI'**

    Enter a Host File: **./XT-7p3-10001-10003-MB.KID**

    KIDS Distribution saved on Jul 31, 2019@08:49:03
    Comment: XINDEX Multibuild (contains all of XINDEX + fixes) T2

    This Distribution contains Transport Globals for the following Package(s):
       XT*7.3*10001
       XT*7.3*10003
    Distribution OK!

    Want to Continue with Load? YES// **enter**
    Loading Distribution...

       XT*7.3*10001
       XT*7.3*10003
    Use INSTALL NAME: XT*7.3*10001 to install this Distribution.

    Select INSTALL NAME: **XT*7.3*10001**       Loaded from Distribution    7/31/19@14:5
    6:11
         => XINDEX Multibuild (contains all of XINDEX + fixes) T2  ;Created on Jul

    This Distribution was loaded on Jul 31, 2019@14:56:11 with header of 
       XINDEX Multibuild (contains all of XINDEX + fixes) T2  ;Created on Jul 31, 20
    19@08:49:03
       It consisted of the following Install(s):
       XT*7.3*10001   XT*7.3*10003
    Checking Install for Package XT*7.3*10001

    Install Questions for XT*7.3*10001


    Checking Install for Package XT*7.3*10003

    Install Questions for XT*7.3*10003



    Want KIDS to INHIBIT LOGONs during the install? NO// **enter** 
    Want to DISABLE Scheduled Options, Menu Options, and Protocols? NO// **enter** 


-----------
Development
-----------

You can mount a volume into the container to share the routines instead of
transferring them using sftp or only modifying the routines in the container.

To run the image:

    docker run -p 9430:9430 -p 8001:8001 -p 2222:22 -v $(pwd)/Packages/Toolkit/Routines:/home/osehra/p -d -P --name=xindex krmassociates/osehravista

-----------
What's New
-----------

The XINDEX code here is based on the latest VA code which was relased in patch
XT*7.3*140 obtained from https://hdl.handle.net/10909/11315.

The changes made here can be dividied into enhancements for XINDEX for it to be
able to find and index all M code in VistA; Fileman Templates Pointer Analysis;
and other enhancements to XINDEX, mostly bug fixes.

The authors will be referenced by their initials. Here is a list of all of them:

* Christopher Edwards; CJE (main author)
* Sam Habiel; SMH (secondary author; corresponding author for questions)
* Jason Li; JL (contributor)
* David Whitten; DJW (contributor)
* John McCormack; JM (contributor)
* George Timson; GFT (contributor)

M-Code-Finding Expansion
========================
XINDEX has now been expanded to cover the following which XINDEX didn't cover
before.

Fileman Coverage (CJE)
----------------------
XINDEX coverged a lot of Fileman code frameworks, but not all. It now does 
cover all allowable M code extension points that Fileman executes. The following
is new:

* Variable pointer screens in ^DD(file,field,.12,1)
* Expression for pointer screen in ^DD(file,field,12.2)
* Special Lookup code in ^DD(file,0,"DIC")
* Post-Action code in ^DD(file,0,"ACT")
* New Style Indexes
* M code embedded in Print Templates
* M code in the Sort Template file (#.401)--embedded code in Sort Tempaltes is very rare: only a single $DATA check was found in all of FOIA.
* M code embedded in Input Templates

The first 5 bullets are shown under the \|ddnnn faux routine. Print, Sort, and
Input templates are shown under faux routines \|prnt, \|sort, and \|inpt.

Fileman Data Coverage (SMH)
---------------------------
XINDEX now checks Fileman data for items that can be xindex'ed. These are
either fields marked as having M code or a field whose label contains the word
"ROUTINE" which has a field before or after it with a label that contains the
word "TAG". E.g., on file 3.2::

	|dd3.2        * *  1399 Lines,  55619 Bytes, Checksum: 
         S ^%ZIS(2,"B",$E(X,1,30),DA)=""
   .01XRF1S+1   S - Set to a '%' global.
         50
   5DATA77+1    F - UNDEFINED COMMAND (rest of line not checked).
         S $X=250 W $C(27)_$C(91)_(DY+1)_$C(59)_(DX+1)_$C(72 S $Y=DY)_$X=DX
   5DATA167+1   F - Unmatched Parenthesis.
   5DATA167+1   F - Invalid or wrong number of arguments to a function.
   5DATA167+1   F - Unmatched Parenthesis.

Routine print-outs have a label indicating in a verbose way which entry we are
talking about, plus code::

	 2DATA1,360 --
         ; Data file 3.2055, field 2, IENS 1,360
         W "^XA"

See the `Sample Output <Packages/Toolkit/Documentation/sample_output/>`__ folder for a full example of XINDEXing the TERMINAL TYPE
file (#3.2).

Other Component Coverage (CJE)
------------------------------
These components that have M code were previously not analyzed by XINDEX:

* Forms (in faux routine \|form)
* Dialogs ( " " " \|dlg)
* Help Frames ( " " " \|help)
* Security Keys ( " " " \|key)
* Protocols ( " " " \|ptcl)
* List Templates ( " " " \|list)
* HL7 Application Parameters ( " " " \|hlap)
* Remote Procedures ( " " " \|rpc)

Remarks on how this is accomplished (CJE)
-----------------------------------------
There is a new select by namespace prompt and a new select file prompt. This
lets users choose to analyse a whole namespace. BUILD, INSTALL, PACKAGE prompts
will analyse all components in the BUILD file (for both builds and installs)
and will analyze all Package elements by the namespace obtained from the
package file.

Fileman Template Pointer Analysis (SMH)
=======================================
This is the routine XINDX13, which has the entry points DIBT, DIET, and DIPT,
that produce CSV files called respectively DIBTOUT.csv, DIETOUT.csv,
and DIPTOUT.csv. Here's some sample output::

  SORT TEMPLATE IEN,SORT TEMPLATE NAME,SOURCE FILE,DESTINATION FILE,DESTINATION FIELD
  224,RT ALL INPATIENTS,2,190,4
  223,RT WARD LIST,2,195.2,15
  8,XUOPTLOGS,19.081,19,.01
  104,DGWAIT LIST,42.5,42.51,5

The output is used to figure out if a source file calls to a destination file
that is not itself and what field is the destination file is called in the
template. The second line (i.e. first data line after the header) says that RT
ALL INPATIENTS is a sort template based on File 2 (PATIENT) but in the process
of doing the sort, a reference is made to file 190 (RECORDS) field 4
(APPLICATION). The output for print and input templates follows the same
pattern.

It's worth noting for experienced readers that the templates analyse computed
expressions as well, not just upright fields.

The entry points are:

* ALL^XINDX13(path) - Run all the entry points.
* DIBT^XINDX13(path,filename) - Export CSV of sort templates
* DIET^XINDX13(path,filename) - Export CSV of input templates
* DIPT^XINDX13(path,filename) - Export CSV of print templates

In all of these entry points, if path is not specified, then the value of
``$$DEFDIR^%ZISH()`` is used; and if filename is not specific, the default
filenames of ``DIBTOUT.csv``, ``DIETOUT.csv``, and ``DIPTOUT.csv`` are used.

Other Enhancements to XINDEX
============================
* XINDEX [p10003] Routine name printer when collecting routines now assumes
  they are ≤16 characters long (DJW)
* XINDX1 [p10001] Added error 65: ;;X,Z,DI,DD,KMP;S - Vendor specific code is
  not allowed (SACC 2.2.8) (SMH)
* XINDX2 [p10001] Modified to not crash on non-Cache systems when trying to
  evaluate the existence of Cache classes (JL)
* XINDX6 [p10001] allows you to choose "F" for diagramming control flow (DJW)
  XINDEX already supported this but did not show "F" as an option when printing
  routines
* XINDX8 [p10001] ':' (post-conditionals) show as a lowercase "if" in
  structured listing of routines (DJW)
* XINDX9 [p10001] Lower case ISV's are now recognized as valid M syntax (SMH)
* XINDX9 [p10001] Cache Objects will trigger error #65, but only for non-Kernel
  packages (SMH)
* XINDX9 [p10003] Don't uppercase labels in routines (SMH)
* XINDX5 [p10001] Tags can be 16 characters long according to latest SAC (JM)
* XINDX3 [p10001] R $$ or R $P is invalid syntax (GFT)
* XINDX7 [p10001] Update VA Standards and Conventions tag line with correct
  version of the current SAC that this XINDEX enforces (SMH)
* XINDX7 [p10001] ^$PDISPLAY is not a standard SSVN (GFT)
* XINDX7 [p10001] Fix spelling on environment (CJE)
* XINDX10 [p10003] Check for call to ^DIM as wel to determine if field contains
  M data (DJW)
* XINDX51 [p10001] Routine name can be up to 16 characters long in the header
  (GFT)
* XINDX51 [p10001] Print Fileman documentation file in routine listings if it
  is present (GFT)

----------------
Future Work List
----------------

* All new items, plus \|opt, print as a single faux routine. Consider making each internal entry number a faux routine. E.g. \|opt123.
* Create pexpect based Unit Tests.
* Warn on use of && or ||
* Recognize $ROLES and $NAMESPACE as vendor specific constructs
* Better parsing for COS in general (See PSSHTTP for an example that fails)

----------
Unit Tests
----------

No Unit Tests are supplied in this release. The following are manual testing instructions:

To exercise the enhanced finding of M-code snippets in the database, follow the example in `here <Packages/Toolkit/Documentation/sample_output/request_to_index_all_of_XOBW.txt>`_. See usage instructions below for more details.

To exercise the tracing of pointers in Fileman templates, run ``D
ALL^XINDX13``.

------------------------
Brief Usage Instructions
------------------------
Most of how XINDEX is used has not changed. Refer to the XINDEX section in https://www.va.gov/vdl/documents/Infrastructure/Kernel/krn8_0dg.pdf.

The following prompts are new. If you want all KIDS components to be analysed, use the Namespace option. If you want files as well, use files::

  LIST OF NAMESPACES TO BE INDEXED; PRESS RETURN TO TERMINATE LIST

  NAMESPACE: **XOBW**
  NAMESPACE:

  LIST OF FILES TO BE INDEXED; PRESS RETURN TO TERMINATE LIST

  FILE: **18.02**
  FILE: **18.12** 
  FILE: **18.13**
  FILE: 

This prompt has been modified to include (F)low::

  Print (R)egular,(S)tructured or (B)oth or Control (F)low ?  R//

The text for this yes/no prompt has been modified to add "and other package code" as now XINDEX does more than functions and options::

  Print the DDs, Functions, Options, and other package code? YES//

XINDEX's output has been added to DD has been expanded to accomodate extra fields preiovusly not accounted for; and the new components generate these additional faux routines:

* Forms (in faux routine \|form)
* Print Templates ( " " " \|prnt)
* Sort Tempaltes ( " " " \|sort) 
* Input Templates ( " " " \|inpt)
* Dialogs ( " " " \|dialog)
* Help Frames ( " " " \|help)
* Security Keys ( " " " \|key)
* Protocols ( " " " \|ptcl)
* List Templates ( " " " \|list)
* HL7 Application Parameters ( " " " \|hlap)
* Remote Procedures ( " " " \|rpc)

Here is some sample output. \*\*\* means that this output is new. If a whole section is new, then \*\*\* will only show up on the section header. See the `Sample Output <Packages/Toolkit/Documentation/sample_output/>`__ folder for a full example of XINDEXing the Health-e-Vet Web Services package. The sample below does not include the XINDEX and error listing for the faux routines for brevity's sake.::


  |dd18.12        * *  41 Lines,  1452 Bytes, Checksum: 
                                                    Feb 23, 2018@12:47:25 page 21
                389 bytes in comments
  |dd18.12 ;DD of the WEB SERVER file.
  ***IXPRIMARYSL --
            ; SET LOGIC
           S ^XOB(18.12,"PRIMARY",X,DA)=""
  ***IXPRIMARYKL --
            ; KILL LOGIC
           K ^XOB(18.12,"PRIMARY",X,DA)
  ***IXPRIMARYKEIC --
            ; KILL ENTIRE INDEX CODE
           K ^XOB(18.12,"PRIMARY")
  .01      ; NAME
           K:$L(X)>30!($L(X)<3)!'(X'?1P.E) X
  .01XRF1S ; SET LOGIC FOR 'B' XREF
           S ^XOB(18.12,"B",$E(X,1,30),DA)=""
  .01XRF1K ; KILL LOGIC FOR 'B' XREF
           K ^XOB(18.12,"B",$E(X,1,30),DA)
  ...
  ...
  ...
  ***|inpt        * *  15 Lines,  403 Bytes, Checksum: Feb 23, 2018@12:47:25 page 24
                151 bytes in comments
  |inpt    ; '' Input Templates.
           ;
  1749     ; XOBW WEB SERVER KEY SETUP - EXECUTABLE CODE
           ;
  1747     ; XOBW WEB SERVER SETUP - EXECUTABLE CODE
           ;
           WRITE !!,"Security Credentials"
           WRITE !,"===================="
           IF +X=0 SET Y="@100"
           IF '$$SSLOK^XOBWENV() SET Y="@200"
           WRITE !!,"SSL Setup"
           WRITE !,"========="
  1748     ; XOBW WEB SERVICE EDIT - EXECUTABLE CODE
           WRITE !,"========="
           IF X'=1 SET Y=200
  ...
  ...
  ...
  ***|list        * *  50 Lines,  1516 Bytes, Checksum: 
                                                    Feb 23, 2018@12:47:25 page 25
                1031 bytes in comments
  |list    ; '' List Templates.
           ;
  666      ; XOBW WEB SERVER - HEADER CODE (#100)
           DO HDR^XOBWU
           ; XOBW WEB SERVER - EXPAND CODE (#102)
           DO EXPAND^XOBWU
           ; XOBW WEB SERVER - HELP CODE (#103)
           DO HELP^XOBWU
           ; XOBW WEB SERVER - EXIT CODE (#105)
           DO EXIT^XOBWU
           ; XOBW WEB SERVER - ENTRY CODE (#106)
           DO INIT^XOBWU
           ; XOBW WEB SERVER - ARRAY NAME (#107)
           I $L(^TMP("XOB) Q
  669      ; XOBW WEB SERVER LOOKUPKEY - HEADER CODE (#100)
           DO HDR^XOBWUA
           ; XOBW WEB SERVER LOOKUPKEY - EXPAND CODE (#102)
           DO EXPAND^XOBWUA
           ; XOBW WEB SERVER LOOKUPKEY - HELP CODE (#103)
           DO HELP^XOBWUA
           ; XOBW WEB SERVER LOOKUPKEY - EXIT CODE (#105)
           DO EXIT^XOBWUA
           ; XOBW WEB SERVER LOOKUPKEY - ENTRY CODE (#106)
           DO INIT^XOBWUA
           ; XOBW WEB SERVER LOOKUPKEY - ARRAY NAME (#107)
           I $L(^TMP("XOB) Q
  667      ; XOBW WEB SERVICE - HEADER CODE (#100)
           DO HDR^XOBWUS
           ; XOBW WEB SERVICE - EXPAND CODE (#102)
           DO EXPAND^XOBWUS
           ; XOBW WEB SERVICE - HELP CODE (#103)
           DO HELP^XOBWUS
           ; XOBW WEB SERVICE - EXIT CODE (#105)
           DO EXIT^XOBWUS
           ; XOBW WEB SERVICE - ENTRY CODE (#106)
           DO INIT^XOBWUS
           ; XOBW WEB SERVICE - ARRAY NAME (#107)
           I $L(^TMP("XOB) Q
  668      ; XOBW WEB SERVICE DISPLAY - HEADER CODE (#100)
           DO HDR^XOBWUS2
           ; XOBW WEB SERVICE DISPLAY - EXPAND CODE (#102)
           ;
           ; XOBW WEB SERVICE DISPLAY - HELP CODE (#103)
           DO HELP^XOBWUS2
           ; XOBW WEB SERVICE DISPLAY - EXIT CODE (#105)
           DO EXIT^XOBWUS2
           ; XOBW WEB SERVICE DISPLAY - ENTRY CODE (#106)
           DO INIT^XOBWUS2
           ; XOBW WEB SERVICE DISPLAY - ARRAY NAME (#107)
           ;

-----
Links
-----

* OSEHRA Homepage: http://osehra.org
* OSEHRA Repositories: http://code.osehra.org
* OSEHRA Github: https://github.com/OSEHRA
