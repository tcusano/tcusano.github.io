# Deploy specific resource

## Exaple Rapid7 package

cd %rootdir%/InfraAzureAutomation\powershell

<span style="color:yellow"> NOTE: my root dir is C:\Deployment this is where I downloaded the source from devops.azure. </span>

pwsh ./az_deploy.ps1 -resource pkg_rapid7 -account MGT01 -region uksouth


<span style="color:yellow"> NOTE: There is also -destroy option. </span>