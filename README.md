![alt text](https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcTWZszcRQrmUkjlRKlL2PuLO9Yk_aSLcm0iTA&usqp=CAU) ![alt text](https://threatconnect.com/wp-content/uploads/Splunk_Logo_Website.png)
# sysmon2splunk
Generating Sysmon events with the SwiftOnSecurity configuration and ingesting/normalizing the dataset in a remote Splunk instance.

## Objectives
* Use Microsoft Sysinternals Sysmon on several Microsoft Windows endpoints to generate granular security-related event logs.
* Push the Sysmon event logs to an index on a remote Splunk virtual machine.
* Parse all the things.

## Requirements
* [Splunk.com](https://www.splunk.com) Account
* Splunk Universal Forwarder
* Splunk   *(Note: This write-up does not go over Splunk installation. Therefore, having an instance of Splunk stood up is a pre-req.)*
* Host running Microsoft Windows
* Sysmon
* SwiftOnSecurity Sysmon Config
* Text Editor


## Sysmon
### What is Sysmon?
*System Monitor (Sysmon)* is a Windows system service and device driver that, once installed on a system, remains resident across system reboots to monitor and log system activity to the Windows event log. It provides detailed information about process creations, network connections, and changes to file creation time.  
*https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon*

### SwiftOnSecurity config 
The Sysmon configuration file from SwiftOnSecurity Sysmon provides high-quality event tracing to support threat hunting, compromise assessments, incident response, etc.  
*https://github.com/SwiftOnSecurity/sysmon-config*

```
sysmon.exe -accepteula -i sysmonconfig-export.xml
```

## Splunk
### What is Splunk?
Splunk.com account

### Universal Forwarder
https://www.splunk.com/en_us/download/universal-forwarder.html

***C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf***
```
[default]
host = <Computer Name>

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = false
renderXml = true
index = sysmon
source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
```
### Splunk Add-on for Microsoft Sysmon
https://splunkbase.splunk.com/app/1914/
