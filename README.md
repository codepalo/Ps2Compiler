# **Ps2Vb**

Convert PowerShell to Compliable Code

## Overview

Provides an extendable path to converting most PowerShell code to a compilable format

## Getting Started

### Installation

```text
dotnet tool install ps2vb --global

dotnet tool update ps2vb --global

dotnet tool uninstall ps2vb --global
```
### Installation to local location

```text
dotnet tool install ps2vb --global c:\ps2vb

dotnet tool update ps2vb --global c:\ps2vb

dotnet tool uninstall ps2vb --global c:\ps2vb

setx PATH "%PATH%;C:\ps2vb"
```

### Installation from downloaded nuget to local location

```text
dotnet tool install ps2vb --add-source [download location] --tool-path c:\ps2vb

dotnet tool update ps2vb --add-source [download location] --tool-path c:\ps2vb

dotnet tool uninstall ps2vb --tool-path c:\ps2vb

setx PATH "%PATH%;C:\ps2vb"
```

### Installation from downloaded nuget to global location

```text
dotnet tool install ps2vb --add-source [download location] --global

dotnet tool update ps2vb --add-source [download location] --global

dotnet tool uninstall ps2vb --global
```

### Installation from downloaded msi

```text
ps2vb.msi - defaults to \ps2vb

setx PATH "%PATH%;C:\ps2vb"
```

### Requirements

- Visual Studio (VS2022 Community Edition is free)
- Dotnet Core
- PowerShell 7
- 7Zip (if you use PowerShell Compress-Archive)

## Command Line

```text
  ps2vb [psfilename]
```

  *[psfilename]Ps2Vb.Config.ps1* - conversion configuration file is automatically created during the first run. It should be reviewed and changed if required.

## Options

The following can be added to the same folder as the code. The first two are converted if found. The last three are copied to the converted folder at the end of the conversion process.

```
  [FunctionFileName]Function.psm1   Contains separate functions that are referenced by Import-Module in main file
  [PropertyFileName]Property.ps1    Contains variable definitions that are included by Import-Module in main file

  [anyExtraFile].vb                 add on vb code
  [anyProjectFile].vbproj           custom project file
  [psfilename]Setup                 custom project setup folder
  [My Project]                      folder containing custom settings
```

## Usage Notes

- Copy your current PowerShell code into a separate folder while running the converter to prevent code modifications from impacting you original PowerShell code.

- The converted VB project will be in a subfolder, **ps2vb**, of your PowerShell code folder.

- If after converting you find variables that did not get defined correctly or other issues here are some suggestions

- Define all variables with a type - [String], [Bolean], [Int], [Regex] [DateTime]

- Define all arrays with [array type[]] prior to using - [String[]]

- Define all global variables using Set-Variable statement

```text
    Set-Variable -Name LASTEXITCODE -Value 0 -Scope Global
    [Int]$LASTEXITCODE = 0
```

- Put all variables followed by a wild card into a string

```text
    "$($something)*" or $something+"*"
```

- Put all quoted string variables in parenthesis

```text
    "$($thisvariable)""
```

- Put all quoted string variables with qualifiers in a line that joins them using the "+"

```text
    from:   [String]$vbFunctionList = "$($ps2vbFolder)vb$($psMainFileName.Substring(0, $psMainFileName.LastIndexOf('.')))FunctionFileList$($fullorpartgroup).txt"

    to:     [String]$vbFunctionList = $ps2vbFolder + "vb" + $psMainFileName.Substring(0, $psMainFileName.LastIndexOf('.')) + "FunctionFileList" + $fullorpartgroup + ".txt"
```

- Put all return statements on a separate line

```text
    If ($psApplication -eq "none") {
      return
    }
```

- Convert pipeline lines to loops except most Get-Child pipelines which will be converted into loops automatically (VB does not have pipelines)

- Use a variable instead of string with 'Invoke-Expression' statements

```text
    $LASTEXITCODE = Invoke-Expression $cmd
```

- Avoid using single line Default statements at the end if Switch statements

```text
    Default {
      $nline = $nline
      return $nline
      }
```

- Fix variable that used $array[$c + 1] to a temp variable

```text
    $tempc = $c + 1
    $array [$tempc]
```

Some of the above recommendations may not be necessary in future releases but most are required to collect variables and define then correctly.

This converter does not cover ALL possible uses of PowerShell and it's diverse world of wonderful modules. It is not yet ready for most PowerShell scripts used by system administrators and the vast array of AD and System commands.

#

## Extending conversion with InLine VB Code

- VB code can be inserted into the converted VB files at conversion time by adding InLine VBcode in the **[psfilename]** PowerShell file. It will be ignored when using PowerShell since it begins with #

- Add lines with using '#vb some code'

```text
    #vb ReDim $returnList(-1)
```

- Import-Module statements in the **[psfilename]** file will be converted by reading the defined module that is in the same folder as the PowerShell code

- The converted project can be extended by writing custom code in VB and placing the .VB file in the same folder as the **[psfilename]**.ps1 file. Any file that ends with .vb will be copied to the VB project folder.

- The conversion creates a VB project file, **[yourproject].vbproj**, unless one exist in the same folder as the PowerShell file. The new project file can be modified and copied to the PowerShell folder for inclusion during the next conversion. The same holds true for the solution file.

- If you create a setup project after the conversion it can be included in future conversion by placing the setup project folder in the same folder as the PowerShell code.

- If you create command line debugging inside of the VB project it can be included in future conversion by copying the **My Project** folder to the same folder as the PowerShell file.

#

## PowerShell Commands

The following PowerShell command have been written in VB with additional commands scheduled for future releases

```text
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
```

### [Have a Pleasant Coding Day]

Copyright (C) 2023. All Rights Reserved PaloByte
