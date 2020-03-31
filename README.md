# Install-SqlServer

This script installs MS SQL Server silently from ISO image that can be available locally or on SMB share.
Transcript of entire operation is recorded in the log file.

The script lists parameters provided to the native setup but hides sensitive data. See the provided links for SQL Server silent install details.

The installer is tested with SQL Servers 2016-2019.

## Prerequisites

1. MS SQL Server ISO image

## Usage

The fastest way to install core SQL Server is via this command:

```ps1
$params = @{
    IsoPath      = "<path_to>/en_sql_server_2019_developer_x64_dvd_e0079655.iso"
    Credentials  = Get-Credential domain user     # SMB Share credentials where ISO resides if needed
}
.\Install-SqlServer.ps1 @params
```

This assumes number of [default parameters](Install-SqlServer.ps1#L24-60) and installs features `SQLEngine`, `Replication` and `RS`, 

## Notes

- If share is already mounted Credentials are not required to access remote ISO file.
- SQL Server Management Studio isn't distributed along with SQL Server any more. Install via chocolatey: [`cinst sql-server-management-studio`](https://chocolatey.org/packages/sql-server-management-studio)
- To check if existing SQL Server instance is registered execute `SELECT @@version`.

## Troubleshooting

### Installing on remote machine using PowerShell remote session 

The following errors may occur:

    There was an error generating the XML document 
        ... Access denied
        ... The computer must be trusted for delegation and the current user account must be configured to allow delegation

**The solution**: Use WinRM session parameter `-Authentication CredSSP`.
        
To be able to use it, the following settings needs to be done on both local and remote machine:

1. On local machine using `gpedit.msc`, go to *Computer Configuration -> Administrative Templates -> System -> Credentials Delegation*.<br>
Add `wsman/*.<domain>` (set your own domain) in the following settings
    1. *Allow delegating fresh credentials with NTLM-only server authentication*
    2. *Allow delegating fresh credentials*
1. The remote machine must be set to behave as CredSSP server with `Enable-WSManCredSSP -Role server`

## Links

- [Install SQL Server from the Command Prompt](https://docs.microsoft.com/en-us/sql/database-engine/install-windows/install-sql-server-2016-from-the-command-prompt)
    - [Features](https://docs.microsoft.com/en-us/sql/database-engine/install-windows/install-sql-server-2016-from-the-command-prompt#Feature)
    - [Accounts](https://docs.microsoft.com/en-us/sql/database-engine/install-windows/install-sql-server-2016-from-the-command-prompt#Accounts) 
- [Download SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)
- [Editions and features](https://docs.microsoft.com/en-us/sql/sql-server/editions-and-components-of-sql-server-2017)
