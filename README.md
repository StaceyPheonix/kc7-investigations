markdown

# KC7 Investigations - HopsNStuff APT Hunt

This repository contains my full investigation work for the **KC7 HopsNStuff: An Easter APT Hunt** module.  
It includes:

- A complete analyst notebook documenting my reasoning and investigation flow  
- All KQL queries used for Q1–Q8  
- MITRE ATT&CK mapping for the observed adversary behaviors  
- Organized folder structure for clarity and professionalism  

This project demonstrates real SOC analyst skills including log analysis, threat hunting, correlation, and investigative documentation.

---

## Repository Structure

```text
kc7-investigations/
├── README.md          ← You are here
└── hopsnstuff/
    ├── notes/
    │   └── HopsNStuff Investigation Notes.md
    ├── queries/
    │   ├── q1_distinct_senders.kql
    │   ├── q2_arthur_unique_urls.kql
    │   ├── q3_hosts_with_file.kql
    │   ├── q4_earliest_timestamp.kql
    │   ├── q5_role_of_employee.kql
    │   ├── q6_external_emails_for_employee.kql
    │   ├── q7_external_emails_for_infected_hosts.kql
    │   └── q8_most_recent_blocked_sender.kql
    └── README.md      (optional folder README)

MITRE ATT&CK Mapping
The activity observed in the HopsNStuff investigation aligns with several MITRE ATT&CK techniques.
This mapping reflects the adversary behaviors identified through log analysis.


Initial Access
T1566.001 – Phishing: Spearphishing Attachment  
Malicious ZIP file (Dev-Requirements.zip) delivered via external email.


Execution
T1204.002 – User Execution: Malicious File  
Employees executed the ZIP file after receiving it.


Discovery
T1083 – File and Directory Discovery  
Malware likely enumerated directories after execution.


Collection
T1114 – Email Collection  
Targeting employee inboxes and external email interactions.


Command and Control
T1071 – Application Layer Protocol  
Outbound network events suggest possible C2 communication attempts.


Exfiltration
T1041 – Exfiltration Over C2 Channel  
Potential exfiltration behavior flagged for further investigation.


## MITRE Summary Table

| Stage             | Technique Name              | ID        | Description                         |
|-------------------|----------------------------|-----------|-------------------------------------|
| Initial Access    | Spearphishing Attachment   | T1566.001 | Malicious ZIP delivered via email   |
| Execution         | User Execution             | T1204.002 | User opens malicious file           |
| Discovery         | File/Directory Discovery   | T1083     | Malware enumerates system           |
| Collection        | Email Collection           | T1114     | Targeting inbox data                |
| Command & Control | Application Layer Protocol | T1071     | Suspicious outbound traffic         |
| Exfiltration      | Exfiltration Over C2       | T1041     | Possible data exfiltration          |


Skills Demonstrated
- Email log investigation  
- Network event correlation  
- Host-based artifact review  
- Threat hunting methodology  
- MITRE ATT&CK mapping  
- Documentation and reporting  
- SOC-style investigation workflow  


How to Navigate This Repo
**Investigation Notes**  
`hopsnstuff/notes/HopsNStuff Investigation Notes.md`  
Contains full reasoning, troubleshooting, and investigation flow.


**Queries**  
`hopsnstuff/queries/`  
Contains all KQL queries used for Q1–Q8.


**MITRE Mapping**  
Included in this README for quick reference.


About This Project
This project is part of my cybersecurity investigation training through KC7.  


It demonstrates my ability to:
- Analyze multi-source logs  
- Identify malicious activity  
- Map behaviors to ATT&CK  
- Document investigations professionally  
- Build a clean, organized GitHub portfolio  
