# SOP-001: SSH Authentication Failure Triage

**Purpose:** Provide a repeatable L1 SOC workflow for investigating SSH authentication-failure alerts in Wazuh.

**Scope:** Authorized monitoring environments and approved security tests.

**Primary detection:** Wazuh rule `5710` — SSH attempt to log in using a non-existent user.

**Related detection:** Rule `5503` — PAM user login failure.

## 1. Initial alert review

Record the following from the Wazuh event:

- Alert timestamp and timezone
- Agent name and ID
- Rule ID, description, and severity
- Source IP address and source username, when available
- Full event message
- Related alerts within the same time window

Do not assume that a single failed login proves an attack.

## 2. Validate the event

1. Open **Threat Hunting → Events** in Wazuh.
2. Filter by the relevant rule ID and agent name.
3. Open the individual event and inspect its full log message.
4. Compare related `Invalid user`, `Failed password`, and PAM authentication-failure events.
5. Establish whether the source is localhost, an internal host, or an external address.
6. Check the affected host's authentication logs and, where authorized, relevant identity or access records.

Example DQL query:

`rule.id: 5710 AND agent.name: "kali-vm"`

Replace the agent name and rule ID as appropriate for the alert under investigation.

## 3. Assess severity and context

Consider:

- Number and frequency of failures
- Number of distinct usernames attempted
- Source IP and whether it is expected
- Whether a successful login followed the failures
- Whether the activity coincides with approved testing or maintenance
- Whether other alerts indicate suspicious activity on the same host

Treat localhost events generated during an approved lab test as test activity when the evidence supports that conclusion.

Escalate repeated or distributed attempts, unexpected privileged-account targeting, suspicious successful logins, or associated indicators of compromise according to the organization's incident-response policy.

## 4. Response and escalation

- **Confirmed authorized test:** Document the test, preserve evidence, and close according to the lab's workflow.
- **Suspicious but unconfirmed activity:** Collect relevant timestamps, source details, usernames, and related events; escalate to the designated analyst.
- **Credible compromise indicators:** Follow the organization's incident-response and containment procedures. Obtain authorization before isolating hosts or changing access controls.

Do not automatically block an IP based solely on one authentication-failure alert.

## 5. Evidence and ticket requirements

Record:

- Incident ID and analyst
- Start and end time of the observed activity
- Alert rule and severity
- Affected agent and source details
- Relevant event messages or sanitized screenshots
- Investigation steps and findings
- Classification, escalation decision, and closure rationale

Remove passwords, authentication secrets, private keys, and unrelated personal data from evidence before sharing it.

## 6. Closure criteria

Close only when the event has been classified, supporting evidence has been recorded, required escalation has occurred, and the disposition is documented.

## Lab validation record

In this project, three deliberately failed SSH login attempts were generated from Kali to `127.0.0.1` using nonexistent usernames. The manager recorded rule `5710` events and related PAM rule `5503` events. The matching rule-5710 events were visible in Wazuh Threat Hunting.

This validates the tested detection path for local invalid-user SSH activity. It does not establish detection of remote brute-force activity, automatic blocking, or successful compromise prevention.