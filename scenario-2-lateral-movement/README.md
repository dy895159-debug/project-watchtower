Markdown

# Scenario 2: Lateral Movement via PsExec

## Objective
Simulate an attacker using stolen credentials to achieve remote code execution on a target machine, then detect the activity using Sysmon and Windows Defender.

## Attack Steps

1. **Privilege Context** — After brute-forcing credentials in Scenario 1, the compromised account (`testuser`) was confirmed to have local administrator privileges.
net localgroup administrators

text


2. **Disable Remote UAC Restriction** — Applied a registry change to allow remote administrative actions for local accounts (a common attacker/pentester technique to bypass default Windows restrictions).
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f

text


3. **Lateral Movement Attempt** — Used Impacket's `psexec.py` from Kali Linux to remotely execute commands on the Windows target using the compromised credentials.
impacket-psexec 'testuser:Password123!'@10.0.2.3

text


This technique works by uploading an executable to the target's ADMIN$ share, then creating and starting a Windows service to execute commands remotely — a technique commonly used by both legitimate IT tools and attackers performing lateral movement.

## Detection

### Windows Defender ![Windows Defender quarantine](../screenshots/2soc.jpg)
Windows Defender automatically detected and quarantined the uploaded executable within seconds.

- **Threat Name:** `VirTool:Win32/RemoteExec!pz`
- **File Path:** `C:\Windows\xAhfviJv.exe`
- **Action:** Quarantined

### Sysmon Event Timeline
Sysmon captured the full attack timeline at the registry and file-system level:

| Time | Event | Detail |
|------|-------|--------|
| 10:36:28.876 PM | File Create (EventCode 11) | `C:\Windows\xAhfviJv.exe` written to disk by SYSTEM |
| 10:36:29.207 PM | Registry Event (EventCode 13) | Service `HbBU` registered in `HKLM\System\CurrentControlSet\Services\HbBU\ImagePath`, pointing to the malicious executable |
| 10:36:56.090 PM | File Access (EventCode 11) | Windows Defender engine (`MsMpEng.exe`) accessed and quarantined the file |

**Total detection time: ~28 seconds** from payload drop to quarantine.

## Results

- Confirmed that the lateral movement attempt was successfully blocked by native Windows Defender protections
- Demonstrated ability to reconstruct a full attack timeline using Sysmon's file and registry monitoring capabilities, even when the attack is blocked before completing

## Lessons Learned

- Windows service creation via remote tools (like PsExec-style utilities) leaves distinct registry artifacts (`ImagePath` key changes) that can be monitored even if the standard "service installed" event (EventCode 7045) doesn't fire due to fast AV intervention
- Sysmon's granular file and registry event logging provides deeper visibility than default Windows Security logs alone
- This scenario reinforces the importance of layered defenses — even if initial access (brute force) succeeds, endpoint protection can still prevent full compromise
