# Basic PowerShell tricks and notes

This page is designed for beginners and newcomers to PowerShell who want to quickly learn the essential basics, the most frequently used syntaxes, elements and and tricks. It should help you jump start your journey as a PowerShell user.

The main source for learning PowerShell is Microsoft Learn websites. There are extensive and complete guides about each command/cmdlet with examples.

[PowerShell core at Microsoft Learn](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/?view=powershell-7.4)

**Also Use Bing Chat for your PowerShell questions. The AI is fantastic at creating code and explaning everything.**

<br>

## Pipeline variable

`$_`  is the variable for the current value in the pipeline.

[Examples](https://stackoverflow.com/questions/3494115/what-does-mean-in-powershell)

<br>

## Filtering with Where-Object

`?` which is an alias for `Where-Object`, is used to filter all the data given to it.

[Where-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object)

Example

```powershell
Get-PSDrive | ?{$_.free -gt 1} 
```

<br>

## Show the properties of an object selectively

`Select` or `Select-Object` show the properties that we want to see from an object

If we use `*` then all of the properties will be shown and from there we can choose which properties to add.

Example:

```powershell
Get-PSDrive | ?{$_.free -gt 1} | select * 

Get-PSDrive | ?{$_.free -gt 1} | select root, used, free
```

<br>

## Looping using ForEach-Object

`ForEach-Object { }`

The `ForEach-Object` cmdlet performs an operation on each item in a collection of input objects. The input objects can be piped to the cmdlet or specified using the InputObject parameter.

i.e. For every item in the pipe, run this line.

Examples:

```powershell
Get-PSDrive | ?{$_.free -gt 1} | select root, used, free | ForEach-Object{"zebra"} 
```

```powershell
Get-PSDrive | ?{$_.free -gt 1} | select root, used, free | ForEach-Object{ Write-Host "Free Space for " $_.Root "is" ($_.free/1gb )} 
```

The parenthesis, `($_.free/1gb )` must be there if we want to modify one of the output strings.

<br>

## To get online help about any Cmdlet

Opens the webpage for the specified command

`Get-help cmdlet –online`

Example:

`Get-Help ForEach-Object –online`

`Get-Help dir –online`

Shows the full help on the PowerShell console

`Get-help get-service -full`

Opens a new window showing the full help content and offers other options such as Find

`Get-help get-service -ShowWindow`

<br>

## To Query Windows services

This gets any Windows service that has the word "Xbox" in it.

`Get-service "*xbox*"`

This gets any Windows service that has the word "x" in it.

`Get-service "*x*"`

Putting `*` around the word or letter finds anything that contains it.

`Get-service "*x*" | sort-object status`

Example syntax:

```powershell
Get-Service [[-Name] <System.String[]>] [-ComputerName <System.String[]>] [-DependentServices] [-Exclude <System.String[]>] [-Include <System.String[]>] [-RequiredServices] [<CommonParameters>] 
```

In this part

```powershell
Get-Service [[-Name] <System.String[]>] 
```

The `-Name` Parameter accepts `<System.String[]>`, which is a StringList, and when [] is included, that means there can be multiple inputs/strings, separated by comma `,`.

So `[[-Name] <System.String[]>]` can be used like this:

```powershell
Get-Service -Name WinRM,BITS,*Xbox*
```

Also in another similar example syntax:

```powershell
Get-Service [-ComputerName <System.String[]>] [-DependentServices] -DisplayName <System.String[]> [-Exclude <System.String[]>] [-Include <System.String[]>] [-RequiredServices] [<CommonParameters>]
```

Everything is inside a bracket except for -DisplayName, that means it is mandatory. **If a parameter is inside a bracket, that means it is optional.**

<br>

## How to suppress errors in PowerShell

```powershell
-ErrorAction SilentlyContinue 
```

[Everything you wanted to know about exceptions](https://learn.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-exceptions)

Try/Catch will only 'trigger' on a terminating exception. Most cmdlets in PowerShell, by default, won't throw terminating exceptions. You can set the error action with the -ErrorAction or -ea parameters:

```powershell
Do-Thing 'Stuff' -ErrorAction Stop 
```

Careful when using `-ErrorAction Stop`, If using it in loops like with `ForEach-Object`, then it will stop the entire loop after the first encounter of error.

[Handling Errors the PowerShell Way](https://devblogs.microsoft.com/scripting/handling-errors-the-powershell-way/)

Tip: If you set

```powershell
$ErrorActionPreference = 'Stop'
```

In your PowerShell code, either locally or globally for the entire script, `Write-Error` will cause the script to stop because it will be like throwing an error.

<br>

## Get file signature of all the files in a folder

This will check all of the files in the current directory and show an error for folders, you can add `-ErrorAction SilentlyContinue` to the `Get-AuthenticodeSignature` cmdlet to ignore the errors.

```powershell
Get-ChildItem | ForEach-Object -Parallel {Get-AuthenticodeSignature $_.Name}
```

This will recursively check only the files in the current directory and sub-directories, no folder is piped.

```powershell
Get-ChildItem -Recurse -File | ForEach-Object -Parallel {Get-AuthenticodeSignature $_.Name}
```

<br>

## Write output to a file or string

```powershell
> output.txt 
```

Example:

```powershell
ipconfig /all > mynetworksettings.txt 
```

[about_Redirection](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_redirection)

<br>

## How to add delay/pause to the execution of Powershell script

To sleep a PowerShell script for 5 seconds, you can run the following command

```powershell
Start-Sleep -Seconds 5
```

You can also use the `-milliseconds` parameter to specify how long the resource sleeps in milliseconds.

```powershell
Start-Sleep -Milliseconds 25
```

[Start-Sleep](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/start-sleep)

<br>

## How to stop/kill a a process or (.exe) executable in Powershell

Using native PowerShell cmdlet

```powershell
Stop-Process -Name "Photoshop"
```

[Stop-Process](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/stop-process)

Using `taskkill.exe`

```cmd
taskkill /IM "photoshop app.exe" /F 
```

[taskkill](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/taskkill)

<br>

## Automatically answer “yes” to a prompt in Powershell

Use `–force` at the end of the command

<br>

## Displays all information in the current access token

The command below displays all information in the current access token, including the current user name, security identifiers (SID), privileges, and groups that the current user belongs to.

```cmd
whoami /all
```

[whoami](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/whoami)

<br>

## Display all the TCP and UDP ports on which the computer is listening

```cmd
netstat -a 
```

[netstat](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/netstat)

<br>

## Copy the result of a command to clipboard automatically

Add `| clip` at the end the command

Example:

```powershell
Get-TimeZone | clip
```

Example:

```cmd
rg -i -F URL: | clip
```

<br>

## How to scan 2 text files for differences and pipe the difference to a third File

```powershell
$File1 = "C:\Scripts\Txt1.txt" 
$File2 = "C:\Scripts\Txt2.txt" 
$Location = "C:\Scripts\Txt3.txt" 

Compare-Object (get-content $File1) (get-content $File2) | format-list | Out-File $Location 
```

[Compare-Object](https://learn.microsoft.com/en-gb/powershell/module/Microsoft.PowerShell.Utility/Compare-Object)

<br>

## Difference between Strings and StringLists

This is Stringlist in PowerShell:

`[String[]]`

And this is a string  

`[String]`

When we define Stringlist in a parameter, then the argument will keep asking for multiple values instead of 1, if we want to stop adding arguments for the parameter, we have to enter twice.

<br>

## How to run a PowerShell (.ps1) script ?

* Method 1:

```powershell
&"Path\To\PS\Script.ps1"
```

Using the `&` [Call operator](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_operators#call-operator-)

* Method 2:

```powershell
Set-Location 'Path\To\Folder\OfThe\Script'
.\Script.ps1
```

* Method 3

```powershell
pwsh.exe -File 'Path\To\Folder\OfThe\Script.ps1'
```

*This example uses PowerShell Core*

<br>

## Enclosing strings that have a lot of single and double quotation marks

```powershell
$string =@" 

Some string text 

"@

$string
```

the markers `@"` and `"@` indicating the beginning and end of the string must be on separate lines.

<br>

## How to find the type of the output of a command in PowerShell?

Using `GetType()`

Examples:

```powershell
(Get-BitlockerVolume -MountPoint "C:").KeyProtector.keyprotectortype.GetType() 
```

```powershell
(get-nettCPConnection).GetType() 
```

<br>

## Make sure to use Pascal Case for variable names

Pascal Case requires variables made from compound words and have the first letter of each appended word written with an uppercase letter.

Example: `$Get-CurrentTime`

<br>

## Some popular resources and cmdlets

* [Out-Null](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/out-null)

* [Test-Path](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/test-path)

* [Add-Content](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/add-content)

* [New-Item](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-item)

* [Everything you wanted to know about arrays](https://learn.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-arrays)

* [about_Split](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_split)

* [Start-Process](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/start-process)

* [about_Parsing](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_parsing)

* [about_Quoting_Rules](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_quoting_rules)

* [about_PowerShell_exe](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_powershell_exe)

* [about_Comparison_Operators](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_comparison_operators)

* [Everything you wanted to know about hashtables](https://learn.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-hashtable)

* [about_Hash_Tables](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_hash_tables)

* [about_Operators](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_operators)

* [ForEach-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/foreach-object)

* [about_Foreach](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_foreach)

* [Set-Acl](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-acl)

* [Set-Content](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/set-content)

* [icacls](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/icacls)

* [Get-Process](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-process)

* [about_Environment_Variables](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_environment_variables)

* [Everything you wanted to know about the if statement](https://learn.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-if)

* [Tee-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/tee-object)

* [about_Signing](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_signing)

* [CIM Classes (WMI)](https://learn.microsoft.com/en-us/windows/win32/wmisdk/cimclas)

* [Get-CimInstance](https://learn.microsoft.com/en-us/powershell/module/cimcmdlets/get-ciminstance)

* [ConvertFrom-Json](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/convertfrom-json)

* [PowerShell scripting performance considerations](https://learn.microsoft.com/en-us/powershell/scripting/dev-cross-plat/performance/script-authoring-considerations)

* [Creating Get-WinEvent queries with FilterHashtable](https://learn.microsoft.com/en-us/powershell/scripting/samples/creating-get-winevent-queries-with-filterhashtable)

* [Checkpoint-Computer](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/checkpoint-computer)

  * [Restore Point Description Text](https://learn.microsoft.com/en-us/windows/win32/sr/restore-point-description-text)

* [Get-ComputerRestorePoint](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-computerrestorepoint)

* [Pop-Location](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/pop-location)

* [Invoke-Expression](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-expression)

* [about_Script_Blocks](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_script_blocks)

* [about_Functions_Advanced_Parameters](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions_advanced_parameters)

* [about_Functions_CmdletBindingAttribute](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions_cmdletbindingattribute)

* [Add-Computer](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/add-computer)

* [Get-Unique](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-unique)

* [Sort-Object](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/sort-object)

* [about_Comment_Based_Help](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_comment_based_help)

* [Get-Date](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-date)

* [about_Parameters_Default_Values](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_parameters_default_values)

* [about_Parameter_Sets](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_parameter_sets)

* [about_Automatic_Variables](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_automatic_variables)

* [about_Functions_Argument_Completion](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions_argument_completion)

* [Using tab-completion in the shell](https://learn.microsoft.com/en-us/powershell/scripting/learn/shell/tab-completion)

* [about_Continue](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_continue)

* [Trim Your Strings with PowerShell](https://devblogs.microsoft.com/scripting/trim-your-strings-with-powershell/)
