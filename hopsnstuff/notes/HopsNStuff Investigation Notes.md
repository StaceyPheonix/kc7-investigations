markdown

# HopsNStuff Investigation Notes
*A complete analyst notebook documenting reasoning, troubleshooting, and investigation flow.*

These notes capture my full workflow during the KC7 HopsNStuff investigation.  
They include:

- my queries  
- my troubleshooting  
- my false starts  
- my reasoning  
- how I interpreted KC7 wording  
- how I linked tables together  
- how I recognized KC7 investigation patterns  

---

# Warm-Up & Practice Investigations

---

## Distinct senders from easterdelights.org

**Goal:** Filter email logs by domain and count unique senders.

**Troubleshooting Notes:**
- KC7 email logs often contain multiple variations of the same domain.
- Using `contains` is safer than `endswith`.

**Query:**
```kusto
Email
| where sender contains "easterdelights"
| distinct sender
| count

Arthur Raymond – Unique URLs visited

Challenge:  
OutboundNetworkEvents does not contain a username field.

Breakthrough:  
Use the Employees table to get Arthur’s IP, then filter browsing by IP.

Step 1 – Get Arthur’s IP
kusto

Employees
| where name == "Arthur Raymond"

Step 2 – Count URLs
kusto

OutboundNetworkEvents
| where src_ip == "<ARTHUR_IP>"
| distinct url
| count

Notes:  
This taught me the KC7 pattern:
User → IP → Browsing Activity
Domains containing “automation”

Goal: Practice filtering PassiveDNS.

Query:
kusto

PassiveDns
| where domain contains "automation"
| summarize dcount(domain)

IPs for automationpackages.com

Goal: Understand domain-to-IP resolution.

Query:
kusto

PassiveDns
| where domain == "automationpackages.com"
| distinct ip

Unique URLs browsed by employees named Karen

Troubleshooting:  
I forgot to filter by Karen’s IP(s).
This query returns all URLs, not Karen’s — but KC7 accepted it in warm-ups.

Query:
kusto

Employees
| where username == "Karen"

OutboundNetworkEvents
| distinct url

Quarantined file alert – host and employee

Goal: Link alerts → host → employee.

Find the host:
kusto

SecurityAlerts
| where description contains "quarant"
| sort by timestamp asc

Find the employee:
kusto

Employees
| where hostname == "QBYQ-DESKTOP"

Identifying the suspicious file

Troubleshooting:  
I forgot the filename from the earlier exercise.
I scanned for suspicious filenames by frequency.

Query to rediscover the file:
kusto

FileCreationEvents
| summarize count() by filename
| order by count_ desc

Suspicious file identified: Dev-Requirements.zip

Count unique hosts:
kusto

FileCreationEvents
| where filename contains "Dev-Requirements.zip"
| distinct hostname
| count

Section 2 – HopsNStuff Investigation Chain
Distinct senders from easterdelights.org

Query:
kusto

Email
| where sender contains "easterdelights"
| distinct sender
| count

Unique URLs visited by Arthur Raymond

Reasoning:  
OutboundNetworkEvents has no username → must use IP.

Queries:
kusto

Employees
| where name == "Arthur Raymond"

kusto

OutboundNetworkEvents
| where src_ip == "<ARTHUR_IP>"
| distinct url
| count

Unique hosts with Dev-Requirements.zip

Query:
kusto

FileCreationEvents
| where filename contains "Dev-Requirements.zip"
| distinct hostname
| count

Earliest timestamp Dev-Requirements.zip was seen

Troubleshooting:  
KC7 locks the answer box, not the tables.

Query:
kusto

FileCreationEvents
| where filename contains "Dev-Requirements.zip"
| sort by timestamp asc
| take 1

Role of employee using earliest host

Earliest host: FTVO-LAPTOP
Username: megeisinsky

Query:
kusto

Employees
| where username == "megeisinsky"

External emails received by this employee

Goal: Count external emails to the employee from Q5.

Query:
kusto

Email
| where recipient == "meghann_geisinsky@hopsnstuff.com"
| where not(sender endswith "@hopsnstuff.com")
| count

External emails to employees on infected hosts

This was the hardest question.
Troubleshooting:

    KC7 wording is misleading.

    There is no “infected user” column.

    KC7 always uses the chain:
    Host → Employee → Email

Required steps:

    Get the 9 infected hosts

    Map hosts to employees

    Map employees to email addresses

    Count external emails to those employees

Final Query:
kusto

Email
| where recipient in (
    Employees
    | where hostname in (
        FileCreationEvents
        | where filename == "Dev-Requirements.zip"
        | distinct hostname
    )
    | project email_addr
)
| where not(sender endswith "@hopsnstuff.com")
| count

Most recent blocked/suspicious sender to those employees

Troubleshooting:  
KC7 wording is confusing.
What it really means is:

“From the same employees in Q7, find the most recent BLOCKED or SUSPICIOUS email and return the sender.”

Query:
kusto

Email
| where recipient in (
    Employees
    | where hostname in (
        FileCreationEvents
        | where filename == "Dev-Requirements.zip"
        | distinct hostname
    )
    | project email_addr
)
| where verdict == "BLOCKED" or verdict == "SUSPICIOUS"
| sort by timestamp desc
| project sender, timestamp, subject, verdict
| take 1

KC7 Investigation Pattern (Reusable)

KC7 investigations always follow this chain:

    Identify infected hosts

    Map hosts to employees

    Map employees to email activity

    Filter internal vs external

    Filter by verdict (blocked/suspicious)

    Sort by timeline

    Extract the required field

Once I recognized this pattern, Q7 and Q8 became predictable.
End of Notes
