---
ms.date:  2017-06-09
schema:  2.0.0
locale:  en-us
keywords:  powershell,cmdlet
online version:  http://go.microsoft.com/fwlink/?LinkID=113324
external help file:  Microsoft.PowerShell.Commands.Management.dll-Help.xml
title:  Get-Process
---

# Get-Process
## SYNOPSIS
Gets the processes that are running on the local computer or a remote computer.
## SYNTAX

### Name (Default)
```
Get-Process [[-Name] <String[]>] [-ComputerName <String[]>] [-Module] [-FileVersionInfo] [<CommonParameters>]
```

### Id
```
Get-Process -Id <Int32[]> [-ComputerName <String[]>] [-Module] [-FileVersionInfo] [<CommonParameters>]
```

### InputObject
```
Get-Process [-ComputerName <String[]>] [-Module] [-FileVersionInfo] -InputObject <Process[]>
 [<CommonParameters>]
```

## DESCRIPTION
The Get-Process cmdlet gets the processes on a local or remote computer.

Without parameters, Get-Process gets all of the processes on the local computer.
You can also specify a particular process by process name or process ID (PID) or pass a process object through the pipeline to Get-Process.

By default, Get-Process returns a process object that has detailed information about the process and supports methods that let you start and stop the process.
You can also use the parameters of Get-Process to get file version information for the program that runs in the process and to get the modules that the process loaded.
## EXAMPLES

### Example 1
```
PS C:\> Get-Process
```

This command gets a list of all of the running processes running on the local computer.
For a definition of each column, see the "Additional Notes" section of the Help topic for Get-Help.
### Example 2
```
PS C:\> Get-Process winword, explorer | format-list *
```

This command gets all available data about the Winword and Explorer processes on the computer.
It uses the Name parameter to specify the processes, but it omits the optional parameter name.
The pipeline operator (|) passes the data to the Format-List cmdlet, which displays all available properties (*) of the Winword and Explorer process objects.

You can also identify the processes by their process IDs.
For example, "get-process -id 664, 2060".
### Example 3
```
PS C:\> get-process | where-object {$_.WorkingSet -gt 20000000}
```

This command gets all processes that have a working set greater than 20 MB.
It uses the Get-Process cmdlet to get all running processes.
The pipeline operator (|) passes the process objects to the Where-Object cmdlet, which selects only the object with a value greater than 20,000,000 bytes for the WorkingSet property.

WorkingSet is one of many properties of process objects.
To see all of the properties, type "Get-Process | Get-Member".
By default, the values of all amount properties are in bytes, even though the default display lists them in kilobytes and megabytes.
### Example 4
```
PS C:\> $a = get-process PS C:\> get-process -inputobject $a | format-table -view priority
```

These  commands list the processes on the computer in groups based on their priority class.
The first command gets all the processes on the computer and then stores them in the $a variable.

The second command uses the InputObject parameter to pass the process objects that are stored in the $a variable to the Get-Process cmdlet.
The pipeline operator passes the objects to the Format-Table cmdlet, which formats the processes by using the Priority view.
The Priority view, and other views, are defined in the PS1XML format files in the Windows PowerShell home directory ($pshome).
### Example 5
```
PS C:\> get-process powershell -computername S1, localhost | ft @{Label="NPM(K)";Expression={[int]($_.NPM/1024)}}, @{Label="PM(K)";Expression={[int]($_.PM/1024)}},@{Label="WS(K)";Expression={[int]($_.WS/1024)}},@{Label="VM(M)";Expression={[int]($_.VM/1MB)}}, @{Label="CPU(s)";Expression={if ($_.CPU -ne $()) { $_.CPU.ToString("N")}}}, Id, MachineName, ProcessName -auto

NPM(K) PM(K) WS(K) VM(M) CPU(s)   Id MachineName ProcessName
------ ----- ----- ----- ------   -- ----------- -----------
6 23500 31340   142        1980 S1          powershell
6 23500 31348   142        4016 S1          powershell
27 54572 54520   576        4428 localhost   powershell
```

This example provides a Format-Table (alias = ft) command that adds the MachineName property to the standard Get-Process output display.
### Example 6
```
PS C:\> get-process powershell -fileversioninfo

ProductVersion   FileVersion      FileName
--------------   -----------      --------
6.1.6713.1       6.1.6713.1 (f... C:\WINDOWS\system32\WindowsPowerShell\v1.0\powershell.exe
```

This command uses the FileVersionInfo parameter to get the version information for the PowerShell.exe file that is the main module for the PowerShell process.

To run this command with processes that you do not own on Windows Vista and later versions of Windows, you must open Windows PowerShell with the "Run as administrator" option.
### Example 7
```
PS C:\> get-process sql* -module
```

This command uses the Module parameter to get the modules that have been loaded by the process.
This command gets the modules for the processes that have names that begin with "sql".

To run this command on Windows Vista (and later versions of Windows) with processes that you do not own, you must start Windows PowerShell with the "Run as administrator" option.
### Example 8
```powershell
PS C:\> $p = Get-WmiObject Win32_Process -Filter "name='powershell.exe'"
PS C:\> $p.GetOwner()


__GENUS          : 2
__CLASS          : __PARAMETERS
__SUPERCLASS     :
__DYNASTY        : __PARAMETERS
__RELPATH        :
__PROPERTY_COUNT : 3
__DERIVATION     : {}
__SERVER         :
__NAMESPACE      :
__PATH           :
Domain           : DOMAIN01
ReturnValue      : 0
User             : user01
```

This command shows how to find the owner of a process.
Because the System.Diagnostics.Process object that `Get-Process` returns does not have a property or method that returns the process owner, the command uses the `Get-WmiObject` cmdlet to get a Win32_Process object that represents the same process.

The first command uses `Get-WmiObject` to get the PowerShell process.
It saves it in the $p variable.

The second command uses the GetOwner method to get the owner of the process in $p.
The output reveals that the owner is Domain01\user01.

### Example 9
```powershell
PS C:\> Get-Process powershell

Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
308      26        52308      61780   567     3.18   5632 powershell
377      26        62676      63384   575     3.88   5888 powershell


PS C:\> Get-Process -Id $PID

Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
396      26        56488      57236   575     3.90   5888 powershell
```

These commands show how to use the $PID automatic variable to identify the process that is hosting the current PowerShell session.
You can use this method to distinguish the host process from other PowerShell processes that you might want to stop or close.

The first command gets all of the PowerShell processes in the current session.

The second command gets the PowerShell process that is hosting the current session.

### Example 10
```
PS C:\> get-process | where {$_.mainWindowTitle} | format-table id, name, mainwindowtitle -autosize
```

This command gets all the processes that have a main window title, and it displays them in a table with the process ID and the process name.

The mainWindowTitle property is just one of many useful properties of the System.Diagnostics.Process object that Get-Process returns.
To view all of the properties, pipe the results of a Get-Process command to the T:Microsoft.PowerShell.Commands.Get-Member cmdlet (get-process | get-member).
## PARAMETERS

### -ComputerName
Gets the processes running on the specified computers.
The default is the local computer.

Type the NetBIOS name, an IP address, or a fully qualified domain name of one or more computers.
To specify the local computer, type the computer name, a dot (.), or "localhost".

This parameter does not rely on Windows PowerShell remoting.
You can use the ComputerName parameter of Get-Process even if your computer is not configured to run remote commands.

```yaml
Type: String[]
Parameter Sets: (All)
Aliases: Cn

Required: False
Position: Named
Default value: Local computer
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -FileVersionInfo
Gets the file version information for the program that runs in the process.

On Windows Vista and later versions of Windows, you must open Windows PowerShell with the "Run as administrator" option to use this parameter on processes that you do not own.

You cannot use the **FileVersionInfo** and **ComputerName** parameters of the **Get-Process** cmdlet in the same command.
To get file version information for a process on a remote computer, use the **Invoke-Command** cmdlet.

Using this parameter is equivalent to getting the MainModule.FileVersionInfo property of each process object.
When you use this parameter, Get-Process returns a FileVersionInfo object (System.Diagnostics.FileVersionInfo), not a process object.
So, you cannot pipe the output of the command to a cmdlet that expects a process object, such as Stop-Process.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: FV, FVI

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### -Id
Specifies one or more processes by process ID (PID).
To specify multiple IDs, use commas to separate the IDs.
To find the PID of a process, type "get-process".

```yaml
Type: Int32[]
Parameter Sets: Id
Aliases: PID

Required: True
Position: Named
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -InputObject
Specifies one or more process objects.
Enter a variable that contains the objects, or type a command or expression that gets the objects.

```yaml
Type: Process[]
Parameter Sets: InputObject
Aliases: 

Required: True
Position: Named
Default value: None
Accept pipeline input: True (ByValue)
Accept wildcard characters: False
```

### -Module
Gets the modules that have been loaded by the processes.

On Windows Vista and later versions of Windows, you must open Windows PowerShell with the "Run as administrator" option to use this parameter on processes that you do not own.

You cannot use the **Module** and **ComputerName** parameters of the **Get-Process** cmdlet in the same command.
To get the modules that have been loaded by a process on a remote computer, use the **Invoke-Command** cmdlet.

This parameter is equivalent to getting the Modules property of each process object.
When you use this parameter, Get-Process returns a ProcessModule object (System.Diagnostics.ProcessModule), not a process object.
So, you cannot pipe the output of the command to a cmdlet that expects a process object, such as Stop-Process.

When you use both the Module and FileVersionInfo parameters in the same command, Get-Process returns a FileVersionInfo object with information about the file version of all modules.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: 

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### -Name
Specifies one or more processes by process name.
You can type multiple process names (separated by commas) and use wildcard characters.
The parameter name ("Name") is optional.

```yaml
Type: String[]
Parameter Sets: Name
Aliases: ProcessName

Required: False
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: True
```

### CommonParameters
This cmdlet supports the common parameters: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction, and -WarningVariable. For more information, see about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
## INPUTS

### System.Diagnostics.Process
You can pipe a process object to Get-Process.
## OUTPUTS

### System.Diagnostics.Process, System.Diagnotics.FileVersionInfo, System.Diagnostics.ProcessModule
By default, Get-Process returns a System.Diagnostics.Process object.
If you use the FileVersionInfo parameter, it returns a System.Diagnotics.FileVersionInfo object. 
If you use the Module parameter (without the FileVersionInfo parameter), it returns a System.Diagnostics.ProcessModule object.

## NOTES
* You can also refer to Get-Process by its built-in aliases, "ps" and "gps". For more information, see about_Aliases.
* On computers that are running a 64-bit version of Windows, the 64-bit version of Windows PowerShell gets only 64-bit process modules and the 32-bit version of Windows PowerShell gets only 32-bit process modules.
* You can use the properties and methods of the WMI Win32_Process object in Windows PowerShell. For information, see T:Microsoft.PowerShell.Commands.Get-WmiObject and the Windows Management Instrumentation (WMI) SDK.
* The default display of a process is a table that includes the following columns. For a description of all of the properties of process objects, see [Process Properties](https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.process#Properties) in the MSDN library.

  - Handles: The number of handles that the process has opened.

  - NPM(K): The amount of non-paged memory that the process is using, in kilobytes.

  - PM(K): The amount of pageable memory that the process is using, in kilobytes.

  - WS(K): The size of the working set of the process, in kilobytes.
The working set consists of the pages of memory that were recently referenced by the process.

  - VM(M): The amount of virtual memory that the process is using, in megabytes.
Virtual memory includes storage in the paging files on disk.

  - CPU(s): The amount of processor time that the process has used on all processors, in seconds.

  - ID: The process ID (PID) of the process.

  - ProcessName: The name of the process.

  For explanations of the concepts related to processes, see the Glossary in Help and Support Center and the Help for Task Manager.

* You can also use the built-in alternate views of the processes available with Format-Table, such as "StartTime" and "Priority", and you can design your own views. For more information, see T:Microsoft.PowerShell.Commands.Format-Table.
## RELATED LINKS

[Debug-Process](Debug-Process.md)

[Get-Process](Get-Process.md)

[Start-Process](Start-Process.md)

[Stop-Process](Stop-Process.md)

[Wait-Process](Wait-Process.md)

