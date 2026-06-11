# Sohail Incident Case – CRM Credential Compromise Investigation

## Investigation Notes

This investigation focuses on a simulated security incident affecting Sohail’s CRM Platform. The incident involved brute-force login activity, unauthorized access, credential compromise, and suspicious account activity.

The SOC alert was triggered after multiple failed login attempts were detected against a CRM user account, followed by a successful login from the same suspicious external IP address.

**Selected Service Area:** Sohail CRM Platform  
**Affected Account:** `m.alhassan@sohail.local`  
**Suspicious Source IP:** `185.77.45.91`  
**Suspicious Session ID:** `S-88421`  
**Incident Type:** Credential compromise and unauthorized CRM access  
**Severity:** Medium to High  

The CRM Platform stores customer profiles, sales leads, contact details, account notes, and communication history. Because this system contains sensitive business and customer information, unauthorized access could create privacy, operational, and reputational risks.

### Initial Analyst Observations

- Five failed login attempts occurred within approximately two minutes.
- A successful login followed from the same suspicious IP address.
- The login came from an unknown browser/device.
- The account accessed customer records after login.
- A bulk export of 2,500 customer lead records was attempted.
- The export attempt was blocked by the CRM system.

### Analyst Conclusion

The activity is consistent with a brute-force attack followed by successful use of valid credentials. The account was likely compromised and used to access CRM customer information.

---

## Simulated Logs

The following simulated logs represent the authentication, application, alert, and response evidence for this investigation.

```text
2026-06-11 09:14:03 CRM-AUTH Failed login user=m.alhassan@sohail.local src_ip=185.77.45.91 reason=Invalid password
2026-06-11 09:14:25 CRM-AUTH Failed login user=m.alhassan@sohail.local src_ip=185.77.45.91 reason=Invalid password
2026-06-11 09:14:48 CRM-AUTH Failed login user=m.alhassan@sohail.local src_ip=185.77.45.91 reason=Invalid password
2026-06-11 09:15:12 CRM-AUTH Failed login user=m.alhassan@sohail.local src_ip=185.77.45.91 reason=Invalid password
2026-06-11 09:15:39 CRM-AUTH Failed login user=m.alhassan@sohail.local src_ip=185.77.45.91 reason=Invalid password
2026-06-11 09:16:02 CRM-AUTH Successful login user=m.alhassan@sohail.local src_ip=185.77.45.91 session_id=S-88421
2026-06-11 09:17:16 CRM-APP Customer search user=m.alhassan@sohail.local query=all_active_clients session_id=S-88421
2026-06-11 09:18:04 CRM-APP Export attempted user=m.alhassan@sohail.local object=customer_leads.csv records=2500 status=blocked
2026-06-11 09:19:10 CRM-AUTH New device detected user=m.alhassan@sohail.local device=Unknown-Browser location=Unknown
2026-06-11 09:21:00 SOC-ALERT Alert generated rule="Multiple Failed Logins Followed by Successful Login" severity=High user=m.alhassan@sohail.local src_ip=185.77.45.91
2026-06-11 09:26:00 SOC-RESPONSE Account disabled user=m.alhassan@sohail.local action=containment
2026-06-11 09:27:10 SOC-RESPONSE Session revoked session_id=S-88421 action=containment
2026-06-11 09:28:30 FIREWALL Blocked IP src_ip=185.77.45.91 action=deny
2026-06-11 09:40:00 IAM Password reset completed user=m.alhassan@sohail.local action=eradication
```

---

## Timeline

| Time | Event | Evidence Source |
|---|---|---|
| 09:14:03 | First failed CRM login from suspicious IP | CRM-AUTH logs |
| 09:14:25 | Second failed login attempt | CRM-AUTH logs |
| 09:14:48 | Third failed login attempt | CRM-AUTH logs |
| 09:15:12 | Fourth failed login attempt | CRM-AUTH logs |
| 09:15:39 | Fifth failed login attempt | CRM-AUTH logs |
| 09:16:02 | Successful login from the same suspicious IP | CRM-AUTH logs |
| 09:17:16 | Customer search activity performed | CRM-APP logs |
| 09:18:04 | Bulk export of customer leads attempted | CRM-APP logs |
| 09:19:10 | New unknown device detected | CRM-AUTH logs |
| 09:21:00 | SIEM alert generated | SOC alert logs |
| 09:26:00 | Account disabled | SOC response logs |
| 09:27:10 | Active session revoked | SOC response logs |
| 09:28:30 | Suspicious IP blocked | Firewall logs |
| 09:40:00 | Password reset completed | IAM logs |

### Timeline Summary

The attacker attempted several failed logins against the CRM account before successfully authenticating. After gaining access, the account was used to search customer records and attempt a bulk export. The SOC team responded by disabling the account, revoking the session, blocking the IP address, and resetting the password.

---

## Detection Evidence

### Detection Rule Triggered

**Multiple Failed Logins Followed by Successful Login**

### Connection to Previous Detection Rules

This investigation uses the same detection logic created in the previous task. The previous detection rules focused on identifying failed login attempts, successful logins after repeated failures, and suspicious account behavior.

In this case, the rule **“Multiple Failed Logins Followed by Successful Login”** triggered because the same source IP address generated five failed login attempts against the same CRM account, followed by a successful login.

### Why the Rule Triggered

The source IP address `185.77.45.91` generated five failed login attempts against the CRM account `m.alhassan@sohail.local`. Shortly after the failed attempts, the same IP address successfully logged in.

This pattern is suspicious because it suggests that the attacker may have guessed the correct password or used compromised credentials.

### Evidence Observed

- Five failed login attempts
- One successful login from the same source IP
- Unknown browser/device detected
- Customer search activity after login
- Attempted export of 2,500 customer lead records
- Export attempt was blocked

### Indicators of Compromise

| Indicator | Value |
|---|---|
| Suspicious IP Address | `185.77.45.91` |
| Compromised Account | `m.alhassan@sohail.local` |
| Attack Type | Brute-force login activity |
| Unauthorized Activity | Successful login from suspicious IP |
| Suspicious Action | Attempted customer lead export |
| Affected Service | Sohail CRM Platform |
| Device Indicator | Unknown browser/device |
| Session ID | `S-88421` |

### Detection Logic

```text
IF failed_login_count >= 5
AND same_user = true
AND same_source_ip = true
AND successful_login occurs within 5 minutes
THEN generate alert "Multiple Failed Logins Followed by Successful Login"
```



## Investigation Artifacts

The investigation artifacts are the evidence and analysis materials created during the SOC investigation. These artifacts support the conclusion that the CRM account was likely compromised through brute-force activity.

| Artifact | Purpose |
|---|---|
| Simulated CRM Logs | Show failed logins, successful login, customer search, and export attempt |
| Timeline Reconstruction | Shows the order of attacker activity and response actions |
| Detection Evidence | Explains why the alert rule triggered |
| Indicators of Compromise | Lists suspicious IP, compromised account, session ID, and unknown device |
| Analyst Notes | Summarizes initial observations and investigation conclusion |
| MITRE ATT&CK Mapping | Connects the activity to brute-force and valid account techniques |
| Response Actions | Shows containment and eradication steps taken by the SOC team |


