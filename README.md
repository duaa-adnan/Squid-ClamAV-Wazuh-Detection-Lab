Blue Team Internship. ITSimplera Solutions
Week 3 Task: Malware Detection & Monitoring with pfSense, Squid, ClamAV & Wazuh

Internship Track Tool Status

Author: Duaa Bint E Adnan

## Overview
This repository documents my work as a SOC Analyst Intern at ITSimplera Solutions, undertaken as part of a hands-on Security Operations Center (SOC) internship program. The goal of the internship is to build practical, industry-relevant skills in defensive cybersecurity by working directly with the tools and workflows used in real-world SOC environments.

This README covers Week 3, in which I deployed a pfSense firewall and a Squid proxy with ClamAV, reconnected and verified an existing Wazuh SIEM deployment, tested malware detection using the EICAR file, built a log forwarding pipeline with Syslog-NG, and developed a custom Wazuh decoder and detection rule after discovering that Wazuh's own built-in Squid decoder does not work correctly in this version.

## Table of Contents
- Week 3 – Task Brief
- Objective
- Lab Environment
- What I Implemented
- Challenges & Troubleshooting
- Tools & Technologies
- Skills Demonstrated
- Lessons Learned
- Outcome
- Documentation
- Future Improvements

## Week 3 — Task Brief
| Detail | Info |
|---|---|
| Task Type | Setup + Exploration |
| Role | SOC Analyst Intern |

## Objective
Deploy and configure a pfSense firewall, a Squid proxy with ClamAV, and integrate them with the Wazuh SIEM platform to detect and monitor malware activity. Analyse security logs, identify indicators of compromise, test malware detection using the EICAR file, and produce an Incident Response Plan based on the results.

## Lab Environment
| Component | Details |
|---|---|
| Host Machine | Windows 11 |
| Hypervisor | VMware Workstation |
| Firewall | pfSense CE 2.7.2 |
| Proxy / AV Host | Ubuntu Server 22.04 LTS (Squid + ClamAV) |
| SIEM Platform | Wazuh 4.9.2 (Manager, Indexer, Dashboard) |
| Log Forwarding | Syslog-NG |
| Test File | EICAR standard antivirus test file |

## What I Implemented
- Deployed and configured a pfSense firewall as the network perimeter for the lab
- Installed and configured a Squid proxy server, with access rules restricting the LAN
- Installed and configured ClamAV, with signatures kept up to date
- Attempted a full ICAP integration between Squid and ClamAV for inline file scanning (see Challenges below)
- Reconnected to and verified an existing Wazuh SIEM deployment, resolving both a service-level and a host networking fault
- Downloaded and scanned the EICAR test file with ClamAV to validate signature-based malware detection
- Configured Syslog-NG to forward Squid's access log to Wazuh's remote syslog listener, and verified the pipeline end-to-end with live traffic
- Diagnosed a genuine defect in Wazuh's built-in Squid decoder, which failed to match even its own documented example log line
- Built a fully custom Wazuh decoder and detection rule for Squid proxy events, verified against live proxy traffic and confirmed in the Wazuh Dashboard
- Documented every step of the process with supporting screenshots
- Wrote an Incident Response Plan based on the EICAR test and the custom rule verification

## Challenges & Troubleshooting

**Challenge 1 — ICAP integration between Squid and ClamAV**
Cause: After enabling ICAP in Squid's configuration to scan files inline via c-icap and squidclamav, all HTTPS traffic through the proxy began failing with a 500 error on every request.
Resolution: Confirmed the issue was isolated to the ICAP configuration by disabling it and restoring normal proxy function. Root cause was not fully isolated within the project deadline.
Result: The ICAP configuration was reverted to restore a working proxy. Documented as an incomplete item rather than presented as working.

**Challenge 2 — Wazuh Dashboard unreachable after VM restart**
Cause: The Windows host's VMware network adapter was receiving an invalid, auto-assigned IP address instead of a proper lease, silently routing traffic over Wi-Fi instead of the lab network.
Resolution: Diagnosed using Test-NetConnection and Get-NetIPAddress, then assigned a static IP directly to the adapter.
Result: Dashboard access restored; noted that the fix does not persist across a Windows reboot and must be reapplied.

**Challenge 3 — Wazuh's built-in Squid decoder not matching any log**
Cause: After ruling out the log format, the syslog envelope, and my own configuration, testing Wazuh's own documented example log line directly showed it also failed to match its own built-in decoder.
Resolution: Located and reviewed the decoder's source file (0305-squid_decoders.xml) to confirm the expected format, then built a fully custom decoder and detection rule rather than continuing to debug a component that did not work as documented.
Result: Verified via wazuh-logtest and, subsequently, a real live-fire alert in the Wazuh Dashboard generated from genuine blocked proxy traffic.

**Challenge 4 — Local disk full, VM would not power on**
Cause: The host machine's C: drive ran out of free space, preventing VMware from writing to the VM's virtual disk file.
Resolution: Freed up disk space (cleared temporary files, removed old VM snapshots) and retried.
Result: VM powered on normally once sufficient free space was available.

## Tools & Technologies
- pfSense (firewall / network perimeter)
- Squid (proxy server)
- ClamAV (antivirus engine)
- c-icap / squidclamav (attempted ICAP integration)
- Wazuh (Manager, Indexer, Dashboard, Agent)
- Syslog-NG (log forwarding)
- EICAR test file (malware detection validation)
- Command-line administration on Ubuntu Server for all service configuration
- wazuh-logtest for decoder and rule development/debugging

## Skills Demonstrated
- Deploying and configuring a firewall and proxy server as a network security perimeter
- Integrating an antivirus engine with a proxy server, including working through an unresolved real-world integration failure
- Reconnecting to and troubleshooting an existing SIEM deployment, including host-level networking faults
- Validating malware detection using an industry-standard, safe test methodology
- Designing a log forwarding pipeline (Syslog-NG to SIEM) and verifying it with live traffic rather than assuming it works
- Reading vendor-provided detection logic critically, diagnosing a genuine product defect rather than assuming a configuration error, and building a working replacement
- Debugging XML-based rule and decoder configuration, including schema validation errors and reserved-field conflicts
- Structuring a long, iterative troubleshooting process into clear, professional documentation

## Lessons Learned
- Vendor-provided default rules and decoders are not automatically trustworthy; testing them against their own documented examples is worth doing early.
- A SIEM's default output does not always show the full picture — wazuh-logtest's standard mode silently omitted rule evaluation results that only appeared in verbose mode.
- Integrating two security tools together is considerably more fragile than running them independently, and is worth timeboxing rather than pursued indefinitely against a deadline.
- Host-level networking issues (like an incorrectly assigned VM adapter IP) can look identical to an application-level fault, and are worth ruling out early.
- Documenting an unresolved problem honestly is more valuable, and more professionally credible, than only presenting the parts that worked.

## Outcome
By the end of Week 3, I had a working malware detection and monitoring pipeline: ClamAV detecting the EICAR test signature directly, Squid proxy logs flowing through Syslog-NG into Wazuh in real time, and a custom-built Wazuh decoder and detection rule generating a genuine, verified alert in the dashboard from live proxy traffic — built after finding and working around a real defect in Wazuh's own built-in Squid decoder.

## Documentation
- Full Technical Report 
- Incident Response Plan 
- Custom Wazuh Decoder & Rule
- LinkedIn Post Link: https://www.linkedin.com/posts/duaa-adnan_squid-proxy-clamav-incident-response-ugcPost-7483185568735088641-3kAC/?utm_source=share&utm_medium=member_desktop&rcm=ACoAADZXX2kBVoIL7tdPW2-3qH6yuAHAwQAOOec

## Future Improvements
- Resolve the ICAP integration between Squid and ClamAV for inline file scanning
- Extend Syslog-NG forwarding to include pfSense firewall logs, not just Squid
- Build additional custom rules covering a wider range of Squid response codes and request patterns
- Configure email or webhook alerting for high-severity custom rule matches
- Re-test the built-in Squid decoder against future Wazuh releases to see whether the defect has been fixed upstream

Part of my ongoing journey into Blue Team & SOC operations, building this repository one task at a time.

Duaa Bint E Adnan · SOC Analyst Intern @ ITSimplera Solutions
