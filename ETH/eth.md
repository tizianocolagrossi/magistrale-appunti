# Eth Windows exposed 
windows system compromised, how to recover? guidelines.
**You shuld just reinstall it completely**  
If you want to clean it, cover four areas: Files, Registry keys, Processes and Network ports.
### Search for suspicius files
known dangerous filename like nc.exe, run antivirus, use tripwire or other tools that tools that identify
changes to system files. 
### Suspicius registry entries
Look for regisry keys that start known backdoors like:
 - HKEY_USERS\.DEFAULT\Software\ORL\WINVNC3
 - HKEY_LOCAL_MACHINE\SOFTWARE\Net Solutions\NetBus Server
**use reg delete to remove**
**Backdor favourite autostart (extensibility points (ASEPs))**
 - **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run**
### Suspicius Processes
Malicius porocess whit CPU utilization, end process or kill to stop. Check scheduler queue: shctasks, task sheduler.
### Suspicius Ports
Use **netstat -aon** to vew network connections.
### Windows security features
Windows firewall, automated updates
