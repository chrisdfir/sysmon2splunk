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



## Splunk
### What is Splunk?
Splunk is a powerful data analytic tool that allows for the parsing and visualizations of big data. Splunk deployments can have several different architectures but for the purpose of this write-up, all components of Splunk have been deployed into a single VM. A [Splunk.com](https://www.splunk.com) Account will be required to download the necessary components of this capability. 

### Universal Forwarder
Splunk utilizes its own Universal Forwarder to send data to the Splunk indexer. In this case, the data sent from our endpoints to the Splunk server will be our Sysmon event logs.  
*https://www.splunk.com/en_us/download/universal-forwarder.html*

### Splunk Add-on for Microsoft Sysmon
The Splunk Add-on for Microsoft Sysmon is a highly-rated application built by Splunk Works in an effort to provide a data input and CIM-compliant field extractions for Microsoft Sysmon. Essentially, this add-on will take the unstructured chunk of data that makes up each log and chop it up into structured fields and values.  
*https://splunkbase.splunk.com/app/1914/*

## Instructions
1. Ensure access to the Splunk web console *(Default: http://(Splunk IP):8000)*  
  * If web console is inaccessible, validate appropriate firewall configuration. Splunk defaults:
    * TCP/8000
    * TCP/9997
2. Login to Splunk web console and set up config to receive data.  
  * If Splunk **is not** configured to receive data from a remote source:  
    * Settings > Forwarding and receiving > + Add new under Configure receiving  
    * Default port is 9997 > Save  
  * If Splunk **is** configured to receive data from a remote source:  
    * Take note of receiving port by reviewing Forwarding and receiving under Settings  
3. Create index for Sysmon  
  * Settings > Indexes > New Index  
  * Type **sysmon** for the index name, make necessary environment configuration changes, and click Save  
4. Install the Splunk Add-on for Microsoft Sysmon on the Splunk server *(https://splunkbase.splunk.com/app/1914/)*  
5. Download Microsoft Sysinternals Sysmon *(https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)*  
6. Download the SwiftOnSecurity Sysmon configuration file *(https://github.com/SwiftOnSecurity/sysmon-config)*
  * Export the Sysmon executables and this config into the same folder. I chose `C:\sysmon` for ease of use.
7. Open a Command Prompt or PowerShell window with the appropriate permissions to install software on the host and run the following command to initiate the Sysmon installation: 
```
sysmon.exe -accepteula -i sysmonconfig-export.xml
```
8. Verify logs are generating by opening the Windows Event Viewer.  
  * Start > Event Viewer *(eventvwr.msc)*  
  * Application and Service Logs > Microsoft > Windows > Sysmon > Operational  
9. Install the Splunk Universal Forwarder *(https://www.splunk.com/en_us/download/universal-forwarder.html)*
  * Update `inputs.conf` file with the code below at the following file location:  
***C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf***
```
[default]
host = <Computer Name> #CHANGE ME

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = false
renderXml = true
index = sysmon
source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
```
10. Restart SplunkForwarder Service
  * Start > Services *(services.msc)*
  * PowerShell: `Restart-Service SplunkForwarder`  
11. Check Splunk `sysmon` index for ingested data
```
index=sysmon
```
12. Chop up the data and create visualizations for effective and efficient analysis.

*Note: If your organization pays for a Splunk Enterprise license, you may be eligible to request a 50GB/day developer license. This provides for a much larger playing field than the free license limitation of 500MB/day. Ensure your company email address is on your Splunk.com account when requesting a developer license here:   https://www.splunk.com/en_us/resources/personalized-dev-test-licenses.html*

**Updates with Sysmon queries and dashboards to come.**
