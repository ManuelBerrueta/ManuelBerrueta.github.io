---
title: "Code Hacking Tips + Snippets"
date: "2022-07-15"
tags: 
  - "blueteam"
  - "ctf"
  - "cybersecurity"
  - "exploit"
  - "hacking"
  - "infosec"
  - "pentesting"
  - "redteam"
  - "security"
---

# Shell Hacks

#### String Multiplier

**PowerShell**: `$temp = "A" * 100000`  
\--You can even pipe it to clip for an easy copy and paste :)  
**Python**: `python -c "print('A' * 10000)"`  
**Bash**: `temp=$(printf 'A%.0s' {1..10000})`

* * *

# PowerShell

#### Running `cmd` commands inside PowerShell

`$tempDir = cmd.exe /c "dir /s /b C:\"`

#### Encoding and Running Encoded Commads

```powershell
$CommandToExecute = "Get-ChildItem"
$encodedcommand = [Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($CommandToExecute))
powershell -exec bypass -w 1 -enc $encodedcommand
```

## Windows Named Pipes

#### Listing Named Pipes w/ PowerShell

**Should work with older versions + Pwsh 7.x**: `[System.IO.Directory]::GetFiles("\\.\\pipe\\")`  
**Works in Windows PowerShell only at this time (Issue in pwsh 7.x)**: `(get-childitem \\.\pipe\).FullName`

### Sample Named Pipe Client

```powershell
$pipeName = 'myPipe'
$pipe = new-object System.IO.Pipes.NamedPipeClientStream '.', $pipeName, 'In', 'None', 'Impersonation'
$pipe.Connect(1000)
$sr = new-object System.IO.StreamReader $pipe
while (($data = $sr.ReadLine()) -ne $null) { Write-Host "Received: $data" -ForegroundColor Magenta }
$sr.Dispose()
$pipe.Dispose()
```

### Sample Named Pipe Server

```powershell
$pipeName = 'myPipe'
$pipe = new-object System.IO.Pipes.NamedPipeServerStream $pipeName,'Out'
$pipe.WaitForConnection()
$sw = new-object System.IO.StreamWriter $pipe
$sw.AutoFlush = $true
$sw.WriteLine("Server pid is $pid")
$sw.Dispose()
$pipe.Dispose()
```

**Reference**:

- [StackOverflow - PowerShell Named Pipe: no connection?](https://stackoverflow.com/questions/24096969/powershell-named-pipe-no-connection)
- [StackOverflow - Asynchronous named pipes](https://stackoverflow.com/questions/31338421/asynchronous-named-pipes-in-powershell-using-callbacks)

* * *

# VSCode Tips

#### Join your multi-line list to a single line:

`Ctrl + A` (to Select All Text)  
`Ctrl + Shift + P` > `Join Lines`  
**Scenario**: Multi-line list of whatever (users, machines, addresses, etc...), but need it in one line!

* * *
