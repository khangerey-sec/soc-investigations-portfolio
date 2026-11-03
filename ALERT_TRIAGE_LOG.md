# SOC Alert Triage Log

**Environment:** Authorized Security Training Labs (LetsDefend)  
**Project:** Live SOC Monitoring  
**Objective:** Alert validation, evidence correlation, incident classification, and response documentation.

This log records security alerts investigated in training environments. Findings reflect the available telemetry; unverified conclusions and recommended actions are distinguished from confirmed observations.

---

## Alert Summary

| Alert ID | Event ID | Alert Type | Initial Severity | Verdict | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| SOC282 | 257 | Phishing / Suspicious Executable Activity | Medium | True Positive | Closed |

---

## Investigation Records

### SOC282 — Phishing Email and Suspicious Malware Execution

- **Incident date:** 2024-05-13 (time zone not verified)
- **Training platform:** LetsDefend
- **Affected endpoint:** `Felix` (`172.16.20.151`, Windows 10)
- **Recipient:** `Felix@letsdefend.io`
- **Initial severity:** Medium
- **Final verdict:** **True Positive**

#### Initial Detection

The training SOC presented an alert involving a suspicious email delivered to the recipient. The message included a link to a password-protected ZIP archive hosted on AWS S3.

#### Key Findings and Evidence Correlation

1. **Email evidence:** Sender `free@coffeeshooop.com` (sender IP `103.80.134.63`); subject `Free Coffee Voucher`; delivery status `Allowed`. The linked archive was named `free-coffee.zip` and used the password `infected`.
2. **Browser activity:** Endpoint browser history showed access to the archive URL at approximately **12:59** on 2024-05-13.
3. **Process execution:** Endpoint telemetry recorded `Coffee.exe` starting at **13:00:38**, with `explorer.exe` as its parent. At **13:01:00**, `Coffee.exe` spawned `cmd.exe` and executed discovery commands including `systeminfo`, `hostname`, `net user`, `tasklist /svc`, `ipconfig /all`, `route print`, and `wmic logicaldisk get caption,description,providername`.
4. **Network activity:** Firewall telemetry recorded outbound TCP events associated with `Coffee.exe`, from `172.16.20.151` to `37.120.233.226:3451`, at **13:00:39** and **15:01:48**. The firewall action was `Permit`. Available threat intelligence associated the destination IP with **C2 / AsyncRAT** activity.

#### Response Actions Recorded in the Training Scenario

- Performed host isolation through the training platform.
- Initiated phishing-email removal through the response playbook.
- Documented suspicious indicators and investigative findings.
- Classified the alert as **True Positive** and closed the investigation.

#### Scope and Limitations

- Suspicious process execution, host-discovery commands, and outbound network events were observed.
- A firewall `Permit` action means the traffic was allowed by policy; it does **not** independently establish a successful bidirectional C2 session or command execution by a remote operator.
- The destination IP's association with AsyncRAT does **not** independently prove that `Coffee.exe` was an AsyncRAT sample. No malware hash or sample-analysis result was verified.
- Data exfiltration, persistence, lateral movement, and full remediation were **not established** from the available evidence.
- The timestamps above are taken from the training telemetry; their time zone was not verified.

---

## Lessons Learned

- A delivered phishing email requires endpoint and network correlation to assess impact.
- Process parent-child relationships and command-line arguments are central to endpoint triage.
- Threat intelligence enriches evidence but does not replace malware identification or protocol analysis.
- Firewall `Permit` is not proof of successful C2 communication or data exfiltration.
- A defensible SOC report separates confirmed observations, analyst assessments, and unverified outcomes.

---

*All entries describe authorized training or laboratory investigations, not production incidents handled for an employer.*
