# **Ps2Compiler**

Convert PowerShell to Compliable Code


## Overview

Provides an extendable path to compile most PowerShell code by converting code to a compliable format


## Getting Started

### Install Ps2Vb

      Download and run ps2vb.msi


### Requirements:

- Visual Studio (VS2022 Community Edition is free)
- Dotnet Core
- PowerShell 7
- 7Zip (if you use PowerShell Compress-Archive)


### Command Line
```
    ps2vb [psfilename]
```
- Run ps2vb.exe from the folder where **[psfilename]** is located
- **[psfilename]Ps2Vb.Config.ps1** contains conversion configuration settings and is automatically created during the first run. It should be reviewed and changed if required.


### Options

The following can be added to the same folder as the code. The first two are converted if found. The last three are copied to the converted folder at the end of the process.

```
  [FunctionFileName]Function.psm1 Contains separate functions that are referenced by Import-Module in main file
  [PropertyFileName]Property.ps1  Contains variable definitions that are included by Import-Module in main file

  [anyExtraFile].vb               add on vb code
  [anyProjectFile].vbproj         custom project file
  [psfilename]Setup               custom project setup folder
  [My Project]                    folder containing custom settings
```
#
## Beta Distribution

This BETA release provides for restricted use and is distributed as is without guarantee or liability. The program cannot be redistributed without written concent. Use at your own risk (which is low if you practice go SDLC processes)

Feed back, Questions, and suggestions are welcome

#
## Usage Notes

Copy your current PowerShell code into a separate folder while running the converter to prevent code modifications from impacting you original PowerShell code.

The converted VB project will be in a subfolder, **ps2vb**, of the PowerShell code folder.

If after converting you find variables that did not get defined correctly or other issues here are some suggestions

``````
  define all variables with a type - [String], [Bolean], [Int], [Regex] [DateTime]

  define all arrays with [array type[]] prior to using - [String[]]

  define all global variables using Set-Variable statement
    Set-Variable -Name LASTEXITCODE -Value 0 -Scope Global
    [Int]$LASTEXITCODE = 0

  put all variables followed by a wild card into a string
    "$($something)*" or $something+"*"

  put all quoted string variables in parenthesis
    "$($thisvariable)""

  put all quoted string variables with qualifiers in a line that joins them using the "+"
    from:   [String]$vbFunctionList = "$($ps2vbFolder)vb$($psMainFileName.Substring(0, $psMainFileName.LastIndexOf('.')))FunctionFileList$($fullorpartgroup).txt"
    to:     [String]$vbFunctionList = $ps2vbFolder + "vb" + $psMainFileName.Substring(0, $psMainFileName.LastIndexOf('.')) + "FunctionFileList" + $fullorpartgroup + ".txt"

  put all return statements on a separate line
    If ($psApplication -eq "none") {
      return
    }

  convert pipeline lines to loops except most Get-Child pipelines which will be converted into loops automatically (VB does not have pipelines)

  use a variable instead of string with 'Invoke-Expression' statements
    $LASTEXITCODE = Invoke-Expression $cmd

  avoid using single line Default statements at the end if Switch statements
     Default {
		  $nline = $nline
		  return $nline
		 }

  fix variable that used $array[$c + 1] to a temp variable
    $tempc = $c + 1
    $array [$tempc]

``````
Some of the above recommendations may not be necessary in future releases but most are required to collect variables and define then in VB.

This converter does not cover ALL possible uses of PowerShell and it's diverse world of wonderful modules. It is not yet ready for most PowerShell scripts used by system administrators and the vast array of AD and System commands.

#

## Extending conversion with InLine VB Code

  VB code can be inserted into the converted VB files at conversion time by adding InLine VBcode in the **[psfilename]** PowerShell file. It will be ignored when using PowerShell since it begins with #
  ```
  Add lines with using '#vb some code'

    #vb ReDim $returnList(-1)
```
Import-Module statements in the **[psfilename]** file will be converted by reading the defined module that is in the same folder as the PowerShell code

The converted project can be extended by writing custom code in VB and placing the .VB file in the same folder as the **[psfilename]**.ps1 file. Any file that ends with .vb will be copied to the VB project folder.

The conversion creates a VB project file, **[yourproject].vbproj**, unless one exist in the same folder as the PowerShell file. The new project file can be modified and copied to the PowerShell folder for inclusion during the next conversion.

If you create a setup project after the conversion it can be included in future conversion by placing the setup project folder in the same folder as the PowerShell code.

If you create command line debugging inside of the VB project it can be included in future conversion by copying the **My Project** folder to the same folder as the PowerShell file.
#
## PowerShell Commands
The following PowerShell command have been written in VB with additional commands scheduled for future releases
```
Add-Content
Compress-Archive
Copy-Item
Get-ChildItem
Get-Content
Get-Item
Invoke-Expression
Move-Item
New-Item
Remove-Item
Rename-Item
Set-Content
Set-Item
Split-Pat
Test-Path

custom vb replacements
Send-Mail
Start-Sleep
DriveInfo
SortAndRemoveStringDuplicates     replaces Sort-Object
SortAndRemoveIntegerDuplicates		replaces Sort-Object
Set-Location
Get-Location
DeleteDirectory
GetItemAttributes
SetItemAttributes
```
#
#
# Eating Our Own Dog Food

## **Ps2Vb.exe** is the result of converting **ps2vb.ps1** and **ps2vbFunctions.psm1** to VB using Ps2Vb.exe

#
### [Have a Pleasant Coding Day]
