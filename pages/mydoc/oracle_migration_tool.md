---
title: Oracle Migration Tool V1.0
tags: 
keywords: 
last_updated: July 6, 2019
summary: "Migrating Oracle database from on premise to the cloud or new server on premise."
sidebar: mydoc_sidebar
permalink: oracle_migration_tool.html
folder: mydoc
---

## License
<a href="https://github.com/tcusano/tcomt/blob/master/LICENSE">GNU GENERAL PUBLIC LICENSE</a>

## About the author

I keep this brief as you are probably more interested in the tool. I started working in IT in 1988 and since then I have been working on many different systems. I have been working with Oracle databases for over 20 years and I recently moved into DEVOPS.   

## Introduction

The reason I started to write this migration tool was really to achive two things. The first was to learn Python and the second to have a tool to migrate Oracle database to the Cloud on premise. What triggered in me to writing this tool was when I carried out a proof of concept migrating an Oracle database from Oracle Solaris (Big Endian) to Oracle Linux (Little Endian) using xttdriver.pl (which is script provided by Oracle). I found that there was too much manual work involved in using this tool and is prone to human error as I have found out. So hopefully with this new tool I have made it easier for the DBA to migrate an Oracle database without the worry of having to remember all the manual checks you have to do before you start a migration.

## Scope

Currently this tool only uses Transportable Tablespaces meaning that it currently does not move the system, sysaux, undo or temp tablespaces. 

In the next release I plan to include the migration of the whole database. 

## About the tool

The tool uses entirely Oracle commands and packages.

The tool can be run any time meaning it can run along side an online database being used by the business as it does not require any database downtime until the final switch over to the newly migrated database. 

It takes a copy of the database files to the new site and converts the files to the appropriate endian if it needs to. Then a daily backup is run and applied to the datafiles on the target to keep then up to date. No different to xttdriver except that the process is automated with this tool.

## Test Matrix

<table id="matrix" class="display">
   <thead>
      <tr>
         <th>Source DB</th>
         <th colspan="4">Target DB</th>
      </tr>
   </thead>
   <tbody>
      <tr>
         <td></td>
         <td>12c (Little Endian)</td>
         <td>12c (Big Endian)</td>
         <td>18c (Little Endian)</td>
         <td>18c (Big Endian)</td>
      </tr>
      <tr>
         <td>10g (Little Endian)</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
      </tr>
    <tr>
         <td>10g (Big Endian)</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
    </tr>
      <tr>
         <td>11g (Little Endian)</td>
         <td>OK</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
      </tr>
      <tr>
         <td>11g (Big Endian)</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
      </tr>
      <tr>
         <td>12c (Little Endian)</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
      </tr>
      <tr>
         <td>12c (Big Endian)</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
      </tr>
      <tr>
         <td>18c (Little Endian)</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
      </tr>
      <tr>
         <td>18c (Big Endian)</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
         <td>TBC</td>
      </tr>
   </tbody>
</table>

{{site.data.alerts.note}} Currently I do not have a Big Endian machine to test with. This is the only step of the tool which I have not been able to fully test. {{site.data.alerts.end}}

## Requirements

Python 3.6.3 and above

Python Modules:  
* paramiko 2.4.0  
* cx_Oracle 7.0.0  
* pycrypto 2.6.1  

### Prerequisites

{{site.data.alerts.important}}The target database must be 12c or higher. Source database must be 10g or upto the same as target database. {{site.data.alerts.end}}

Upgrading/migrate same version of the database at the same time as migrating to the cloud or new server on premise.

### Target Database

12c or higher database requires that only have the following tablespaces:
* system
* sysaux
* undo
* temp
* users  - if you do not plan to migrate the users tablespace from source you can leave this tablespace otherwise drop it. You will need to change the default database tablespace with sql command “ alter database default tablespace sysaux”. After migration change this back to users or tablespace of your chioce as long it is not any of the other four listed above. If you plan to do a full database migration then you will need to drop the Users tablespace on the target.

{{site.data.alerts.note}} If migrating to same version of Oracle please ensure the binaries are on the same patch level. {{site.data.alerts.end}}

### Installation
#### Linux

Create directory structure

<table id="table1" class="display" width="100%">
      <tr>
         <td>mkdyir -p &lt;pathpycryptoof your choice&gt;/migration/pythondir &lt;path of your choice&gt;/migration/log</td>
      </tr>
</table>

{{site.data.alerts.note}} Replace &lt;path of your choice&gt; with root path e.g. /home/oracle {{site.data.alerts.end}}

Create a virtual environment

<table id="table2" class="display" width="100%">
      <tr>
         <td>/usr/bin/virtualenv -p python3.6 &lt;path of your choice&gt;/migration/pythondir/venv36</td>
      </tr>
</table>

{{site.data.alerts.note}} Replace &lt;path of your choice&gt; with root path e.g. /home/oracle {{site.data.alerts.end}}

Activate virtual environment

<table id="table3" class="display" width="100%">
      <tr>
         <td>source &lt;path of your choice&gt;/migration/pythondir/venv36/bin/activate</td>
      </tr>
</table>

Install paramiko module

<table id="table4" class="display" width="100%">
      <tr>
         <td>pip install paramiko</td>
      </tr>
</table>

Install oracle module

<table id="table5" class="display" width="100%">
      <tr>
         <td>pip install cx_Oracle</td>
      </tr>
</table>

Install pycrypto module

<table id="table6" class="display" width="100%">
      <tr>
         <td>pip install pycrypto</td>
      </tr>
</table>

Install tool modules

Download latest code from GitHub

<table id="table7" class="display" width="100%">
      <tr>
 	<td>cd &lt;path of your choice&gt;/migration/pythondir/ <br>
	     git clone <a href="https://github.com/tcusano/tcomt.git">https://github.com/tcusano/tcomt.git</a></td>
      </tr>
</table>

Install code and verify Python Modules

<table id="table8" class="display" width="100%">
      <tr>
 	<td> cd tcomt<br>
             bash install.bash</td>
      </tr>
</table>

Expected Output
<table id="table9" class="display" width="100%">
      <tr>
        <td> Installing scripts ............<br>
         Install completed successfully.<br>
         Checking Python module pycrypto version ....<br>
         pycrypto version ok.<br>
         Checking Python module paramiko version ....<br>
         paramiko version ok.<br>
         Checking Python module cx-Oracle version ....<br>
         cx-Oracle version ok.
        </td>
      </tr>
</table>

## Getting Started With The Migration

Now we ready to start the tool:

<table id="table9" class="display" width="100%">
      <tr>
	<td> cd ${VIRTUAL_ENV} <br>
             python maintc.py &amp 
        </td>
      </tr>
</table>

{{site.data.alerts.note}} “&amp;” means run the process in the background {{site.data.alerts.end}}

The following screens should appear:

<img title="" src="images/Selection_029.png" />

on the left hand side you have the tool and on the right side is the log console which gives information on what is occurings.

As this is the first time we running the tool, we will need to enter a process name for the migration and for this I enter “ale1” and then a new pass key which can be max of 18 characters. You will need to keep this pass key in a safe place as from here onwards you will need it to connect to the tool.

<img title="" src="images/Selection_030.png" />

Once you enter the details, click on “OK” you will notice information appearing in the log console.
Next we click on the “DB Details” tab at the top of the screen 

<img title="" src="images/Selection_031.png" />

Now we enter the details of the source and target databases, once the details are entered click on “SAVE” button,  this will automatically invoke the “Test Source” and “Test Target” buttons.

<img title="" src="images/Selection_032.png" />

In the log console you can see that the connection to the databases where successful and the database versions also are satisfactory. So now we click on tab “OS Details”

<img title="" src="images/Selection_033.png" />

Same again we enter the details and click save.

{{site.data.alerts.note}} Oracle software owner is required. {{site.data.alerts.end}}

<img title="" src="images/Selection_034.png" />

Again we have successful connection as shown in the log console. Should there be a failure the buttons on the tool will also turn. Now we click on the “Tablespaces” tab.

<img title="" src="images/Selection_035.png" />

Even thou we successfully connected to the databases the tool appears to be empty, so we need to refresh the screen by clicking on the “Refresh” button.

<img title="" src="images/Selection_036.png" />

When the refresh happens the tool checks the endians of both databases. Now here we select the tablespace we like to transfer to the target database. Currently the following tablespaces can not be migrated using the tablespace method which are system, sysaux, undo and temp. Normally when you create a new target database the users tablespace is normally empty so if you wish you transfer the one from the source you can either rename the one on the target of drop it (You may need to change the database default tablespace to sysaux for the duration of the migration as Oracle default is the Users tablespace  with the command “ alter database default tablespace sysaux”. After successful migration you should revert this back to USERS tablespace or tablespace you have created. 

<img title="" src="images/Selection_066.png" />

Once you have selected the tablespaces you wish to transfer you will need to click on the button “Verify &amp;Save”. This will carry out a number of checks which will be seen in the log console.

<img title="" src="images/Selection_037.png" />

The very first check is the time zone file, as you can see the database have different time zones. My recommendation is for you to refer to Oracle Support (Metalink) on this for further details. For now I going to click “Yes” to ignore this as these are just proof of concept databases.

<img title="" src="images/Selection_038.png" />

If you want to migrate all the tablespaces then ensure you select then all leaving the “Source” pane empty.

In the log console you can see other checks that were carried out:

a) Character sets must be same on both databases  
b) Checking the database log mode must be in archivelog mode  
c) Checking database role (currently this tool does not support standby databases)  
d) Checking Time Zone File Versions  
e) Checking if tablespaces are Self-Contained  
f) Checking block sizes (this is not only the DB block size but also the tablespace block size)  

If everything is good then we click on the next tab “Options”

<img title="" src="images/Selection_039.png" />

Again we select our choices currently this tool does not support multiple target file destinations. By clicking on the check box for “Full schema import” this will import all the objects that are not done by the transportable tablespace import like packages, procedures so on. If you click on the info  button it will give you a full list. 

{{site.data.alerts.note}} This will only import objects for users that have objects within the tablespaces that have been migrated. Any other users will have to be imported separately. {{site.data.alerts.end}}

The target file storage can be either “ASM” or “File System”. I would strongly recommend using ASM.

When you select either “ASM” or “File System” the drop down list will be populated with a list of storage destinations.

Next you select backup schedule time, for my test I entered 10:00hrs but normally you may run this in the evening really dependent on your database activity.

For the backup source and target destination you will have to ensure that you have enough space to hold the incremental backups. 

Once you have entered all the details click on the “SAVE” button, the tool will attempt to create the directory if it does not exist. Once the save is succefully the button turned green then we can click on the proceed button and you will be prompted for confirmation.

<img title="" src="images/Selection_040.png" />

Here you will notice in the log console that there has been a failure. This is a know issue and the workaround is to click the “Proceed” button again and it should work the second time around. 

<img title="" src="images/Selection_041.png" />

This time screenshot show the confirmation, click on “YES” if you wish to proceed.  After quite a while the screen will return then you can click on the “Sync Status” tab.

<img title="" src="images/Selection_042.png" />

As you can see in the log console a lot of tasks have been proceesed. Now to see this information we need to click the “Refresh All” button.

<img title="" src="images/Selection_043.png" />

You will see a few jobs on the source database (dependant on the number of tablespaces) and a few on the target database. See appendix A for details of the jobs. On this screen you have 6 button options and you already have used the the “Refresh All” button. The “Change job” button currently only allows you to change the time of a job in either the source or target window as shown below: 

<img title="" src="images/Selection_055.png" />

Just hit “Apply” button once you changed the time or not. The next button “Run Job” will actually run the job interactively meaning you screen will lock until the job completes I would advice changing the job time instead as this does not lock your screen.

<img title="" src="images/Selection_056.png" />

Next button “Show Error” button will display the error if the job has failed. Select the job and click the button.

<img title="" src="images/Selection_057.png" />

Next button “Show Output” button will display the output if the job has failed. Select the job and click the button.

<img title="" src="images/Selection_058.png" />

This is not supported for any version lower than 12c, you will receive the below message

<img title="" src="images/Selection_060.png" />

And lastly the “Change Parallel” button this allows you to tune the process the default parallelism is 5.

<img title="" src="images/Selection_059.png" />

Just change the figure in the table and click the “Apply” button. 

{{site.data.alerts.important}} Ensure that you have enough memory and cpu power before increasing these values, also ensure you do not impact the source database. {{site.data.alerts.end}}

Next we click on the monitor tab you will see a bank screen as below:

<img title="" src="images/Selection_044.png" />

Hit the refresh button 

<img title="" src="images/Selection_045.png" />

In this screen you will see details of the tablespaces that are being migrated along with the file id and the backup scn.

As the schedule jobs run you will inavertantly see new jobs being added and old jobs being removed by the tool. 

<img title="" src="images/Selection_061.png" />

In the screenshot above you can see three new job for tablespace EXAMPLE2, one to transfer the backup file another to convert the file and the final job to restore the backup file. If you check the alert log you will see details of the restore as shown here

<img title="" src="images/Selection_062.png" />

as you can see it was successful.

Now we move to the “Final Steps” you only go to this tab if you are ready to do the switch over from source to target.

<img title="" src="images/Selection_067.png" />

You have two options here Tablespace only and Full Database import. The screen clearly explains what either of the options do. If you have not selected all the tablespace from the Tablespaces tab then you will not be allowed to select the “Full Database” button. It is only available when you select all tablespaces. You will need to ensure that the “Source” pane on the Tablespace tab is empty as shown below. If not then this option will not be available.

<img title="" src="images/Selection_072.png" />

Both buttons will do the same steps apart from the import stage which is the only difference. 

The process scheduled is called &lt;Name&gt;_FINAL_STEPS. This will start to disable all the scheduled jobs and run then for the final time and the state will change to “BROKEN” as shown below. Once this is done then it will start the selected import.

<img title="" src="images/Selection_077.png" />

Finally you will need to check the import log files there will always be some errors and which you will need to rectify yourselfs for now. Good luck!

## Appendix A

### Job Information

On the Source database:

<table id="table98" class="display" width="100%">
      <tr>
        <td>&lt;Process Name&gt;_BACKUP_JOB_&lt;tablespace&gt; </td>
        <td>You will see a number of these jobs running dependant on the number of tablespaces you are migrating.<br><br>
            The reason for having invidual jobs so that if there is a failure it only affects the single tablespace.
        </td>
      </tr>
      <tr>
        <td>&lt;Names&gt;_REFRESH_DF</td>
        <td>This process maintains a list of backup files generated by the process &lt;Process Name&gt;_BACKUP_JOB_&lt;tablespace&gt;
        </td>
      </tr>
</table>

On the Target database:

<table id="table99" class="display" width="100%">
      <tr>
        <td>&lt;Process Names&gt;_CHECK_NEW_DATAFILES </td>
        <td>If any new datafiles are added on the Source database this process will enlist these files so that they are included as part of the migration.
        </td>
      </tr>
      <tr>
        <td>&lt;Process Names&gt;_DATAFILES_TOBE_TRANSFERRED</td>
        <td>This picks up any new datafiles that were enlisted and starts the transfer process.
        </td>
      </tr>
      <tr>
        <td>&lt;Process Names&gt;_SUBMIT_FILE_TRANSFER_&lt;file_id&gt;</td>
        <td>This job is started by the process &lt;Process Names&gt;_DATAFILES_TOBE_TRANSFERRED which transfers the tablespaces datafiles to the target storage
        </td>
      </tr>
      <tr>
        <td>&lt;Process Names&gt;_CHK_BAKFILES_TOBE_TRANSFERRED</td>
        <td>This transfers any new backup from the source backup location to the target backup location.
        </td>
      </tr>
      <tr>
        <td>&lt;Process Names&gt;_CHK_BAKFILES_TOBE_RESTORED</td>
        <td>This process restores any transferred backups.
        </td>
      </tr>
      <tr>
        <td>&lt;Process Names&gt;_BAK_FILE_&lt;tablespace_name&gt;_&lt;SCN&gt;_&lt;backup piece&gt;</td>
        <td>This job transfers the backup piece from source to target.
        </td>
      </tr>
      <tr>
        <td>&lt;Process Names&gt;_CONV_FILE_&lt;tablespace_name&gt;_&lt;SCN&gt;_&lt;backup piece&gt;</td>
        <td>This job converts the file from source endian to target endian.
        </td>
      </tr>
      <tr>
        <td>&lt;Process Names&gt;_REST_FILE_&lt;tablespace_name&gt;_&lt;SCN&gt;_&lt;backup piece&gt;</td>
        <td>This job restores the backup piece to the tablespace.
        </td>
      </tr>
      <tr>
        <td>&lt;Process Names&gt;_HSKBAK_FILE</td>
        <td>This job deleted all backup pieces that have been successfully restored.
        </td>
      </tr>
</table>

### Logs:

<table id="table97" class="display" width="100%">
      <tr>
        <td>Console Logs</td>
        <td>&lt;path of your choice&gt;/migration/log/*.log Every time to restart the console a new log is generated.
        </td>
      </tr>
      <tr>
        <td>Backup Log Files</td>
        <td>&lt;source backup location&gt;/&lt;tablespace_name&gt;/scn_&lt;scn no.&gt;/*.out/p
        </td>
      </tr>
      <tr>
        <td>Datapump Logs</td>
        <td>&lt;target backup location&gt;<br><br>
            - Tablespace Import 3 files<br>
            a) USERIMPORT_&lt;date&gt;_&lt;time&gt;.log<br>
            b) TBSIMPORT_&lt;date&gt;_&lt;time&gt;.log<br>
            c) FULLUSRIMPORT_&lt;date&gt;_&lt;time&gt;.log<br><br>
            - Full Database Migration 1 file<br>
            a) FULLDBIMPORT_&lt;date&gt;_&lt;time&gt;.log
        </td>
      </tr>
</table>

Where is path defined:

&lt;source backup location&gt; - Details entered in the GUI via tab Options “Source Location”  
&lt;target backup location&gt; - Details entered in the GUI via tab Options “Target Location”/p
