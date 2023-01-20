#requires -version 2
<#
.SYNOPSIS
  Move agent to another tenant of Trend Micro Cloud One Endpoint & Workload Security
.DESCRIPTION
  Moving Trend Micro Cloud One Endpoint & Workload Security agent from old tenant to new tenant.
  This script can be utilized via deployment tool or run though Custom Scripts in Trend Micro Vision One
.PARAMETER <Parameter_Name>
    <Brief description of parameter input required. Repeat this attribute if required>
.INPUTS
  None
.OUTPUTS
  Log file stored in C:\Windows\Temp\C1-Agent-Migration.log
  Log file Configuration stored in C:\Windows\Temp\C1-Agent-Migration.log
.NOTES
  Version:        1.0
  Author:         Soren Isager
  Creation Date:  January 2023
  Purpose/Change: Initial script development
  
.EXAMPLE
 None
#>

#---------------------------------------------------------[Initialisations]--------------------------------------------------------

#Set Error Action to Silently Continue
$ErrorActionPreference = "SilentlyContinue"

#----------------------------------------------------------[Declarations]----------------------------------------------------------

#Log File Info
$global:LogPath = "C:\Windows\Temp"
$global:LogName = "C1-Agent-Migration.log"
$global:LogFilePath = Join-Path -Path $global:LogPath -ChildPath $global:LogName

# Cloud One Endpoint & Workload Security - (THE DESTINATION OF AGENT)
$C1ManagerUrl = "<>"
$C1TenantID = "<>"
$C1Token = "<>"
$C1PolicyID = "<>"

#-----------------------------------------------------------[Functions]------------------------------------------------------------

function Write-Log {
  param (
      [Parameter(Mandatory=$False, Position=0)]
      [String]$Entry
  )

  "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss.fff') $Entry" | Out-File -FilePath $global:LogFilePath -Append
}

Function ReactivateAgent {
  Param(
    [Parameter(Mandatory=$True)][String]$C1ManagerUrl,
    [Parameter(Mandatory=$True)][String]$C1TenantID,
    [Parameter(Mandatory=$True)][String]$C1Token,
    [Parameter(Mandatory=$True)][String]$C1PolicyID
  )
  
  Begin{
    Write-Log -Entry "Starting Reactivation on agent"
  }
  
  Process{
    Try{
      # Validation of client exsistence

      # Set Working path
      Write-Log -Entry "Setting Working Environment"
      Set-Location "$($Env:ProgramFiles)\Trend Micro\Deep Security Agent\"

      # Reset current client
      Write-Log -Entry "Reset Agent configuration"
      .\dsa_control -r 

      # Activate into destination C1 tenant
      Write-Log -Entry "Activation on destination C1 Tenant: $($C1ManagerUrl) : $($C1TenantID)"
      .\dsa_control -a dsm://$($C1ManagerUrl)/ "tenantID:$($C1TenantID)" "token:$($C1Token)" "policyid:$($C1PolicyID)"  

      # Get Configuration information
      Write-Log -Entry "Getting Agent Policy Information"
      .\sendCommand.cmd --get GetConfiguration | out-file -FilePath "$($global:LogPath)/Config-$($global:LogName)" 

    }
    
    Catch{
        Write-Log -Entry $_.Exception -ExitGracefully $True
      Break
    }
  }
  
  End{
    If($?){
    Write-Log -Entry "Completed Successfully."
    Write-Log -Entry " "
    }
  }
}


#-----------------------------------------------------------[Execution]------------------------------------------------------------

## Execute Reactivation of agent

# Reactivate Agent
ReactivateAgent `
-C1ManagerUrl $C1ManagerUrl `
-C1TenantID $C1TenantID `
-C1Token $C1Token `
-C1PolicyID $C1PolicyID
