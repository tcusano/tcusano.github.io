# Setting up environment for deployment:

Prerequisits: 

PWSH

GIT 

Terraform

[AZ client](Install the Azure CLI for Windows | Microsoft Learn) 

After installing AZ client:

```
If you're using Azure CLI over a proxy server that uses self-signed certificates, the Python requests library used by the Azure CLI may cause the following error: SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",). To address this error, set the environment variable REQUESTS_CA_BUNDLE to the path of CA bundle certificate file in PEM format.

OS	                    Default certificate authority bundle
Windows 32-bit	        C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem
Windows 64-bit	        C:\Program Files\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem
Ubuntu/Debian Linux	    /opt/az/lib/python<version>/site-packages/certifi/cacert.pem
CentOS/RHEL/SUSE Linux	/usr/lib64/az/lib/python<version>/site-packages/certifi/cacert.pem
macOS	                /usr/local/Cellar/azure-cli/<cliversion>/libexec/lib/python<version>/site-packages/certifi/cacert.pem

Append the proxy server's certificate to the CA bundle certificate file, or copy the contents to another certificate file. Then set REQUESTS_CA_BUNDLE to the new file location. 
Here's an example:

Copy
<Original cacert.pem>

-----BEGIN CERTIFICATE-----
<Your proxy's certificate here>
-----END CERTIFICATE-----

Some proxies require authentication. The format of the HTTP_PROXY or HTTPS_PROXY environment variables should include the authentication, such as HTTPS_PROXY="https://username:password@proxy-server:port".

```

## Step 1 - Create working folder

cd "c:\Users\U4xxxx\OneDrive\"

mkdir repo

mklink /D C:\deployment "C:\Users\U4xxxx\OneDrive\Repo"

cd c:\deployment\repo

## Step 2 - Setup git config if not larady done
Sample

```# This is Git's per-user configuration file.
[user]
        name = TonyCusano
        email = <email>
[http]
      proxy = http://127.0.0.1:9000
        sslVerify = false
        postBuffer = 1048576000
        sslBackend = openssl
[credentials "dev.azure.com"]
        tokenduration = 24
        preserve = true
        helper = store --file ~/.my-credentials
```

## Step 3 - Clone repository

git clone <url>

## Step 4 - Create access file

cd InfraAzureAutomation/powershell

notepad $HOME\az_access.json

Add the following: 
{
"client_id": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
"client_secret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
"tenant_id": "xxxxxxxxxxxxxxxxxxxxxx",
"build_user": "RUN-TFM-AWS-BLD",
"build_secret": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
"vmpath": ""
}

<span style="color:yellow"> NOTE: The client secret and build_secret has to be secure string. </span>

```
To create secure string use cmd:

ConvertTo-SecureString -AsPlainText -Force -String "<secret>" |ConvertFrom-SecureSting

copy the output
```

## Step 4 - Update your powershell environment
edit file $PROFILE

```
${ENV:HTTP_PROXY}="http://localhost:9000"
${ENV:HTTPS_PROXY}="http://localhost:9000"
${ENV:REQUESTS_CA_BUNDLE}="C:\Program Files\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem"
${ENV:PATH}="${ENV:PATH};c:\terraform;"
Register-ArgumentCompleter -Native -CommandName az -ScriptBlock {
    param($commandName, $wordToComplete, $cursorPosition)
    $completion_file = New-TemporaryFile
    $env:ARGCOMPLETE_USE_TEMPFILES = 1
    $env:_ARGCOMPLETE_STDOUT_FILENAME = $completion_file
    $env:COMP_LINE = $wordToComplete
    $env:COMP_POINT = $cursorPosition
    $env:_ARGCOMPLETE = 1
    $env:_ARGCOMPLETE_SUPPRESS_SPACE = 0
    $env:_ARGCOMPLETE_IFS = "`n"
    $env:_ARGCOMPLETE_SHELL = 'powershell'
    az 2>&1 | Out-Null
    Get-Content $completion_file | Sort-Object | ForEach-Object {
        [System.Management.Automation.CompletionResult]::new($_, $_, "ParameterValue", $_)
    }
    Remove-Item $completion_file, Env:\_ARGCOMPLETE_STDOUT_FILENAME, Env:\ARGCOMPLETE_USE_TEMPFILES, Env:\COMP_LINE, Env:\COMP_POINT, Env:\_ARGCOMPLETE, Env:\_ARGCOMPLETE_SUPPRESS_SPACE, Env:\_ARGCOMPLETE_IFS, Env:\_ARGCOMPLETE_SHELL
}
Set-PSReadlineKeyHandler -Key Tab -Function MenuComplete
```

## Step 5 - Now you should the az_vm.ps1 or the az_deploy.ps1
