# INC-003 — Nmap Port Scan With No Confirmed Wazuh Detection

## Incident Summary

| Field | Details |
|---|---|
| Incident ID | INC-003 |
| Title | Controlled Nmap Port Scan — Detection Gap |
| Date | 25 October 2026 |
| Source host | Kali Linux VM (`192.168.182.128`) |
| Target | Same Kali Linux VM (`192.168.182.128`) |
| Tool | Nmap 7.99 |
| Monitoring platform | Wazuh 4.14.9 |
| Agent | `kali-vm`, agent ID `001` |
| Status | Closed — Detection Not Confirmed |
| Assessment | Monitoring validation / detection gap |
| Authorization | Controlled scan of the lab VM |

## 1. Objective

Validate the result of a limited TCP port scan against the authorized Kali Linux lab VM and determine whether the current Wazuh configuration generated a corresponding detection alert.

The purpose was to test monitoring visibility, not to exploit a service or scan an external system.

## 2. Test Procedure

The following command was executed from the Kali Linux VM:

```bash
nmap -sT -T2 --top-ports 100 192.168.182.128 -oN ~/nmap-inc003.txt
```

A repeat scan was subsequently performed:

```bash
nmap -sT -T2 --top-ports 100 192.168.182.128 -oN ~/nmap-inc003-repeat.txt
```

Both commands targeted the lab VM's own IP address. The scan used TCP connect scanning, a conservative timing template, and the 100 most common TCP ports.

## 3. Observed Results

The repeat scan reported:

| Port | Service | Observed state |
|---|---|---|
| 22/tcp | SSH | Open |
| 80/tcp | HTTP | Open |
| Other 98 scanned TCP ports | Various | Closed |

The target was reachable, and the scan completed in approximately 40.63 seconds.

These results confirm that Nmap performed the scan and identified the listening SSH and HTTP services.

## 4. Wazuh Validation

The following checks were performed:

- Confirmed that Wazuh agent `001` (`kali-vm`) was listed as active on the manager.
- Checked the Kali agent service and confirmed it was running.
- Reviewed the agent log collector output, which showed monitoring of the Nginx access and error logs, Apache log paths, and system journal.
- Examined `/var/ossec/logs/alerts/alerts.json` on the Wazuh manager.
- Searched alert records for the Kali IP address and terms associated with Nmap or port-scan detection.

The latest supplied alert record was timestamped `2026-10-25T17:05:43.340Z`. The repeat scan began at approximately `2026-10-25 17:06 UTC`.

The returned records did not contain a confirmed Nmap-specific alert associated with the repeat scan. Earlier alerts, including rule `533` for a change in listening-port status, predated the scan and were not treated as evidence of scan detection.

## 5. Assessment

**Result: Scan successful; Wazuh detection not demonstrated.**

The scan output establishes that the target's open ports were discovered. The available Wazuh evidence does not establish that the scan itself generated a security alert.

The agent's active status and log-collection messages show that the collection components were running, but they do not prove that Nmap activity was being collected or detected.

The underlying reason for the missing detection remains undetermined. Possible areas for further investigation include available network telemetry, agent collection configuration, existing detection rules, and whether relevant events are indexed and searchable in the dashboard.

This result should not be described as a successful Nmap detection or as proof that Wazuh is incapable of detecting port scans.

## 6. Response and Recommended Follow-up

No containment action was required because this was an authorized scan of the lab VM.

Recommended follow-up:

1. Review which telemetry sources are available for observing connection attempts and network activity.
2. Determine whether the existing Wazuh configuration collects the relevant events.
3. If suitable telemetry is available, evaluate a narrowly scoped detection rule for repeated connection attempts across multiple ports.
4. Repeat the authorized scan after any configuration changes.
5. Validate the resulting event timestamp, source, target, rule ID, alert description, and dashboard visibility.
6. Update this incident only after the evidence supports the revised conclusion.

Any future detection rule should be tested against both scan activity and ordinary service connections to assess false positives.

## 7. Evidence and Limitations

Evidence available for this test:

- Nmap output showing the target was up and ports 22/tcp and 80/tcp were open.
- Wazuh manager output listing agent `001` as active.
- Kali agent service status and recent agent log output.
- Wazuh alert records and the search output supplied during validation.

Limitations:

- No Nmap-specific Wazuh alert was confirmed.
- The supplied alert-file checks do not independently establish whether all relevant events were collected, indexed, or searchable.
- No custom port-scan detection rule was validated as part of this test.
- No exploit attempt, compromise, or unauthorized activity was demonstrated.

## 8. Closure

INC-003 is closed for this test cycle as a **documented detection gap**. The scan results were obtained successfully, but detection by Wazuh remains unverified.

The next improvement is to establish an appropriate telemetry source and validate a detection rule with repeatable evidence before claiming port-scan detection capability.

**Portfolio note:** This is a controlled local lab test. It demonstrates testing methodology, evidence review, honest reporting of limitations, and identification of a monitoring improvement opportunity—not detection of a real-world attack.
