# File descriptions.

This folder contains datasets for the GLW workload. You need to define PDS datasets to receive XMIT and TRS datasets, ftp files to zOS LPAR, then unpack the XMIT/TRS datasets. We provide several helper files to assist you to perform those tasks.

### 1. Helper files 
	To define datasets, upload files to zOS LPAR, and unpack XMIT/TRS datasets, you can use sample helper files below.
	
	* GLWDEFDS.JCL: JCL to define datasets for XMIT and TRS datasets.
	
	* DEFINE.TXT : Sample Windows batch script to submit GLWDEFDS.JCL to zOS LPAR.
	
	* UPLOAD.TXT: Sample Windows batch file to ftp XMIT and TRS files to predefined datasets in zOS LPAR.
	
	* UNPAK.JCL : JCL to unpack XMIT datasets and terse TRS datasets.
	
	* UNPAK.TXT : Sample Windows batch script to submit UNPAK.JCL to zOS LPAR.
	
	Notes:
	
	* You need to customize those above JCLs and batch scripts for your environment( Hostname, userID, password for FTP, [HLQ] for MVS datasets ).
	
	* ftp -s:windowsbatchfile is the command on Windows command prompt to run a Windows batch script.
	
### 2. SGLWCFG  
    Sample JCLs to set up and run the workload. These JCLs are from the SGLWSAMP and customized for the test environment. This set up uses the default values

	* CRTSG : sample JCL to create store group GLW01.   
	
	* GLWSETUP  : sample JCL to create database and objects for the GLW workload.   
	
	* GLWDEFDS  : sample JCL to define all xmit and trs dataset to prepare to receive files from FTP process.
	
	* GLWDROP   : sample JCL to drop objects created for the GLW workload.
	
	* GLWD001   : DDL to create optional usage tracking tables.
	
	* GLWINST   : sample JCL to receive data from xmit datasets and unterse the TRS datasets.   
	
	* GLWLDALL  : sample JCL to load data into GLW tables, in case of a WLMU has not been set up.
	
	* GLWPARM0  : sample parameter files. Needs to be customized for your environment.
	
	* GLWRSTAT  : sample JCL to perform runstats on GLW tablespaces.
	
	* GLWRUN    : sample JCL to run the workload.
	
	* GLWDDL    : DDL for the GLW workload.   
	
	* GLWSTART  : this EXEC needs to be customized for your environment if you plan to add GLW to ISPF.
	
	* GLWTBSEL  : sample JCL to select row count from GLW table to verify the load process.
	
	* README    : original readme file from the original authors.
	
	* WLMSAMP   : sample WLM environment definition.
	
	* STREPLAC  : sample JCL to replace a string by another string for all members in a PDS dataset. 
	
### 3. SGLWDBRM  
     DBRM  for the GLW workload.

### 4. SGLWEXEC  
     REXX modules for the GLW workload. 

	* CRTSPCAT  : REXX module to creare stored procedure code.
	
	* DB2LOCK1  : REXX module to lock a Db2 table for a time interval.
	
	* GLW       : REXX EXEC to act as an interface to the GLW.
	
	* GLWBUILD  : REXX module to build environment for the GLW, called by GLWRUN.
	
	* GLWDB2LV  : REXX function to find the current level of Db2 subsystem.
	
	* GLWRUN    : REXX module to drive the workload.
	
	* SLEEP     : REXX function to wait x seconds.
	
	* STRREPL   : REXX module to replace a string by another string for all members in a PDS dataset. 
	
### 5. SGLWLOAD  
	LOAD modules for the GLW workload.

### 6. SGLWMLIB  
	Library for the GLW workload to display error messages.

### 7. SGLWSAMP  
	Sample JCL to run the workload. These JCLs are copied to SGLWCFG  then customized for the running environment. For description, please see SGLWCFG section above.

### 8. SGLWSLIB 
	Library for the GLW workload.

### 9. SGLWSRCC
	DDL to create stored procedures ( in C language )used by the GLW workload, and the source code of stored procedures in C language.

### 10. SGLWSRCN 
	DDL to create stored procedures ( Native stored procedure ) used by the GLW workload, and ther source code od stored procedures in Native SQLPL.

### 11. SGLWTABD
	DDL to load data into tables of the GLW workload.

### 12. .TRS files	
	The .TRS files are data files  to be used to load into GLW tables.
