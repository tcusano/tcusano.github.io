# Sign in to a Linux virtual machine in Azure by using Microsoft Entra ID and OpenSSH

Microsoft Documentation

## Requirements

### Network

VM network configuration must permit outbound access to the following endpoints over TCP port 443.

Azure Global:
```
https://packages.microsoft.com: For package installation and upgrades.
http://169.254.169.254: Azure Instance Metadata Service endpoint.
https://login.microsoftonline.com: For PAM-based (pluggable authentication modules) authentication flows.
https://pas.windows.net: For Azure RBAC flows.
```

### Virtual machine

Ensure that your VM is configured with the following functionality:

1. System-assigned managed identity. This option is automatically selected when you use the Azure portal to create VMs and select the Microsoft Entra login option. You can also enable system-assigned managed identity on a new or existing VM by using the Azure CLI.

<span style="color:yellow"> NOTE: This is done as part of the VM build. </span>

2. aadsshlogin and aadsshlogin-selinux (as appropriate). These packages are installed with the AADSSHLoginForLinux VM extension. The extension is installed when you use the Azure portal or the Azure CLI to create VMs and enable Microsoft Entra login (Management tab).

<span style="color:yellow"> NOTE: This will done via a policy </span>

### Configure role assignments for the VM

Now that you've created the VM, you need to assign one of the following Azure roles to determine who can sign in to the VM. To assign these roles, you must have the Virtual Machine Data Access Administrator role, or any role that includes the Microsoft.Authorization/roleAssignments/write action such as the Role Based Access Control Administrator role. However, if you use a different role than Virtual Machine Data Access Administrator, we recommend you add a condition to reduce the permission to create role assignments.

Virtual Machine Administrator Login: Users who have this role assigned can sign in to an Azure virtual machine with administrator privileges.

Virtual Machine User Login: Users who have this role assigned can sign in to an Azure virtual machine with regular user privileges.

To allow a user to sign in to a VM over SSH, you must assign the Virtual Machine Administrator Login or Virtual Machine User Login role on the resource group that contains the VM and its associated virtual network, network interface, public IP address, or load balancer resources.

An Azure user who has the Owner or Contributor role assigned for a VM doesn't automatically have privileges to Microsoft Entra sign in to the VM over SSH. There's an intentional (and audited) separation between the set of people who control virtual machines and the set of people who can access virtual machines.

<span style="color:yellow"> NOTE: This is also done via a policy. </span>

## Connecting to the VM

### Connecting with az ssh

1. Install the SSH extension for the Azure CLI

Run the following command to add the SSH extension for the Azure CLI:
```
az extension add --name ssh
```
The minimum version required for the extension is 0.1.4. Check the installed version by using the following command:

Check install
```
az extension show --name ssh
```
2. Connecting to VM 

a) az account clear

b) az login 

c) az account set --subscription xxxxxxxxxxxxxxxxxxxxxxxxxxxxx

d) az ssh vm -n AZTONYL50T -g RG_Tonytest --prefer-private-ip

### Connecting with SSH clients that support OpenSSH

Connecting to VM 

a) az account clear

b) az login 

c) az account set --subscription xxxxxxxxxxxxxxxxxxxxxxxxxxxxx

d) az ssh config --file C:/users/U46630/.ssh/config -n AZTONYL50T -g RG_Tonytest

<span style="color:yellow"> NOTE: Run above only when you require ssh key</span>

e) ssh RG_Tonytest-AZTONYL50T

<span style="color:red"> NOTE:
Run sudo with Microsoft Entra login
After users who are assigned the VM Administrator role successfully SSH into a Linux VM, they'll be able to run sudo with no other interaction or authentication requirement. Users who are assigned the VM User role won't be able to run sudo.
</span>

Information:

Config file will contain:

```
Host RG_Tonytest-AZTONYL50T
        User !fred@x.com
        HostName 10.85.17.13
        CertificateFile "C:\users\fred\.ssh\az_ssh_config\RG_Tonytest-AZTONYL50T\id_rsa.pub-aadcert.pub"
        IdentityFile "C:\users\fred\.ssh\az_ssh_config\RG_Tonytest-AZTONYL50T\id_rsa"
Host 10.85.17.13
        User !fred@x.com
        CertificateFile "C:\users\fred\.ssh\az_ssh_config\RG_Tonytest-AZTONYL50T\id_rsa.pub-aadcert.pub"
        IdentityFile "C:\users\fred\.ssh\az_ssh_config\RG_Tonytest-AZTONYL50T\id_rsa"
```