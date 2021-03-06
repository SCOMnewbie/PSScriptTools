# ![toolbox](./images/toolbox-thumbnail.png) PSScriptTools

This PowerShell module contains a number of functions you might use to enhance your own functions and scripts. The [Samples](./samples) folder contains demonstration script files.

## Current Release

You can get the current release from this repository or install this from the PowerShell Gallery:

```powershell
Install-Module PSScriptTools
```

or in PowerShell Core:

```powershell
Install-Module PSScriptTools -scope currentuser
```

Please post any questions, problems or feedback in Issues. Any input is greatly appreciated.

## [Add-Border](docs/Add-Border.md)

This command will create a character or text based border around a line of text. You might use this to create a formatted text report or to improve the display of information to the screen.

```powershell
PS C:\> add-border $env:computername

*************
*   COWPC   *
*************
```

## [Get-PSWho](docs/Get-PSWho.md)

This command will provide a summary of relevant information for the current user in a PowerShell Session. You might use this to troubleshoot an end-user problem running a script or command.

```powershell
PS C:\> Get-PSWho

User            : BOVINE320\Jeff
Elevated        : True
Computername    : BOVINE320
OperatingSystem : Microsoft Windows 10 Pro [64-bit]
OSVersion       : 10.0.17763
PSVersion       : 5.1.17763.134
Edition         : Desktop
PSHost          : ConsoleHost
WSMan           : 3.0
ExecutionPolicy : RemoteSigned
Culture         : en-US
```

## [New-CustomFileName](docs/New-CustomFileName.md)

This command will generate a custom file name based on a template string that you provide.

```powershell
PS C:\> New-CustomFileName %computername_%day%monthname%yr-%time.log
COWPC_28Nov19-142138.log

PS C:\> New-CustomFileName %dayofweek-%####.dat
Tuesday-3128.dat
```

You can create a template string using any of these variables. Most of these should be self-explanatory

- %username
- %computername
- %year  - 4 digit year
- %yr  - 2 digit year
- %monthname - The abbreviated month name
- %month  - The month number
- %dayofweek - The full name of the week day
- %day
- %hour
- %minute
- %time
- %string - A random string
- %guid

You can also insert a random number using %### with a # character for each digit. If you want a 2 digit random number use %##. If you want 6 digits, use %######.

## [New-RandomFileName](docs/New-RandomFileName.md)

Create a new random file name. The default is a completely random name including the extension.

```powershell
PS C:\> new-randomfilename
fykxecvh.ipw
```

But you can specify an extension.

```powershell
PS C:\> new-randomfilename -extension dat
emevgq3r.dat
```

Optionally you can create a random file name using the TEMP folder or your HOME folder. On Windows platforms this will default to your Documents folder.

```powershell
PS C:\> new-randomfilename -extension log -UseHomeFolder
C:\Users\Jeff\Documents\kbyw4fda.log
```

On Linux machines it will be the home folder.

```powershell
PS /mnt/c/scripts> new-randomfilename -home -Extension tmp
/home/jhicks/oces0epq.tmp
```

## [Write-Detail](docs/Write-Detail.md)

This command is designed to be used within your functions and scripts to make it easier to write a detailed message that you can use as verbose output. The assumption is that you are using an advanced function with a Begin, Process and End scriptblocks. You can create a detailed message to indicate what part of the code is being executed. The output can be configured to include a datetime stamp or just the time.

```powershell
PS C:\> write-detail "Getting file information" -Prefix Process -Date
9/15/2018 11:42:43 [PROCESS] Getting file information
```

In a script you might use it like this:

```powershell
Begin {
    Write-Detail "Starting $($myinvocation.mycommand)" -Prefix begin -time | Write-Verbose
    $tabs = "`t" * $tab
    Write-Detail "Using a tab of $tab" -Prefix BEGIN -time | Write-Verbose
} #begin
```

## [Out-VerboseTee](docs/Out-VerboseTee.md)

This command is intended to let you see your verbose output and write the verbose messages to a log file. It will only work if the verbose pipeline is enabled, usually when your command is run with -Verbose. This function is designed to be used within your scripts and functions. You either have to hard code a file name or find some other way to define it in your function or control script. You could pass a value as a parameter or set it as a PSDefaultParameterValue.

This command has an alias of `Tee-Verbose`.

```powershell
Begin {
    $log = New-RandomFilename -useTemp -extension log
    Write-Detail "Starting $($myinvocation.mycommand)" -Prefix begin | Tee-Verbose $log
    Write-Detail "Logging verbose output to $log" -prefix begin | Tee-Verbose -append
    Write-Detail "Initializing data array" -Prefix begin | Tee-Verbose $log -append
    $data = @()
} #begin
```

When the command is run with -Verbose you will see the verbose output **and** it will be saved to the specified log file.

## [Out-ConditionalColor](docs/Out-ConditionalColor.md)

This command is designed to take pipeline input and display it in a colorized format,based on a set of conditions. Unlike `Write-Host` which doesn't write to the pipeline, this command will write to the pipeline.

You can use a simple hashtable to define a color if the given property matches the hashtable key.

![](./images/occ-1.png)

Or you can specify an ordered hashtable for more complex processing.
![](./images/occ-2.png)

This command doesn't always work depending on the type of object you pipe to it. The problem appears to be related to the formatting system. Development and testing is ongoing.

## [Copy-Command](docs/Copy-Command.md)

This command will copy a PowerShell command, including parameters and help to a new user-specified command. You can use this to create a "wrapper" function or to easily create a proxy function. The default behavior is to create a copy of the command complete with the original comment-based help block.

## Format-Functions

A set of simple commands to make it easier to format values.

```powershell
PS C:\> format-percent -Value 123.5646MB -total 1GB -Decimal 4
12.0669
PS C:\> format-string "powershell" -Reverse -Case Proper
Llehsrewop
PS C:\>  format-value 1235465676 -Unit kb
1206509
```

## [Get-PSLocation](docs/Get-PSLocation.md)

A simple function to get common locations. This can be useful with cross-platform scripting.

![](./images/pslocation-win.png)

![](./images/pslocation-linux.png)

## [Get-PowerShellEngine](docs/Get-PowerShellEngine.md)

Use this command to quickly get the path to the PowerShell executable. In Windows you should get a result like this:

```powershell
PS C:\> Get-PowerShellEngine
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

But PowerShell Core is a bit different:

```powershell
PS /home/jhicks> Get-PowerShellEngine
/opt/microsoft/powershell/6/pwsh
```

You can also get detailed information.

![Windows PowerShell](./images/get-powershellengine1.png)

![PowerShell Core on Windows](./images/get-powershellengine2.png)

![PowerShell Core on Linux](./images/get-powershellengine3.png)

Results will vary depending on whether you are running Windows PowerShell or PowerShell Core.

## [Out-More](docs/Out-More.md)

This command provides a PowerShell alternative to the cmd.exe **MORE** command, which doesn't work in the PowerShell ISE. When you have screens of information, you can page it with this function.

```powershell
get-service | out-more
```

![](./images/out-more.png)

This also works in PowerShell Core.

## [Invoke-InputBox](docs/Invoke-InputBox.md)

This function is a graphical replacement for `Read-Host`. It creates a simple WPF form that you can use to get user input. The value of the text box will be written to the pipeline.

```powershell
$name = Invoke-InputBox -Prompt "Enter a user name" -Title "New User Setup"
```

![](./images/ibx-1.png)

You can also capture a secure string.

```powershell
Invoke-Inputbox -Prompt "Enter a password for $Name" -AsSecureString -BackgroundColor red
```

![](./images/ibx-2.png)

This example also demonstrates that you can change form's background color. This function will **not** work in PowerShell Core.

## ToDo

Because this module is intended to make scripting easier for you, it adds options to insert ToDo statements into PowerShell files. If you are using the PowerShell ISE or VS Code and import this module, it will add the capability to insert a line like this:

```yaml
# [12/13/2018 16:52:40] TODO: Add parameters
```

In the PowerShell ISE, you will get a new menu under Add-Ons

![](./images/todo-1.png)

You can use the menu or keyboard shortcut which will launch an input box.

![](./images/todo-2.png)

The comment will be inserted at the current cursor location.

In VS Code, access the command palette (Ctrl+Shift+P) and then "PowerShell: Show Additional Commands from PowerShell Modules". Select "Insert ToDo" from the list and you'll get the same input box. Note that this will only work for PowerShell files.

## [Test-Expression](docs/Test-Expression.md)

The primary command can be used to test a PowerShell expression or scriptblock for a specified number of times and calculate the average runtime, in milliseconds, over all the tests.

### Why?

When you run a single test with `Measure-Command` the result might be affected by any number of factors. Likewise, running multiple tests may also be influenced by things such as caching. The goal in this module is to provide a test framework where you can run a test repeatedly with either a static or random interval between each test. The results are aggregated and analyzed. Hopefully, this will provide a more meaningful or realistic result.

### Examples

The output will also show the median and trimmed values as well as some metadata about the current PowerShell session.

```powershell
PS C:\> $cred = Get-credential globomantics\administrator
PS C:\> Test-Expression {param($cred) get-wmiobject win32_logicaldisk -computer chi-dc01 -credential $cred } -argumentList $cred

Tests        : 1
TestInterval : 0.5
AverageMS    : 1990.6779
MinimumMS    : 1990.6779
MaximumMS    : 1990.6779
MedianMS     : 1990.6779
TrimmedMS    :
PSVersion    :5.1.17763.134
OS           : Microsoft Windows 10 Pro
```

You can also run multiple tests with random time intervals.

```powershell
PS C:\>Test-expression {param([string[]]$Names) get-service $names} -count 5 -IncludeExpression -argumentlist @('bits','wuauserv','winrm') -RandomMinimum .5 -RandomMaximum 5.5

Tests        : 5
TestInterval : Random
AverageMS    : 1.91406
MinimumMS    : 0.4657
MaximumMS    : 7.5746
MedianMS     : 0.4806
TrimmedMS    : 0.51
PSVersion    : 5.1.17763.134
OS           : Microsoft Windows 10 Pro
Expression   : param([string[]]$Names) get-service $names
Arguments    : {bits, wuauserv, winrm}
```

For very long running tests, you can run them as a background job.

### Graphical Testing

The module also includes a graphical command called `Test-ExpressionForm`. This is intended to serve as both an entry and results form.

![Test Expression](images/testexpressionform.png)

When you quit the form the last result will be written to the pipeline including all metadata, the scriptblock and any arguments.

## [Find-CimClass](docs/Find-CimClass.md)

This function is designed to search an entire CIM repository for a class name. Sometimes, you may have a guess about a class name but not know the full name or even the correct namespace. `Find-CimClass` will recursively search for a given classname. You can use wildcards and search remote computers.

![find-cimclass](images/find-cimclass.png)

## [ConvertTo-Markdown](docs/ConvertTo-Markdown.md)

This command is designed to accept pipelined output and create a markdown document. The pipeline output will formatted as a text block. You can optionally define a title, content to appear before the output and content to appear after the output. You can run a command like this:

```powershell
 Get-Service Bits,Winrm | Convertto-Markdown -title "Service Check" -precontent "## $($env:computername)"  -postcontent "_report $(Get-Date)_"
 ```

which generates this markdown:

    # Service Check

    ## BOVINE320

    ```text

    Status   Name               DisplayName
    ------   ----               -----------
    Running  Bits               Background Intelligent Transfer Ser...
    Running  Winrm              Windows Remote Management (WS-Manag...
    ```

    _report 09/25/2018 09:57:12_

Because the function writes markdown to the pipeline you will need to pipe it to a command `Out-File` to create a file.

## [ConvertTo-WPFGrid](docs/ConvertTo-WPFGrid.md)

This command is an alternative to `Out-Gridview`. It works much the same way. Run a PowerShell command and pipe it to this command. The output will be displayed in an autosized data grid. You can click on column headings to sort. You can resize columns and you can re-order columns.

```powershell
get-eventlog -list -ComputerName DOM1,SRV1,SRV2 |
Select Machinename,Log,MaximumKilobytes,Overflowaction,
@{Name="RetentionDays";Expression={$_.MinimumRetentionDays}},
@{Name="Entries";Expression = {$_.entries.count}} |
ConvertTo-WPFGrid -Title "Event Log Report"
```

![Displaying Eventlog Info](images/wpfgrid.png)

You can also have automatically refresh the data.
```powershell
get-process | sort-object WS -Descending | Select -first 20 ID,Name,WS,VM,PM,Handles,StartTime |
Convertto-WPFGrid -Refresh -timeout 20 -Title "Top Processes"
```

![Displaying Top Processes](images/wpfgrid2.png)

## [Convert-CommandtoHashtable](docs/Convert-CommandtoHashtable.md)

This command is intended to convert a long PowerShell expression with named parameters into a splatting
alternative.

```powershell
PS C:\> Convert-CommandtoHashtable -Text "get-eventlog -listlog -computername a,b,c,d -erroraction stop"

$paramHash = @{
  listlog = $True
   computername = "a","b","c","d"
   erroraction = "stop"
}

Get-EventLog @paramHash
```

## [Convert-HashtableString](docs/Convert-HashtableString.md)

This function is similar to `Import-PowerShellDataFile`. But where that command can only process a file, this command
will take any hashtable-formatted string and convert it into an actual hashtable.

```powershell
PS C:\> get-content c:\work\test.psd1 | unprotect-cmsmessage | Convert-HashtableString

Name                           Value
----                           -----
CreatedBy                      BOVINE320\Jeff
CreatedAt                      10/02/2018 21:28:47 UTC
Computername                   Think51
Error
Completed                      True
Date                           10/02/2018 21:29:35 UTC
Scriptblock                    restart-service spooler -force
CreatedOn                      BOVINE320
```

The test.psd1 file is protected as a CMS Message. In this example, the contents are decoded as a string which is then in turn converted into an actual hashtable.

## [Convert-HashTableToCode](docs/Convert-HashTableToCode.md)

Use this command to convert a hashtable into its text or string equivalent.

```powershell
PS C:\> $h = @{Name="SRV1";Asset=123454;Location="Omaha"}
PS C:\> convert-hashtabletocode $h
@{
        Name = 'SRV1'
        Asset = 123454
        Location = 'Omaha'
}
```

Convert a hashtable object to a string equivalent that you can copy into your script.

## [ConvertTo-HashTable](docs/ConvertTo-HashTable.md)

This command will take an object and create a hashtable based on its properties. You can have the hashtable exclude some properties as well as properties that have no value.

```powershell
PS C:\> get-process -id $pid | select name,id,handles,workingset | ConvertTo-HashTable

Name                           Value
----                           -----
WorkingSet                     418377728
Name                           powershell_ise
Id                             3456
Handles                        958
```

## [Join-Hashtable](docs/Join-Hashtable.md)

This command will combine two hashtables into a single hashtable.Join-Hashtable will test for duplicate keys. If any of the keys from the first, or primary hashtable are found in the secondary hashtable, you will be prompted for which to keep. Or you can use -Force which will always keep the conflicting key from the first hashtable.

```powershell
PS C:\> $a=@{Name="Jeff";Count=3;Color="Green"}
PS C:\> $b=@{Computer="HAL";Enabled=$True;Year=2020;Color="Red"}
PS C:\> join-hashtable $a $b
Duplicate key Color
A Green
B Red
Which key do you want to KEEP \[AB\]?: A

Name                           Value
----                           -----
Year                           2020
Name                           Jeff
Enabled                        True
Color                          Green
Computer                       HAL
Count                          3
```

## Select Functions

The module contains 2 functions which simplify the use of `Select-Object`. The commands are intended to make it easier to select the first or last X number of objects. The commands include features so that you can sort the incoming objects on a given property first.

```powershell
PS C:\> get-process | select-first 5 -Property WS -Descending

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
    696      89   615944     426852     391.97   7352   0 sqlservr
    541      78   262532     274576     278.41   6208   8 Code
   1015      70   227824     269504     137.39  16484   8 powershell_ise
   1578     111   204852     254640      98.58  21332   8 firefox
    884      44   221872     245712     249.23  12456   8 googledrivesync
```

## [New-PSDriveHere](docs/New-PSDriveHere.md)

This function will create a new PSDrive at the specified location. The default is the current location, but you
can specify any PSPath. The function will take the last word of the path and use it as the name of the new
PSDrive.

```powershell
PS C:\users\jeff\documents\Enterprise Mgmt Webinar> new-psdrivehere

Name           Used (GB)     Free (GB) Provider      Root                                 CurrentLocation
----           ---------     --------- --------      ----                                 ---------------
Webinar                         146.57 FileSystem    C:\users\jeff\Documents\Enter...
```

## [Get-MyVariable](docs/Get-MyVariable.md)

This function will return all variables not defined by PowerShell or by this function itself. The default is to
return all user-created variables from the global scope but you can also specify a scope such as script, local or
a number 0 through 5.

```powershell
PS C:\> Get-MyVariable

NName Value                  Type
---- -----                  ----
a    bits                   ServiceController
dt   10/22/2018 10:49:38 AM DateTime
foo  123                    Int32
r    {1, 2, 3, 4...}        Object[]
...
```

Depending on the value and how PowerShell chooses to display it, you may not see the type.

## [Optimize-Text](docs/Optimize-Text.md)

Use this command to clean and optimize content from text files. Sometimes text files have blank lines or the content has trailing spaces. These sorts of issues can cause problems when passing the content to other commands.

This command will strip out any lines that are blank or have nothing by white space, and trim leading and trailing spaces. The optimized text is then written back to the pipeline. Optionally, you can specify a property name. This can be useful when your text file is a list of computer names and you want to take advantage of pipeline binding.

## [Show-Tree](docs/Show-Tree.md)

Shows the specified path as a graphical tree in the console. This is intended as PowerShell alternative to the tree DOS command. This function should work for any type of PowerShell provider and can be used to explore providers used for configuration like the WSMan provider or the registry.

By default, the output will only show directory or equivalent structures. But you can opt to include items well as item details.

![show file system tree](images/show-tree1.png)

## [New-WPFMessageBox](docs/New-WPFMessageBox.md)

This function creates a Windows Presentation Foundation (WPF) based message box. This is intended to replace the legacy MsgBox function from VBScript and the Windows Forms library. The command uses a set of predefined button sets, each of which will close the form and write a value to the pipeline.

    OK     = 1

    Cancel = 0

    Yes    = $True

    No     = $False

You can also create an ordered hashtable of your own buttons and values. It is assumed you will typically use this function in a script where you can capture the output and take some action based on the value.

```powershell
PS C:\> New-WPFMessageBox -Message "Are you sure you want to do this?" -Title Confirm -Icon Question -ButtonSet YesNo
```

![A YesNo WPF Message box](/images/wpfbox-1.png)

You can also create your own custom button set as well as modify the background color.

```powershell
PS C:\> New-WPFMessageBox -Message "Select a system option from these choices:" -Title "You Decide" -Background cornsilk -Icon Warning -CustomButtonSet ([ordered]@{"Reboot"=1;"Shutdown"=2;"Cancel"=3})
```

![A customized WPF Message box](/images/wpfbox-2.png)

## [Compare-Module](docs/Compare-Module.md)

Use this command to compare module versions between what is installed against an online repository like the PSGallery

```powershell
PS C:\> Compare-Module | Where UpdateNeeded | Out-Gridview -title "Select modules to update" -outputMode multiple | Foreach { Update-Module $_.name }
```

Compare modules and send results to Out-Gridview. Use Out-Gridview as an object picker to decide what modules to update.

## [Get-WindowsVersion](docs/Get-WindowsVersion.md)

This is a PowerShell version of the `winver.exe` utility. This command uses PowerShell remoting to query the registry on a remote machine to retrieve Windows version information.

```powershell
PS C:\> get-windowsversion -Computername srv1,srv2,win10 -Credential company\artd | format-table

ProductName                             EditionID          ReleaseId Build      InstalledUTC          Computername
-----------                             ---------          --------- -----      ------------          ------------
Windows Server 2016 Standard Evaluation ServerStandardEval 1607      14393.2273 12/26/2018 4:07:25 PM SRV1
Windows Server 2016 Standard Evaluation ServerStandardEval 1607      14393.2273 12/26/2018 4:08:07 PM SRV2
Windows 10 Enterprise Evaluation        EnterpriseEval     1703      15063.1387 12/26/2018 4:08:11 PM WIN10
```

### [Get-WindowsVersionString](docs/Get-WindowsVersionString.md)

This command is a variation of `Get-WindowsVersion` that returns a formatted string with version information.

```powershell
PS C:\> Get-WindowsVersionString
Windows 10 Pro Version 1809 (OS Build 17763.195)
```

## [New-PSFormatXML](docs/New-PSFormatXML.md)

When defining custom objects with a new typename, PowerShell by default will display all properties. However, you may wish to have a specific default view, be it a table or list. Or you may want to have different views display the object differently. Format directives are stored in format.ps1xml files which can be tedious to create. This command simplifies that process.

Define a custom object:

```powershell
$tname = "myThing"
$obj = [PSCustomObject]@{
    PSTypeName   = $tname
    Name         = "Jeff"
    Date         = (Get-Date)
    Computername = $env:computername
    OS           = (get-ciminstance win32_operatingsystem -Property Caption).caption
}
Update-TypeData -TypeName $tname -MemberType "ScriptProperty" -MemberName "Runtime" -value {(Get-Date) - [datetime]"1/1/2019"} -force
```

That looks like this by default:

```powershell
PS C:\> $obj

Name         : Jeff
Date         : 2/10/2019 8:49:10 PM
Computername : BOVINE320
OS           : Microsoft Windows 10 Pro
Runtime      : 40.20:49:43.9205882
```

Now you can create new formatting directives.

```powershell
PS C:\> $obj | New-PSFormatXML -Properties Name, Date, Computername, OS -FormatType Table -path "C:\work\$tname.format.ps1xml"
PS C:\> $obj | New-PSFormatXML -Properties Name, OS, Runtime -FormatType Table -view runtime -path "C:\work\$tname.format.ps1xml" -append
PS C:\> $obj | New-PSFormatXML -FormatType List -path "C:\work\$tname.format.ps1xml" -append
PS C:\> Update-FormatData -appendpath "C:\work\$tname.format.ps1xml"
```

And here is what the object looks like now:

```powershell
PS C:\> $obj

Name Date                 Computername Operating System
---- ----                 ------------ ----------------
Jeff 2/10/2019 8:49:10 PM BOVINE320    Microsoft Windows 10 Pro

PS C:\> $obj | format-table -View runtime

Name OS Runtime
---- -- -------
Jeff    40.20:56:24.5411481

PS C:\> $obj | format-list


Name            : Jeff
Date            : Sunday, February 10, 2019
Computername    : BOVINE320
OperatingSystem : Microsoft Windows 10 Pro
Runtime         : 40.21:12:01
```

## [Remove-Runspace](docs/Remove-Runspace.md)

During the course of your PowerShell work, you may discover that some commands and scripts can leave behind runspaces. You may even deliberately be creating additional runspaces. These runspaces will remain until you exit your PowerShell session. Or use this command to cleanly close and dispose of runspaces.

```powershell
PS C:\> get-runspace | where ID -gt 1 | Remove-Runspace
```

Get all runspaces with an ID greater than 1, which is typically your session, and remove the runspace.

## Compatibility

Where possible these commands have been tested with PowerShell Core, but not every platform. If you encounter problems, have suggestions or other feedback, please post an issue.

*last updated 12 February 2019*
