---
title: "KQL For Security"
date: "2022-04-30"
tags: 
  - "azure"
  - "azuredefender"
  - "azuremonitor"
  - "azuresecurity"
  - "azuresentinel"
  - "blueteam"
  - "cloud"
  - "cloudsecurity"
  - "cybersecurity"
  - "defenders"
  - "detection"
  - "detectionengineer"
  - "dfir"
  - "hacking"
  - "incidentresponse"
  - "infosec"
  - "kql"
  - "kusto"
  - "kustoquerylanguage"
  - "loganalytics"
  - "microsoftdefender"
  - "microsoftsentinel"
  - "pentesting"
  - "redteam"
  - "securityanalysis"
  - "securityanalyst"
  - "securityengineer"
  - "securitymonitoring"
  - "sentinel"
  - "siem"
  - "soc"
  - "threathunt"
  - "threathunting"
---

Kusto Query Language (`KQL`) can be used for all kinds of security shenanigans. It is often used in incident response and threat hunting, but it can be leveraged in different ways for different needs.

I use `KQL` often and while I am definitely not a pro, I am on a never-ending journey to always get better with it. I am going to be on a continuously dumping/updating resources, queries, tips, etc... in here so that you can leverage for your different security needs.

## `KQL Query` Goodies

#### Using Lists to query against

```KQL
let interestingCmds = dynamic([@"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe", @"C:\Windows\System32\cscript.exe", "cmd"]);
SecurityEvent
| where ParentProcessName in~ (interestingCmds)
| take 20
```

- `has_any` does substring matching
- `project` filters to only specified column(s)
- `project-away` excludes the specified column(s)

```KQL
let interestingCmds = dynamic([@"powershell.exe", @"cscript.exe", "cmd"]);
SecurityEvent
| where Process has_any (interestingCmds)
| take 20
| project Account, AccountType, Computer, Activity, CommandLine, FileHash, FilePath, NewProcessName, ParentProcessName, Process
```

#### Search for a string in any of the columns

```KQL
search in (SecurityEvent) "powershell"
| take 10 
```

#### Interpret a string as JSON

Using the [`parse_json()`](\(https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/parsejsonfunction\)) function, we can convert a string into a dynamic value so we can work with the Json object(s).

#### Parse a command line string

Using the [`parse_command_line()`](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/parse-command-line) function, to (no surprise) parse the command line into a dynamic array, allowing us to extract the pieces for analysis/comparisons etc... The limitation right now, is that it only supports `windows` command line parsing.

```KQL
print parse_command_line("powershell.exe -windowstyle hidden -file C:\\hax.ps1", "windows")
```

```KQL
//Output:
["powershell.exe","-windowstyle","hidden","-file","C:\\hax.ps1"]
```

#### Using `distinct` + `project` == `summarize`?!

Ref: [StackOverflow - Using both `distinct` and `project`](https://stackoverflow.com/questions/62291692/using-both-distinct-and-project)

* * *

#### Azure Sentinel

- Check out the [`Azure-Sentinel GitHub repo`](https://github.com/Azure/Azure-Sentinel) for a lot of great queries!

* * *

#### Useful Blog Posts + References

- [KQL for Incident Response by DaRT](https://techcommunity.microsoft.com/t5/security-compliance-and-identity/leveraging-the-power-of-kql-in-incident-response/ba-p/3044795)
- [Malicious KQL](https://securecloud.blog/2022/04/27/azure-monitor-malicious-kql-query/)
- \[Microsoft's Azure Demo Environment\] (https://portal.azure.com/#blade/Microsoft\_Azure\_Monitoring\_Logs/DemoLogsBlade) for a lot of these!

* * *

#### DOCS

- [Azure Monitor Sample Queries](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Azure Data Explorer Sample Queries](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/samples?pivots=azuredataexplorer)
- [Query Best Practices](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/query/best-practices)
- [Quick Reference Guide](https://docs.microsoft.com/en-us/azure/data-explorer/kql-quick-reference)
- [Get started with log queries in Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/get-started-queries)

* * *

#### TOOLS

- [Kusto.Explorer](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/tools/kusto-explorer#installing-kustoexplorer)
- [Azure Data Explorer](https://docs.microsoft.com/en-us/azure/data-explorer/)
- [`msticpy`](https://msticpy.readthedocs.io/en/latest/)
- [Kqlmagic](https://docs.microsoft.com/en-us/sql/azure-data-studio/notebooks/notebooks-kqlmagic?view=sql-server-ver16)

* * *
