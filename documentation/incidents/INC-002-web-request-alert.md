# INC-002 — Suspicious Web Request Alert

**Status:** Closed — Lab validation completed  
**Severity:** Low (controlled lab test; Wazuh rule level 5)  
**Affected host:** Kali Linux VM  
**Wazuh agent:** `kali-vm` (ID `001`)  
**Detection rule:** `31101`  
**Rule description:** `Web server 400 error code.`  
**Log source:** `/var/log/nginx/access.log`  
**Decoder:** `web-accesslog`

## 1. Summary

During a controlled web-log validation exercise, two HTTP requests were sent to the Nginx server running locally on the Kali VM. The request to a nonexistent path returned HTTP 404 and generated a Wazuh alert matching rule `31101`.

## 2. Observed activity

| Request | HTTP status | Source |
|---|---:|---|
| `GET /` | 200 | `127.0.0.1` |
| `GET /does-not-exist-for-wazuh-lab` | 404 | `127.0.0.1` |

The Nginx access log recorded both requests. The Wazuh manager recorded an alert for the 404 request, and the event was subsequently visible in Threat Hunting → Events.

## 3. Detection evidence

- Wazuh rule ID: `31101`
- Rule level: `5`
- Rule description: `Web server 400 error code.`
- Agent: `kali-vm`
- Source IP: `127.0.0.1`
- Requested path: `/does-not-exist-for-wazuh-lab`
- Observed HTTP status: `404`
- Decoder: `web-accesslog`
- Log location: `/var/log/nginx/access.log`
- Dashboard evidence: `evidence/web-404-alert.png`

## 4. Analyst assessment

The request was deliberately generated for local testing. The evidence supports a benign lab event, not a confirmed attack or compromise.

The rule description refers to a 400 error code, while the event contains status 404. This discrepancy should be reviewed against the deployed rule definition before proposing any tuning changes.

## 5. Response and disposition

1. Confirmed that Nginx generated access-log entries.
2. Confirmed that the Wazuh manager generated the matching alert.
3. Confirmed that the event appeared in the Wazuh dashboard.
4. Recorded the observed status/description discrepancy for future investigation.
5. Closed the lab validation incident because the expected test behavior was detected.

No containment or remediation was required for this controlled test.

## 6. MITRE ATT&CK

No specific adversary technique is assigned to this event. A benign HTTP 404 response alone does not establish malicious behavior.

## 7. Limitations

This ticket validates local web-log collection, parsing, alert generation, and dashboard visibility for the tested request. It does not validate SQL injection detection, path traversal detection, exploit detection, remote attack detection, or automated response.