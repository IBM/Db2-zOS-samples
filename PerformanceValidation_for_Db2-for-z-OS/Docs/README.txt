The IBM DB2 Workload Generator V2.2 (GLW)
=========================================

PROPERTY OF IBM;(C) COPYRIGHT 2005 IBM CORP. ALL RIGHTS RESERVED.

Authors: Mike Bracey, IBM UK Ltd, mike_bracey@uk.ibm.com
         Steve Speller, Rocket Software, steve.speller@rs.com
Date   : January 2011

This code is provided on an asis basis with no warranty expressed
or implied.

Any feedback would be welcome.
Comments on the workload generator to mike_bracey@uk.ibm.com
and on the ISPF interface to steve.speller@rs.com.

Note: <glwdsp> is short hand for the dataset prefix that you choose for
your installation. It can consist of one or more qualifiers.

Introduction
============

The purpose of this tool is to create and drive a workload on a
DB2 for z/OS database.
The database can be manipulated and cloned using the
DB2 Administration Tool and the DB2 Object Comparison Tool.
The workload can be examined using IBM Tivoli Omegamon for DB2
Performance Expert for z/OS, DB2 Query Monitor
and DB2 SQL Performance Analyzer. Access paths can be explained
and optimised with Optim Query Workload Tuner.

The generator is designed to be run from either TSO Batch or Windows
though the only action permitted from within Windows is RUN.

The tool has been installed on DEMOMVS (hlq DMSYS) at Dallas
and ZT01 (hlq DB2CFG), the EMEA Ztec system at Montpellier.

You can run the driver program, which is written in rexx,
either as a batch program or from your workstation.
Sample JCL to run the rexx can be found in the
<glwdsp>.SGLWSAMP(GLWRUN) dataset.
Copy this to your own JCL library and amend the parameters
as required.

You can download the rexx code to the workstation from
<glwdsp>.SGLWEXEC(GLWRUN).

Note that you will need to install Object Rexx for Windows from ISSI
to run the tool on the workstation and also that the RUN action is
the only option available from the workstation.

The tool creates a database, collection, and schema for the stored
procedures according to your chosen schema name such as DBAnnnD
or DBAnnnP or whatever name you choose.  You can choose a number
of options via the JCL or ISPF panels.
The database structures at BUILD time:
1) empty or preloaded tables (PRELOAD)
2) prime key and RI indexes only or additional indexes (PERFLEV)
3) compression on or off (COMPRESS)
4) triggers or application code (TRIGGER)
5) Data capture on or off (DATACAP)
6) DB2 or application referential integrity (BUILDRI)
7) GDG limit for image copy datasets (GDGLIMIT)
8) Explain yes or no (EXPLAIN)
9) Audit option on CREATE TABLE (AUDIT)
10) Compiled C or native stored procedures (STORPROC)
11) Level of RUNSTATS (RSTATLEV)
12) Image Copy of indexes allowed (IXCOPY)
13) SQLID for authorisation (SQLID)
14) High level qualifier for utility work datasets (UTILHLQ)
Run time options:
1) Run duration in minutes or number of SP calls (RUNTIME)
2) Repeatable or random sequence of stored procedure calls (RUNMODE)
3) Relative rate of calls for each stored procedure (RUNPROF)
4) Wait time between each stored procedure call (WAITTIME)

Having created the database, you can choose to run the workload
immediately, which can then be monitored using either of Omegamon
Performance Expert of DB2 Query Monitor

Please note that the base level build of the database
is deliberately flawed in that a number of indexes are missing.
This is working as designed so that the tools can be used to find the
issues.

You can choose to define the missing indexes by using the PERFLEV
input parameter.  Independently you can also choose whether to start
with the operational tables empty or preloaded with some data by
using the PRELOAD input parameter.  These options are fully described
in the JCL sample and also in the rexx program GLWRUN.

Installation Instructions
=========================

New Installation
================

For Windows:
 1. Install Object Rexx for Windows from ISSI or from the open source
    community at http://www.rexxla.org/
 2. Download the rex file from <glwdsp>.SGLWEXEC(GLWRUN) in text mode
    and rename glwrun.exe and place in a directory in your
    PATH statement

For TSO:
 1. Choose a data set prefix (<glwdsp>) for the code and data.
    This can have multiple qualifiers if you need to have multiple
    concurrent releases of GLW such as DB2CFG.GLW21 and DB2CFG.GLW22
 2. Define a dataset on the host to receive the SGLWSAMP.XMIT file
    with JCL like this:

//DELETE   EXEC PGM=IEFBR14
//GLWSAMP  DD DSN=<glwdsp>.SGLWSAMP.XMIT,
//            UNIT=SYSDA,SPACE=(TRK,0),DISP=(MOD,DELETE,DELETE)
//DEFINE   EXEC PGM=IEFBR14
//GLWSAMP  DD DSN=<glwdsp>.SGLWSAMP.XMIT,
//            DCB=(DSORG=PS,LRECL=80,BLKSIZE=3120,RECFM=FB),
//            UNIT=SYSDA,SPACE=(TRK,(11,1)),DISP=(NEW,CATLG,DELETE)

 3. FTP the file SGLWSAMP.XMIT to the host using the binary option.
    Once sent use:

    RECEIVE INDS('<glwdsp>.SGLWSAMP.XMIT')
    Optional input parm:
    DSNAME('<glwdsp>.SGLWSAMP')

    in option 6 to create the PDS dataset <glwdsp>.SGLWSAMP.
    Copy this PDS to <glwdsp>.SGLWCFG which will be customised
    for all local settings. You should not overwrite this file
    when a new release is installed.
    Customise the member GLWDEFDS in SGLWCFG as indicated.
    Execute this member to define the remaining datasets for ftp.
 4. FTP the these files to the previously created target datasets
    SGLWDBRM.XMIT
    SGLWEXEC.XMIT
    SGLWLOAD.XMIT
    SGLWMLIB.XMIT
    SGLWPLIB.XMIT
    SGLWSLIB.XMIT
    SGLWSRCC.XMIT
    SGLWSRCN.XMIT
    SGLWTABD.XMIT
    SGLWDATA.GLWLD5K.GLWTDPT.TRS
    SGLWDATA.GLWLD5K.GLWTEMP.TRS
    SGLWDATA.GLWLD5K.GLWTPRJ.TRS
    SGLWDATA.GLWLD5K.GLWTPJA.TRS
    SGLWDATA.GLWLD5K.GLWTEPA.TRS
    and rename to <glwdsp>.SGLWDBRM.XMIT etc if required.
 5. Edit this member of the pds:
    <glwdsp>.SGLWCFG(GLWINST)
    and customise as indicated.
    Execute this to receive the remaining XMIT files and to unpack the
    .TRS files.
    If you do not have TRSMAIN installed then you can download
    it from:
    http://techsupport.services.ibm.com/390/trsmain.html
 6. Edit this member of SGLWCFG:
    <glwdsp>.SGLWCFG(GLWRUN)
    Amend the STEPLIB DD statement
    to refer to your DB2 SDSNEXIT and SDSNLOAD library.
    Amend the SYSEXEC DD statement to refer to
    <glwdsp>.SGLWEXEC
    Amend the GLWPARM DD statement to refer to
    <glwdsp>.SGLWCFG(GLWPARM0)
    It is recommended thta each user of GLW copies this JCL member
    to their own library before customising for their usage.
 7. Customise the parm file
    <glwdsp>.SGLWCFG(GLWPARM0)
    by adding or amending the appropriate lines for
    each setting. This file is read and executed by the rexx GLWRUN
    and so each line must be a valid line of rexx code.
    If you choose not to use GLWPARM0 as the active member then remember
    to update the JCL in GLWRUN and GLWSTART.
    Some settings can be set for all subsystems or specified for each
    subsystem individually. These are environmental settings which
    once set should not need ot be changed. The other settings are the
    run time defaults which can all be overridden on each invocation.
 8. If compiled C stored procedures are to be used then define or reuse
    a WLM environment for them. This is option STORPROC(COMPC) in the
    JCL job GLWRUN. This is not required if only native stored
    procedures will be used as defined by STORPROC(NATIVE) in the JCL.
    Set NUMTCB to 15 or thereabouts.
    Ensure that the procedure refered to in the WLM environment
    definition includes the SGLWLOAD library in the STEPLIB
    concatenation as well as SDSNLOAD and SDSNEXIT.
    The WLM environment name chosen should be specified in the
    wlmenva. variable in <glwdsp>.SGLWCFG(GLWPARM0).
    If the run profiles TIMEOUT or DEADLOCK are to be used then define
    or reuse a WLM environment for the rexx stored procedure SLEEP.
    Set NUMTCB to 1.
    Ensure that the procedure refered to in the WLM environment
    definition includes the SGLWEXEC library in the SYSEXEC DD
    card concatenation.
    The WLM environment name chosen should be specified in the
    wlmenvrexxa. variable in <glwdsp>.SGLWCFG(GLWPARM0).
 9. Set up the DB2 bufferpools by doing one of:
    a) defining the bufferpools BP15 and BP16 and granting use to the
       appropriate group or public.
    b) use the input parameters TSBP and IXBP to override the defaults.
    c) amend the default values for tsbp and ixbp in the parm file
       <glwdsp>.SGLWCFG(GLWPARM0)
       to use your own bufferpools and ensure that they are defined.
10. Set up the DB2 storage group by doing one of:
    a) defining the group GLWG01 and granting use to the appropriate
       group or public.
    b) use the input parameter STGP to override the default.
    c) amend the default value for stgp in the parm file member
       <glwdsp>.SGLWCFG(GLWPARM0)
       to use your own storage group and ensure it is defined.
11. Ensure that the TEMP database and tablespaces are defined
    for 4K and 8K page sizes.  The PRJADD stored procedure uses
    a declared temporary table and so needs the TEMP database.
    This is merged with the work database in V9 onwards.
12. Check that the DB2 supplied stored procedure SYSPROC.DSNUTILS
    is installed and available for use. The instructions for this
    can be found in the DB2 installation guide.
    This stored procedure runs utilities as required such as
    LOAD, COPY and RUNSTATS.
    You may need to bind the DSNUTILS package into the DSNREXX
    plan.
13. Optionally define the usage tracking table GLWTLOG.
    Sample DDL for a database, tablespace and table can be found
    in the member <glwdsp>.SGLWCFG(GLWD001). Amend this as
    required. You can define the table owner in GLWPARM0.
    The purpose of this table is gather usage
    information on systems such as Montpellier or Dallas
    where there are many users. The code checks whether the table
    is defined before trying to use it and so this step is optional.

For the ISPF user interface:

 1. Edit the member GLWSTART in dataset <glwdsp>.SGLWCFG by
    changing the <GLWDSP> to the data set prefix for GLW as the
    LOAD and EXIT Libraries are now defined in SGLWCFG(GLWPARM0).
 2. Copy this member to a local CLIST or REXX library
    used for invocation of other DB2 Tools.
    This member is the start up code for the ISPF interface.
 3. To invoke the ISPF interface you need to call the GLWSTART exec.
    This can be done from TSO option 6 or from the DB2 Admin Main Panel
    (ADB2).
    The GLWSTART exec has one parameter; the DB2 SSID.
    Examples:

    TSO option 6
    EX 'DB2CFG.DB2TOOLS.CLIST(GLWSTART)' 'SSID(DB21)'

    DB2 Admin Main Panel (ADB2) -- customised using ADB2CUST exec
    :cdescr.Workload Generator
    :cispf.SELECT CMD(%GLWSTART  SSID(&DB2SYS)) NEWAPPL(GLW) PASSLIB
 4. The ISPF panels for creating JCL for GLW have been modified to use
    the new parameters of GLW V2.2. These new parameters appear on the
    Advanced Options  pop-up screen. There are validation checks to
    make sure, for example,  that you don't create JCL that will request
    SQL Tracing for a DB2 level lower that DB2 V9.
 5. The Stored Procedure option that was on the main GLW panel has
    been moved to the  Advanced Options  pop-up screen. This is because
    GLW will now automatically determine whether to use native or
    compiled C stored procedures based on the version of DB2 that is
    running.
    GLW will use native stored procedures for DB2 V9 and above, and
    compiled C stored procedures for version lower than DB2 V9.
    If you wish to use compiled C stored procedures on DB2 V9 (and
    above) you would need to make that choice on the Advanced Options
    pop-up screen by specifying COMPC for Stored Procedure and 81
    for DB2 Level.

 6. The JCL created from the ISPF panels is dependent on the  Action
    requested, so that only the parameters relevant to the  Action  will
    be produced. This has simplified the parameters list passed to GLW.

 7. The JOBCARD information is now customisable by the user on a new
    pop-up screen. It is selectable from the main GLW panel and allows
    you 4 lines for customisation. Generally you only need to customise
    this option once.

Existing Installation
=====================

Over the top of 2.1
 1. ftp these xmit files over top of existing xmit files or recreate
    if necessary. The trs files have not changed.
    SGLWEXEC.XMIT
    SGLWMLIB.XMIT
    SGLWPLIB.XMIT
    SGLWSAMP.XMIT
    SGLWSLIB.XMIT
    SGLWSRCN.XMIT
 2. Use RECEIVE to recreate the PDS datasets.
 3. Copy GLWSTART from SGLWSAMP into your EXEC or CLIST library and
    customise as required.
 4. Update GLWPARM0 with the defaults for SQLID and UTILHLQ as per
    GLWPARM0 in SGLWSAMP.
 5. Copy GLWSAMP from SGLWSAMP to GLWCFG.

Over the top of any release before 2.1
 1. Treat as a new installation

Usage Advice
============
Refer to your_dataset_prefix.SGLWCFG(GLWRUN)
for documentation on the parameters.

Object Change Management
------------------------
The tool creates a database with many of the features that you could
expect to find at a customer site. Two of the tables are partitioned.
Referential integrity is defined between the five operational
tables - GLWTDPT, GLWTEMP, GLWTPRJ, GLWTPJA, GLWTEPA.  You can
choose to use application RI by setting the BUILDRI parameter to GLW.
The reference tables and the number generator tables all use
identity columns.  The number generator tables are always empty as
they are used like sequences in V8, purely to generate the next number.
The employee table (GLWTEMP) has three triggers defined on it which
update the department table (GLWTDPT).  You can use the TRIGGER
parameter to disable the triggers.  All tables have views defined
on them but note that the views can not be used unless the user has
access which is either through having SYSADM or a secondary authid
which corresponds to the owner of the view or has been granted access
by one of the above.


Performance Management
----------------------
I tend to use Omegamon Performance Expert for DB2, IBM Query Monitor QM,
Visual Explain, Administration Tool Explain, or Optimization Expert
when exploring performance features.  The workload can be driven from
a workstation through DB2 Connect and so can also be used for
examining the data captured by DB2 Connect Monitoring.
The workload is deliberately not tuned.  By running multiple threads,
either from the workstation or batch or both, you can generate
timeouts and deadlocks to illustrate the ability of PE to capture
and show these events. There is a stored procedure (DPTLCK) which takes
a lock for 65 seconds on the GLWTDPT table and hence causes timeouts
on any other thread running at the time.  This program can be run
by using the run profile of TIMEOUT (RUNPROF(TIMEOUT)).
When using this profile it is also advisable to use the WAITTIME
parameter, say 10 seconds, to let some transactions run in between
the locks.
The stored procedure DEDLCK can be used the create deadlocks.  This
procedure is invoked with the run profile of DEADLOCK.  To create
a deadlock, at least two threads must run concurrently on the same
schema at the same time.
The stored procedure EMPQRY causes RID pool failures which can be
displayed using Omegamon PE.

You can use QM to find the most expensive SQL - no prizes for finding
the missing indexes when using PERFLEV(BASE).
If you plan to use the link to SQL PA then check that the SQL explains
ok first.

The stored procedure EMPFND generates dynamic SQL which can be
captured and displayed by QM.
SQL/PA can be used to explain the SQL contained in the DBRM library
(SGLWDBRM). The last time I tried it, it successfully handled
all but 2 of the statements.

The run parameters - RUNTIME, RUNMODE and RUNPROF together
with the table GLWTPGW control how any one run behaves.
The RUNMODE parm, either RANDOM or FIXED, determines whether
the sequence of stored procedures called will be repeatable or random.
This parameter also controls the behaviour of the stored procedures
when they need to make a choice such as selecting a location
from the locations table GLWTLOC for the department table GLWTDPT.
The fixed option can be used to execute repeatable runs which have
exactly the same sequence of SQL calls.  This can be used for
comparative measurements when changing external factors such as
bufferpool sizes or object placement.  Note that for this sort of
trial do not run more than one thread concurrently as the predictable
nature of a fixed run will be lost.
The interpretation of the run time parameter depends on the run mode.
For run mode random, the run time is the duration of the run in
minutes.  The number of stored procedures called will depend on
how fast they execute.  For run mode fixed it is a little more complex
as it now determines the exact number of stored procedure calls.
This is done by summing the program weights from the GLWTPGW table
for the chosen run profile, such as STANDARD, and multiplying by the
run time.  For example if the program weights sum to 100, and they
all do except for TIMEOUT and DEADLOCK, and the run time is set to 50,
then 50*100=5000 stored procedures will be executed.  A number of run
profiles have been defined but you can edit the USER profile to be
whatever weight combination you like.

Replication
===========
The Generator can be used to create both the source and the
target environments. For the source use DATACAP(CHANGES) and for the
target use TRIGGER(NO).

I have left the generation of image copies as a user exercise.
There is no usage yet of UDFs, UDTs, alias's, synonyms.
This version still runs on V7 and so no features of V8 or V9 have
yet been exploited apart from native stored procedures in V9 and
use of NOT ENFORCED for triggers for V8 NFM and above.
If you want to alter the balance of which stored procedures are
called, then edit the RUN_WEIGHT column of the WLDTPGW table for the
run profile of USER.  The default is the STANDARD profile.

The workload will run on either DB2 V7, V8, V9  and V10 as the DDL and
stored procedures are release controlled.

Known Issues
============
There can be a problem with workload manager if a thread is cancelled
during execution. The symptom is that a subsequent batch submission
fails.  If this occurs then the solution is to quiesce and
resume the workload manager environment where the stored procedures
were executing.

Application Change History
21/01/2011 V2R2
  Reordered the DROP PROCEDURE statements in GLWSAMP to comply with
  APAR PM39466
  Amended comments ref WLMENV; changed code to tolerate no default
  Added ability to define a separate WLM environment for the rexx stored
  procedure SLEEP
  Added code to give better message if DB2 subsystem not active
  Added ability to run the REPAIR utility
  Changed GLWBUILD to always run GRANTs
  Added SQLID parm to allow a user to specify a different
  id to their current id to gain the appropriate authorisation.
  Added a high level qualifier option (UTILHLQ)
  for the work datasets of utilities
  Changed the version detection code to use the session variable
  SYSIBM.VERSION as well as using DISPLAY GROUP and scraping the result.
  if a data sharing group is in coexistence mode.
  Added ability to run UPDATE statements
  Fixed problem with explain of native stored procedures
  Amended the README file in SGLWSAMP.
  Added DATABASE option to override the schema name
  Added DDL to create the full set of EXPLAIN tables
  (DDLEXP09 and DDLEXP10)
  Added ability to override the buffer pools for 8K, 16K and 32K page
  size in GLWPARM0
  Added new JCL parameter BPOOL to control whether overrides are used
  Added new JCL parameter STOGP to control whether overrides are used
19/10/2010 V2R1
  Added ability to INCLUDE members from a pds.
  Used INCLUDE for all native stored procedures
  Removed use of the system node to determine the defaults
  Amended method to set the customisable defaults by adding a parm file
  Added 'SET CURRENT SCHEMA' for java JDBC
  Added ability to run Java stored procedures
  Corrected EMPQRY to fetch all rows from the second table
  Changed to table controlled partitioning as not supported in V10
  except when using DB2 V8 CM.
  Added DB2 release control. Allows users to specify levels of
  functionality lower than that available.
  Added the ability to trace every single SQL statement
  Added control of the dataset prefix to allow for multiple concurrent
  versions of GLW such as DB2CFG.GLW21
  Removed DBRMLIB input parm as no longer required.
  Change default of STORPROC to NATIVE
  Added street names and towns look up tables
  Added XML column to GLWTEMP
  Removed overrides for wlmenv, stgp, tsbp, ixbp as can now be set
  in the GLWPARM file.
  Added stored proc EMPADX to add employee contact info as XML data
  Prevent COMPC running after 8.1 as will fail.
17/09/2009 V1R7
  Changed default for FLUSHBP to NONE
  Added new query function EMPQR2
  Added new employee update function EMPUPR
08/06/2009 V1R6
  Corrected error on call of a BIND
  Dropped TABLE(ALL) INDEX(ALL) from RSTATLEV(BASE)
  Added KEYCARD to RSTATLEV(TUNED)
  Fixed problem with determining the DB2 code level which prevented
  the use of NATIVE for stored procedures in Version 9
  Added DB5n to MOPZT01
18/08/2008 V1R5
  Corrected error which used RI NOT ENFORCED in V8 CM; possible in NFM
  Added utility message to output for LOG(SUMMARY)
  Corrected error in BUILD which did not correctly determine the DB2
  level and mode.
  Added option to control AUDIT parameter
  Added Comments and Labels to the Department and Employee tables
  Added option to choose native or compiled C code for the Stored Procs
  Added option to control level of RUNSTATS
  Added new procedure to create deadlocks along with new RUNPROF
  Changed all default WLM environments for MOP to WLMGLW.
  Changed GLWXEMP4 and GLWXEMP5 from performance level BASE to TUNED
  Amended the native version of EMPFND to run correctly in V9 by
  adding a SET SCHEMA statement.  This is because the default for
  an unqualified table name in dynamic SQL has changed from the
  BIND qualifier for compiled C stored procedures to the
  current sqlid for native stored procedures.
  Added option to control index copy.
12/02/2006 V1R4
  GLWPRIM panel made scrollable;
  GLW exec modified to detect if advanced options variables are set;
  Info message added to GLWPRIM panel indicating advanced options set;
  Minor changes to help panels;
  Option to STOP/START all objects to flush pages from the bufferpools
  before and/or after each run by using the FLUSHBP parameter;
  Ability to process STOP/START commands in the build stream;
  Added definition of data sharing group on DEMOMVS DB1S and DB2S;
  Amended GLWBUILD to use NOT ENFORCED for RI definitions when
  BUILDRI(GLW) chosen and provided DB2 version 8 or above;
  Added the SDSNEXIT library to the sample JCL;
  Added PRELOAD and PERFLEV parameters;
  Consolidated GLWSPEC, GLWSPECT and GLWLD5K into a single
  build dataset GLWSAMP with optional statements controlled
  by use of the PRELOAD and PERFLEV parameters;
  This makes maintenance simpler and also allows for more options
  in that you can now have a preloaded database which is either
  untuned or tuned depending on what you want.
  Added optional workload usage tracking table GLWTLOG;
  Added a DEBUG parameter to control rexx tracing;
  Increased maximum waittime to 60 to allow for more interesting
  use of the DPTLCK stored procedure;
  Substitute BUILD for RUN if the database does not exist and GLWRUN
  is executed via TSO batch;
  Added the COMPRESS parameter to control compression;
  Added the TRIGGER parameter to control creation of the triggers; This
  should be used when defining the target for replication;
  Added the DATACAP parameter to set DATA CAPTURE on the tables;  This
  should be used when defining the source for replication;
15/03/2006 V1R3
  Suppressed the error message for failed DELETEs
  Updated GLWRUN and GLWBUILD to build GLWDRDA for remote access
  Fixed problem for new code causing a REBIND
31/01/2006 V1R2
  Amend SP DPTMGR to handle unlimited number of departments
  Amend SP DPTSEL to avoid TS scans of GLWTDPT
  A BIND of the packages DPTMGR and DPTSEL is required
  Add GLWSPECT as a tuned version of GLWSPEC
  Fix report formatting for very long running procedures
  Remove output of BIND action when LOG(SUMMARY) chosen
  Add ability for a userid to DROP databases created by others
  see the customise section
  Add the ability to execute utility statements;
  LOAD, CHECK, RUNSTATS and COPY can be executed in the BUILD process
  Add the new build spec GLWLD5K to preload a database environment
  Fixed GLWBUILD to correct error when using TSBP, IXBP or STGP
01/12/2005 V1R1
  Base version

Change History for the original workload generator
==================================================
18/07/2003 V0R1
  Base version
21/08/2003 V1R1
  Fix runtime(0) reporting error;
  Change default runtime to 1 minute;
  Update help text;
  Fix runtime when parm not set;
24/11/2003 V2R1
  Rewrite for version control
25/03/2004 V3R1
  Add the project tables to create more data
25/04/2004 V4R1
  Correct the WLRESET stored procedure
25/05/2004 V5R1
  Added PRJUPD to cause DB2 relocates
23/06/2004 V6R1
  Addition of GDGLIMIT parameter
  Addition of base table views
  Reallocation of all tables to new tablespaces
  Addition of table comments
  Adoption of new object naming convention
  Warning instead of RESET when database backlevel
  Report of before and after row count by table
  Remove default for DB2 location or subsystem
19/08/2004 V7R1
  Fixed authorisation issue using views with RACF secondary groups.
  Resolved by changing all DML and code to use the base tables.
19/08/2004 V8R0
  Addition of index on JOB of EMP - not used
  Addition of index on GLWTEPA - for EMPQRY
  Addition of stored procedure EMPQRY - generates RID list failures
  Addition of BUILDMBR parameter.
28/02/2005 V9R1
  Rewrite of DMSYSWLR and DMSYSWLB
  Addition of LOG, BIND, storage group (STGP) parameters
  Addition of tablespace and indexspace bufferpool parm
15/06/2005 V10R1
  Addition of RUNMODE parm to control sequence of SP calls
  Stored procedures rewritten to use same parm so as to be
  predictable for repeatable runs.
  Addition of BUILDDS, WLMENV and DBRMLIB input parameters
01/07/2005 V11R1
  Addition of CLUSTER INDEXES to all tables which enables use of
  REORG with SORTDATA in V7.
  Addition of default column to GLWTPGW
  Fix to EMPADD to populate the PHONENO column
  Addition of EMPFND stored proc to do dynamic SQL
25/07/2005 V12R1
  Recode the generator to allow single threaded RUNMODE(FIXED)
  executions to do exactly the same work across multiple executions.
  For example a RUNTIME of 10 will produce the same
  result as two consecutive runs of RUNTIME 5.
  Restructure the log file GLWTSPL to add a transaction
  counter as an IDENTITY column.
  Recode DMSYSWLR to avoid use of dynamic INSERT into the log GLWTSPL.
  Recode all stored procs to use the RUNMODE as input and
  to INSERT into the log file GLWTSPL
09/08/2005 V13R1
  Add EXPLAIN parameter;  Optional;
  Default depends on the existence of user_id.PLAN_TABLE
  Fix memory leakage by changing back from SQLDA to HV call.
16/09/2005 V13R2
  Introduce a release level.  A release does not need a change to the
  application code or the database.
  Add ABOUT parameter to return the version and release level
  Fix error when a user attempts a BUILD of existing database
22/09/2005 V13R3
  Fix error when a user attempts a RUN of an existing database
  that they did not create.

13/10/2005 V14R1
  Added copyright notice to all code to allow distribution to customers
  Change the GLWSSPL tablespace to partitioned and removed the identity
  column. Add a SPLANO number generator stored proc for GLWTSPL
  Output result of a failed DELETE or DEFINE of a GDG
  Increase RUNTIME limit from 300 to 500
  Fix incorrect definition of the UPDATE trigger
  Add new parameter GRANT to control execution authority.
  Add new parameter RUNPROF to control the profile of stored procedures
  executed.  This will allow a multithreaded run to more easily
  control the workload.
  Add a new stored proc called DPTLCK to cause a timeout.  It takes an
  exclusive lock on GLWTDPT and then sleeps for 65 seconds.
  Modify DPTSEL and EMPSEL to ensure they find a dpt/emp more often
  Modify PRJADD to use more employees on project activities
  when the RUNMODE is FIXED.
  Add the full set of explain tables to support Visual Explain
  Add a new sp, DPTBAL, to balance employees across departments
  Amended WAITTIME to randomise between 0 and 2*WAITTIME
  for RUNMODE(FIXED)
  Remove the RESET function

Footnote
========
The choice of three letter code for the workload generator was
very limited, most options have been taken.  I chose GLW as the
reverse of WLG, the natural choice.
I think of GLW as a tool to Generate a Load of Work.

