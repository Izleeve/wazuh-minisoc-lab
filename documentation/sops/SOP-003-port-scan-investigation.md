# SOP-003 — Port Scan Investigation in Wazuh

## 1. Purpose

Define a repeatable L1 SOC workflow for investigating suspected TCP port-scanning activity and validating whether relevant telemetry generates Wazuh alerts.

## 2. Scope

This procedure applies to authorized lab investigations involving Kali Linux, Nmap, and the Wazuh monitoring platform. Use it only on systems you are authorized to assess.

## 3. Prerequisites

- Access to the Wazuh dashboard or manager.
- The monitored endpoint and its Wazuh agent identity.
- The suspected activity's timestamp and timezone.
- Available scan output or other supporting evidence.
- Authorization to investigate the target system.

## 4. Investigation Procedure

### Step 1 — Validate the report

Record the suspected source, target, time window, reported activity, and authorization. Confirm whether the activity is expected or approved.

### Step 2 — Review scan evidence

If scan output is available, record:

- Tool and command used.
- Target IP address.
- Scan type and port range.
- Open, closed, and filtered ports reported.
- Scan start time and duration.

Do not interpret open ports alone as proof of malicious activity.

### Step 3 — Check agent health

Confirm that the relevant Wazuh agent is active. Review agent logs for communication or collection errors if necessary.

An active agent does not, by itself, prove that port-scan telemetry is being collected.

### Step 4 — Search Wazuh alerts

Search the relevant time window and inspect available alerts for:

- Source and destination addresses, where available.
- Event timestamps and timezone.
- Rule ID, description, and severity.
- Connection attempts or repeated activity across ports.
- Whether the event corresponds to the reported scan.

Use the actual event time and an appropriate dashboard time range. Do not attribute an unrelated or earlier alert to the scan.

### Step 5 — Validate the result

Classify the finding using the evidence:

- **Detection confirmed:** A relevant alert is found and its details correlate with the scan.
- **No matching alert found:** The records examined contain no correlated alert.
- **Validation inconclusive:** Telemetry, timestamps, ingestion, or search coverage is insufficient to determine whether detection occurred.

Do not claim successful detection unless a correlated event has been verified.

### Step 6 — Assess and escalate

For authorized lab activity, document the result and any monitoring limitations. In a production environment, follow organizational escalation procedures if the activity is unexpected or potentially malicious.

Do not block addresses or stop services without appropriate authorization.

### Step 7 — Document and close

Record the evidence, findings, limitations, actions taken, and recommended follow-up in the incident ticket. Keep screenshots and exported evidence free of passwords, tokens, private keys, and unrelated sensitive information.

## 5. Validation Record — INC-003

- **Target:** `192.168.182.128`
- **Tool:** Nmap 7.99
- **Scan:** TCP connect scan, top 100 TCP ports, timing template T2
- **Observed open ports:** 22/tcp (SSH), 80/tcp (HTTP)
- **Agent status:** `kali-vm`, agent ID `001`, reported active
- **Wazuh outcome:** No Nmap-specific alert confirmed in the supplied records
- **Conclusion:** Detection not demonstrated; underlying cause not determined

The manager's latest supplied alert record was timestamped `2026-10-25T17:05:43.340Z`, before the repeat scan began at approximately `17:06 UTC`. Therefore, the available records do not establish post-scan detection or conclusively prove that all relevant telemetry was absent.

## 6. Recommended Follow-up

1. Verify the post-scan time range in Wazuh.
2. Confirm which network or connection telemetry sources are available.
3. Review relevant collection settings and existing rules.
4. If appropriate, develop a narrowly scoped detection rule and test it with authorized scan activity.
5. Document the rule ID and correlated event evidence only after successful validation.

## 7. Closure Criteria

Close the investigation when the activity has been assessed, evidence is recorded, and the outcome is accurately classified. If detection remains unverified, retain the detection gap as a follow-up item rather than reporting a successful detection.

**Status for INC-003:** Investigation documented; port-scan detection not confirmed.
