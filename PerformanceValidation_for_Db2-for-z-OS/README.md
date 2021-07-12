# IBM Db2 Performance Validation Workload.

This is the repository for the IBM Db2 Performance Validation Workload.

This workload was first created by Mike Bracey and Steve Speller. It was designed for Db2 v7 and modified to work up to Db2V10 by the original authors.

Since there are customers requested to enhance this workload to work on the latest Db2 version, we removed the code specific for V10 and below, and enhance it to work on Db2 V11 and above. The enhancements include using SYSPROC.DSNUTILU to replace SYSPROC.DSNUTILS, and UTS tablespaces to replace non-UTS tablespaces.

To install this workload onto your LPAR, you need to get the XMIT files and TRS files to your working LPAR, unpack the datasets, and customize the scripts/JCLs for your environment. You can use provided JCLs to define datasets for XMIT/TRS, then FTP files to your zOS LPAR, and use JCL to unpack the XMIT/TRS datasets. You also can customize the provided sample Windows batch scripts to perform those above tasks.

Here are steps to install this workload onto your LPAR:

1. ### Clone or download all files onto your work station.

	It is better to clone this repository using Git Client or commandline because some JCLs might become unusable if you download ZIP file and unzip the files.

1. ### Get files onto your LPAR.

	After cloning this repository or downloading files onto your work station, you need to customize the helper files in folder FILES before executing those helper batch scripts.

The hostname, userID, and password in the sample Windows batch scripts need to be changed for your environment if you plan to use these Windows batch files to automate the process.

You also need to update the  [HLQ] for dataset name in GLWDEFDS.JCL, UPLOAD.TXT, and UNPAK.JCL. These JCLs are for defining datasets for XMIT/TRS files, FTPing files to zOS LPAR, and unpacking the XMIT/TRS datasets into PDS datasets.

To execute the helper batch scripts, you just run the following command at Windows command prompt: ftp -s:windowbatchscript. Here are steps:
	
		* ftp -s:define.txt   : to submit JCL to define datasets for XMIT/TRS files.
		
		* ftp -s:upload.txt   : to ftp downloaded files to the predefined datasets for XMIT/TRS files.
		
		* ftp -s:unpack.txt    : to submit JCL to unpack the XMIT datasets and unterse the TRS datasets. 	
	
1. ### Verifying the installation

	After getting and unpacking files onto your LPAR, besides the XMIT and TRS datasets, you should have the following datasets:

		[HLQ].GLW.SGLWDBRM

		[HLQ].GLW.SGLWEXEC

		[HLQ].GLW.SGLWLOAD

		[HLQ].GLW.SGLWMLIB

		[HLQ].GLW.SGLWSAMP

		[HLQ].GLW.SGLWSLIB

		[HLQ].GLW.SGLWSRCC

		[HLQ].GLW.SGLWSRCN

		[HLQ].GLW.SGLWTABD
		
		[HLQ].GLW.SGLWCFG

		[HLQ].SGLWDATA.GLWLD5K.GLWTDPT

		[HLQ].SGLWDATA.GLWLD5K.GLWTEMP

		[HLQ].SGLWDATA.GLWLD5K.GLWTPRJ

		[HLQ].SGLWDATA.GLWLD5K.GLWTPJA

		[HLQ].SGLWDATA.GLWLD5K.GLWTEPA

	For description of members in those PDS datasets, please see README in folder FILES.

1. 	### Customize the workload for your environment:
	
	1. Since GLW uses a predefined storage group GLWG01, you could use the sample JCL GLW.SGLWCFG(CRTSG) to define one for your environment.
	
	1. The GLW workload uses a WLM for stored procedures and a WLMU for Utility, so you should update the member SGLWCFG(GLWPARM0) to use the correct WLMs.
	
	1. By default, the workload uses schema GLWSAMP, so if you plan to use a different schema then please replace GLWSAMP by your new schema name for:

		* SGLWCFG/SGLWSAMP: members GLWBUILD, GLWDDL, GLWDROP, GLWLDALL, GLWPARM0, and GLWRUN.
	
		* All members in GLW.SGLWSRCC, GLW.SGLWSRCN, and GLW.SGLWTABD.	 
	
		* We provide a REXX script SGLWEXEC(STRREPL) to replace a string by another string for all members in a PDS dataset. The JCL SGLWCFG(STREPLAC) is an example to replace old string by new string in all members in dataset DB2DRDA.GLW.SGLWWSRCN
	
1. ### Steps to build and run the GLW workload

	1. #### Build database
	
		After customizing the common parameters in SGLWCFG(GLWPARM0), you can customize SGLWCFG(GLWSETUP) to build database for the GLW workload on your environment. The original authors documented the parameters very well in the SGLWSAMP/SGLWCFG(README).

The original README file and SGLWEXEC(GLWRUN) are good source to understand all parameters and variables to customize the GLW workload. You need to correct STEPLIB, SYSEXEC, GLWPARM, DB2SSID, SCHEMA, DBASENME , and SQLID before submitting the GLWSETUP job to execute GLWRUN with ACTION(BUILD). By default, the job is using member GLWSAMP in [HLQ].SGLWCFG as the DDL to define all the objects. There is also a sample member GLWSAMP in [HLQ].SGLWSAMP you can reference. Please do check the joblog after the BUILD option to make sure everything is OK.

You could modify and use sample JCL SGLWCFG(GLWTBSEL) to get the row count of major tables of the GLW to validate the BUILD procedures. If row count is 0, then tables have not been loaded. This could be the SYSPROC.DSNUTILU has not been set up correctly or your environment does not have SYSPROC.DSNUTILU set up. In this case, you could customize JCL SGLWCFG(GLWLDALL) and SGLWCFG(GLWRSTAT) to load data then perform RUNSTATS.
		
If you need to remove all objects created for the GLW workload, please customize and use SGLWCFG(GLWDROP) to drop all database objects and use SGLWCFG(CRTSG) to drop the created storage group.
		
	1. #### Run the workload
		
		You need to customize SGLWCFG(GLWRUN) before trying to run the GLW workload. The correct STEPLIB, SYSEXEC, GLWPARM, DB2SSID, SCHEMA, SQLID, STORPROC, and DBASENME are needed before running the workload.
		
		This workload supports Native stored procedures, C stored procedures, and Java stored procedures. This workload uses Native stored procedures by default, and if
		you want to use C stored procedure instead of Native stored procedure, you need to recompile all stored procedures in GLW.SGLWSRCC for your environment. 
		
		RUNTIME parameter specifies the duration of the run in minutes. After the workload finishes running, you should see the summary in the job log as sample below
		
						Table row count before and after report                      
				Table name                      Before      After Difference 
				GLWSAMP.GLWTACT                     18         18          0 
				GLWSAMP.GLWTDNG                      0          0          0 
				GLWSAMP.GLWTDPT                   5000      15616      10616 
				GLWSAMP.GLWTEMP                  30000     158793     128793 
				GLWSAMP.GLWTENG                      0          0          0 
				GLWSAMP.GLWTEPA                 405000    1618030    1213030 
				GLWSAMP.GLWTJBS                     15         15          0 
				GLWSAMP.GLWTLCN                     31         31          0 
				GLWSAMP.GLWTLNG                      0          0          0 
				GLWSAMP.GLWTLNM                    351        351          0 
				GLWSAMP.GLWTPGW                     57         57          0 
				GLWSAMP.GLWTPJA                 135000     602946     467946 
				GLWSAMP.GLWTPNG                      0          0          0 
				GLWSAMP.GLWTPRJ                   7500      33497      25997 
				GLWSAMP.GLWTSFN                     84         84          0 
				GLWSAMP.GLWTSPL                      0     318965     318965
				GLWSAMP.GLWTSQL                      0          0          0
				GLWSAMP.GLWTSTR                   1911       1911          0
				GLWSAMP.GLWTTWN                     81         81          0
				GLWSAMP.GLWTVRN                      1          1          0
				
			
						GLWR270I: Stored Procedure call summary                            
				DPTADD   ran  13297 times at an average elapsed of    0.001 seconds
				DPTBAL   ran  13378 times at an average elapsed of    0.001 seconds
				DPTDEL   ran   2678 times at an average elapsed of    0.001 seconds
				DPTMGR   ran  13260 times at an average elapsed of    0.001 seconds
				DPTUPD   ran  10415 times at an average elapsed of    0.001 seconds
				DPTUPR   ran  13086 times at an average elapsed of    0.001 seconds
				EMPADD   ran 133076 times at an average elapsed of    0.001 seconds
				EMPADX   ran  53150 times at an average elapsed of    0.001 seconds
				EMPDEL   ran   2707 times at an average elapsed of    0.001 seconds
				EMPFND   ran   5269 times at an average elapsed of    0.009 seconds
				EMPQR2   ran   8082 times at an average elapsed of    0.009 seconds
				EMPUPD   ran   5187 times at an average elapsed of    0.001 seconds
				EMPUPR   ran   5347 times at an average elapsed of    0.001 seconds
				PRJADD   ran  26632 times at an average elapsed of    0.006 seconds
				PRJUPD   ran  13400 times at an average elapsed of    0.001 seconds	

					Total calls= 318964 ; Runtime=    10.0 Minutes        
					Transaction rate=   531.6 trans/sec                   		
		
	
	
	
