Objective

Configure auditd to monitor /etc/ for file writes + attribute changes and verify detection with forensic evidence.Commands Runbash# 1. Add FIM rule to watch /etc/
sudo nano /etc/audit/rules.d/audit.rules
# Added: -w /etc/ -p wa -k etc_changes

# 2. Load rules + restart auditd
sudo augenrules --load
sudo systemctl restart auditd

# 3. Test detection
sudo touch /etc/testfiles

# 4. Search logs for evidence
sudo ausearch -f /etc/testfiles -i

Key Forensic Evidence from audit.log
type=PATH msg=audit(06/21/2026 10:46:53.501:800) : name=/etc/testfiles nametype=CREATE mode=file,644 ouid=root
type=SYSCALL msg=audit(06/21/2026 10:46:53.501:800) : comm=touch exe=/usr/bin/touch uid=root key=etc_changes
type=CWD msg=audit(06/21/2026 10:46:53.501:800) : cwd=/home/simonImportant fields to note:06/21/2026 10:46:53.501 - Timestamp of eventname=/etc/testfiles - File created in critical directory  nametype=CREATE - Action was file creationouid=root comm=touch - Root user ran touch commandkey=etc_changes - Custom audit rule tag fired

Important fields to note:
06/21/2026 10:46:53.501 - Timestamp of event
name=/etc/testfiles - File created in critical directory  
nametype=CREATE - Action was file creation
ouid=root comm=touch - Root user ran touch command
key=etc_changes - Custom audit rule tag fired

Screenshot
screenshots/day6-auditd-fim-evidence.png

Learning
Auditd provides metadata for FIM: who, what file, what action, when. It does NOT log file contents. For content change detection, AIDE/Tripwire is needed.Next
Day 7: Deploy AIDE to detect content/ hash changes in /etc/Previous
Day 5 - Install + Configure auditdLab Environment
VirtualBox | Ubuntu 24.04 | auditd 4.0.3
