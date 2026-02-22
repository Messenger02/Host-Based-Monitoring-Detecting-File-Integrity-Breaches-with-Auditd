# Host-Based-Monitoring-Detecting-File-Integrity-Breaches-with-Auditd
Tools: Auditd, Linux Terminal, Vim, Chmod
Context: CodePath Intermediate Assignment

## Project Overview
The Mission: I was tasked with configuring the Linux Audit Daemon, Auditd, to monitor sensitive files and identify which specific malicious scripts were responsible for unauthorized file modifications. this project simulated an Endpoint Detection and Response (EDR) scenario where I had to correlate process activity with file system changes.

### Environment & Setup
Target Files: cloudia.txt, oakley.txt, squeaky.txt, and precipition.csv

Attack vectors: Three proprietery shell scripts (attack-a, attack-b, and attack-c) designed to simulate common malicious behaviors like data tampering and exfiltration.

Configuration: I utilized vim to manage the attack stripts and used chmod + x to grant execution permissions, simulating the final stage of a successful exploit where an attacker prepares their payload. 

## Methodology: The Investigation
To capture evidence, I followed a three-phase workflow:
1. Rule Configuration: I configured auditctl to place "watches" on the target files. I specifically looked for write (w) and attribute change (a) permissions to ensure I captured any attempt to modify or overwrite the data
2. Execution & Simulation: I executed all three attack scripts at a controlled sequence. This allowed me to generate a baseline of activity that would be recorded in the system logs
3. Log Forensics: using ausearch and aureport, I parsed the raw audit logs to map the Process ID (PID) of the scripts to the file path of the modified documents.
## Analysis & Findings: Identifying the Culprits
the investigation successfully mapped each attack script to its specific target, revealing a targeted multi-vector attack:
* Attack-A: Only made changes to cloudia.txt
* Attack-B: Targeted multiple files, modifying both oakley.txt and squeaky.txt.
* Attack-C: only targeted precipitation.
## Challenges & Solutions
* The Challenge: Raw audit logs are notoriously "noisy" and difficult to read. Initially, findig a single write event in a sea of system calls felt like finding a needle in a haystack.
* The Solution: I learned to leverage the -k flag in auditctl. By assigning a specific tag (like auditctl -w /home/codepath/unit2_lab.txt -p w -k unit2_lab_changes), I was able to instantly filter the logs using ausearch -k unit2_lab_changes, which drastically reduced the time to detect.
## Future Improvements
In a professional SOC environment, manually checking logs is ineffecient. My next step for this project would be to ingest these auditd logs into a SIEM (like Splunk). this would allow for real-time dashboarding and the creation of automated alerts when critical system files are touched by non-root users. 
