---
title: Jenkins
tags:
keywords:
last_updated: July 27, 2019
sidebar: mydoc_sidebar
permalink: mydoc_scan_pipeline_scm_log.html
folder: mydoc
---

# How to scan pipeline scm log

On certain occassions I have experienced cases where I received errors during the SCM scanning and the pipejob job completes successfully. This caused the new build to deploy the incorrect versionis of the objects. Therefore as part of my build script I added the below check which would run after the builds have all completed and verify the success of all objects within the build. 

```js
import hudson.model.*
import Jenkins.*
import Jenkins.model.*
import hudson.*
import jenkins.model.Jenkins
import java.text.SimpleDateFormat

def VSTARTWITH = build.getBuildVariables().get('START_WITH')
def failedjob = 0
def name
def item
def job
def res
def jobpath
def proc
def buf

........ +

// Check for any build failures
wantedJobs = hudson.model.Hudson.instance.items.findAll{ job-> job.name.startsWith(VSTARTWITH)== true }
for (jobname in wantedJobs ){
	if (jobname == 'VSTARTWITH'){
		name = jobname.name

		//If we want to add more then one job
		items = new LinkedHashSet();
		job = Hudson.instance.getJob(name)

		// Check for FATAL errors
		jobpath = job.getRootDir()
		proc = "grep -i fatal ${jobpath}/indexing/indexing.log".execute()
		buf = new StringBuffer()
		proc.consumeProcessErrorStream(b) 
		res = proc.text
  		if (res) {
    		// We attempt to retry the scan
    		println "Rerunning job: ${jobname.name}"
    		jobExec = jobname.scheduleBuild2(0)
    		jobResult = jobExec.get()
            if ("${jobResult.result}" == "FAILURE") {
            	println '#####################'
                println 'Job failed:   ' + jobname.name            
                failedjob=1
            } else {
                println '#####################'
                println jobResult.result
                println '#####################'
            }
        }
     }
}
if (failedjob == 1) {
	throw new Exception("Error failed build has occurred.")
}
````

{{site.data.alerts.note}} The above script is part of another script which submits the build and waits until the parallel build has completed before doing the checks. {{site.data.alerts.end}}

