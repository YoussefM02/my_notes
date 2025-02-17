- An event is any observable occurrence in your IT infrastructure.
- An alert is a notification that an event has happened.
- An incident is one or more related events that negatively affects IT systems and impacts on the business.

- **Event logs** record events to provide an audit trail
- The log files with the .evtx file extension typically reside in `C:\Windows\System32\winevt\Logs`

- A log contains a record of events.

- Log classification:
	- **==`System Logs:`==** records h/w changes and system changes
	- **==`Security Logs`==**: Records events connected to logon and logoff, file deletions, etc. 
	- ==**`Application Logs`**==: Records events related to applications installed on a system. like app errors, events, and warnings
	- ==**`Directory Service Events:`**==Active Directory changes and activities are recorded in these logs, mainly on domain controllers.
	- ==**`File Replication Service Events:`**== Records events associated with Windows Servers during the sharing of Group Policies and logon scripts to domain controllers, from where they may be accessed by the users through the client servers.
	- ==**`DNS Event Logs:`**==DNS servers use these logs to record domain events.
	- ==**`Custom Logs:`**== Events are logged by applications that require custom data storage.

- Log types:
	- ==`Error`==: an event that indicates significant problem like data or functionality loss.
	- ==`Warning`==: an event that is not as significant but may indicate a possible future problem.
	- ==`Information`==: an event that describes the successful operation of an app, driver or service. like the loading of a network driver successfully. 
	- ==`Success audit`==: an event that records an audited security access attempt that is successful. like a logon attempt that worked
	- ==`Failure audit`==: an event that records an audited security access attempt that failed. like a logon attempt that failed

- In event viewer's action pane:
	- The **Create Custom View** and **Filter Current Log** are nearly identical. Create custom view can
----

- **==`wevtutil`==** -> enables you to retrieve information about event logs and publishers. You can also use this command to install and uninstall event manifests, to run queries, and to export, archive, and clear logs

- Event Tracing for Windows (ETW) is an efficient kernel-level tracing facility that lets you log kernel or application-defined events to a log file.

- The Event Tracing API is broken into three distinct components:
	- Controllers, which start and stop an event tracing session and enable providers
	- Providers, which provide the events
	- Consumers, which consume the events

- The following diagram shows the event tracing model.

![event tracing model](https://learn.microsoft.com/en-us/windows/win32/etw/images/etdiag2.png)

----
### Get-WinEvent:

- [Documentation](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-5.1#example-8-get-event-log-provider-names-that-contain-a-specific-string)
- cmdlet to get events from event logs and event tracing log files.
- It provides information on event logs and event log providers

- Example for listing providers
```
Get-WinEvent -ListProvider *

Name     : .NET Runtime
LogLinks : {Application}
Opcodes  : {}
Tasks    : {}
```

- Example for log filtering
```
Get-WinEvent -LogName Application | Where-Object { $_.ProviderName -Match 'WLMS' }

   ProviderName: WLMS

TimeCreated                     Id LevelDisplayName Message
-----------                     -- ---------------- -------
12/21/2020 4:23:47 AM          100 Information
12/18/2020 3:18:57 PM          100 Information
12/15/2020 8:50:22 AM          100 Information
12/15/2020 8:18:34 AM          100 Information
12/15/2020 7:48:34 AM          100 Information
12/14/2020 6:42:18 PM          100 Information
12/14/2020 6:12:18 PM          100 Information
12/14/2020 5:39:08 PM          100 Information
12/14/2020 5:09:08 PM          100 Information
```

- It's inefficient to send objects down the pipeline to a `Where-Object` command.
- Instead use FilterHashtable option:
```
Get-WinEvent -FilterHashtable @{
  LogName='Application' 
  ProviderName='WLMS' 
}

SYNTAX -> @{ <name> = <value>; [<name> = <value> ] ...}

```

```
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-PowerShell/Operational'; ID=4104} | Select-Object -Property Message | Select-String -Pattern 'SecureString'
```

- To "grep" in windows use ==`findstr /i`==
---
### XPath Queries:

- XPath, or **XML Path Language** provide a standard syntax and semantics for addressing parts of an XML document and manipulating strings, numbers, and booleans.

![[4iubim5s.bmp]]
- The first tag is the starting point. This can either be an `*` or the word `Event`.
- The next tag is `System`, **Event ID** is **100**.
- Command -> `Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=100'`
- another way to filter for the event above: `Get-WinEvent -LogName Application -FilterXPath '*/System/Provider[@Name="WLMS"]'`
- Can combine 2 queries with `and` keyword 


- To create XPath queries for elements within `EventData`:
- ![[6f9z6usc.bmp]]

- for `TargetUserName` -> `Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="System"'`

---
### Event IDs

- [The Windows Logging Cheat Sheet (Windows 7 - Windows 2012)](https://static1.squarespace.com/static/552092d5e4b0661088167e5c/t/580595db9f745688bc7477f6/1476761074992/Windows+Logging+Cheat+Sheet_ver_Oct_2016.pdf).
- [Spotting the Adversary with Windows Event Log Monitoring](https://web.archive.org/web/20190115215749/https://apps.nsa.gov/iaarchive/customcf/openAttachment.cfm?FilePath=/iad/library/ia-guidance/security-configuration/applications/assets/public/upload/Spotting-the-Adversary-with-Windows-Event-Log-Monitoring.pdf&WpKes=aF6woL7fQp3dJiqyJL2LenrLxuHC7ztGtVNK3x)
- https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor (AD)
- [The Windows 10 and Windows Server 2016 Security Auditing and Monitoring Reference](https://www.microsoft.com/en-us/download/confirmation.aspx?id=52630) (a comprehensive list **over 700 pages**)

- Powershell logging is not enabled by default
- Another feature to enable/configure is **Audit Process Creation**, which will generate **event ID 4688**. This will allow **command-line process auditing**.
- Use ==`Select-Object -Property *`== or ==`Format-List`== to view event data
- `C:\Windows\System32\net1.exe`