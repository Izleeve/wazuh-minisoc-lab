# INC-001 — SSH Invalid-User Authentication Attempts

**Status:** Closed — Controlled test completed  
**Severity:** Low (lab validation)  
**Source host:** `kali-vm`  
**Wazuh agent ID:** `001`  
**Detection rule:** `5710` — SSH attempt using a non-existent user  
**Rule level:** `5`  
**Related detection:** Rule `5503` — PAM login failure  
**Source IP:** `127.0.0.1`

## Summary
Three deliberate SSH login attempts against the Kali VM used nonexistent usernames. Wazuh collected the authentication messages through the agent's systemd journal integration and generated rule-5710 alerts.

## Evidence
- SSH journal entries show invalid-user and failed-password messages.
- The Wazuh manager recorded rule `5710` and related rule `5503` events.
- Threat Hunting → Events displayed six matching rule-5710 events for `kali-vm`.

## Analyst assessment
The observed activity was generated intentionally for a local detection-validation exercise. No evidence in this test indicates a real attacker or compromise.

## Response
- Confirmed agent connectivity and event collection.
- Verified the matching alert in Wazuh Threat Hunting.
- Captured dashboard evidence.
- No containment or remediation was required for this controlled test.

## MITRE ATT&CK
Rule metadata maps the behavior to T1110.001 (Password Guessing) and T1021.004 (SSH). This mapping describes the detected behavior; it does not establish malicious intent.

## Closure criteria
Closed after confirming that the expected test events were collected and detected. This result does not independently validate remote brute-force detection, threshold-based correlation, or automated response.