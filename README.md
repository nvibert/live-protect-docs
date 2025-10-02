# Cisco Live Protect with Tetragon on NX-OS

## Overview

Cisco Live Protect integrates [Cilium Tetragon](https://tetragon.io/) runtime security observability and enforcement directly into Cisco NX-OS switches. This provides real-time security monitoring, threat detection, and policy enforcement at the network infrastructure level.

## Current Implementation Status

### Phase 1 QR1F Release - Observability Only

Live Protect in its current Phase 1 implementation focuses on **security observability** rather than enforcement. The system monitors and logs security events but does not block or prevent activities.

#### Available Monitoring Policies

The current Phase 1 QR1F release includes three primary monitoring policies:

1. **Process Execution Monitoring** - Monitor all process executions by user admin
2. **Privileged Execution Detection** - Monitor privileged process execution by admin user (escalations to root)
3. **VSH Command Monitoring** - Monitor all Virtual Shell (VSH) executions by non-root users

#### Policy Management

- **Location**: Policies are stored in `/isan/etc/nxsecure/mandate/phase1/`
- **Format**: YAML-based policy definitions
- **Mandate File**: `phase1-mandate.yaml` contains the current active policy set
- **No Tracing Policies**: Phase 1 does not include tracing policies

#### Future Roadmap

**Core Security Capabilities:**
- **Policy Enforcement**: Future releases will include policy enforcement capabilities (blocking/preventing actions)
- **Extended Monitoring Policies**: Additional security policies and monitoring capabilities beyond the current Phase 1 set
- **Advanced Threat Detection**: Enhanced vulnerability detection and exploit identification capabilities

**Centralized Management Platform:**
- **Central Nexus Dashboard**: Network-wide overview of device status, health, and security posture
- **Automated CVE Management**: Device scanning for vulnerabilities with escalation and review workflows
- **Compensating Controls**: Automated deployment of security controls when vulnerabilities are identified
- **Targeted Policy Deployment**: Granular control for pushing policies to specific workloads and devices
- **Comprehensive Analytics**: Performance monitoring, alert investigation, and detailed exploit log analysis

## Prerequisites

### Platform Requirements

- **NX-OS Version**: Cisco NX-OS Release 10.6(1)F or later
- **Supported Hardware**: 
  - Cisco Nexus 9300-FX, -FX2, -FX3, -GX, -GX2, -H1, and -H2R switches
  - Minimum 24GB RAM required
- **Unsupported Hardware**: 
  - SiliconOne switches (including Nexus 9800 and N9324C-SE1U)

### Software Requirements

- Administrative access to the switch
- Basic understanding of Linux containers and eBPF

### Feature Enablement

Before using Live Protect, you must enable the `nxsecure` feature:

```bash
# From NX-OS CLI
switch# configure terminal
switch(config)# feature nxsecure
switch(config)# exit
```

> ⚠️ **Important**: The `nxsecure` feature must be enabled before Live Protect functionality becomes available. See the [official documentation](https://www.cisco.com/c/en/us/td/docs/dcn/nx-os/nexus9000/106x/configuration/security/cisco-nexus-9000-series-nx-os-security-configuration-guide-release-106x/m-secure-nxos-with-cisco-live-protect.html#g-) for complete setup instructions.

## Getting Started

### 1. Enable Bash Shell Access

First, enable the bash shell feature on your NX-OS switch:

```bash
# From NX-OS CLI
switch# configure terminal
switch(config)# feature bash-shell
switch(config)# exit
```

### 2. Access the Linux Environment

Switch to the underlying Linux environment:

```bash
# From NX-OS terminal
switch# run bash sudo su -
```

You now have root access to the underlying Linux system running the switch.

### 3. Verify System Information

Check the Linux kernel version and system details:

```bash
# Check kernel version and architecture
uname -ar

# Verify system resources
free -h
df -h
```

## Tetragon Container Management

### Check Running Containers

Verify that Tetragon containers are running:

```bash
# List all running containers
docker ps

# Check Tetragon-specific containers
docker ps | grep tetragon
```


## Configuration and Policies

### Internal Documentation

Access the built-in documentation and configuration:

```bash
# Read the internal README
cat /isan/etc/nxsecure/mandate/README.md

# Check configuration files
ls -la /isan/etc/nxsecure/mandate/
```

### Current Policy Set (Phase 1 QR1F)

Live Protect currently ships with three monitoring policies located in the phase1 alerts directory:

```bash
# View available policies
ls -la /isan/etc/nxsecure/mandate/phase1/alerts/

# Examine policy files
cat /isan/etc/nxsecure/mandate/phase1/alerts/*.yaml

# View the current mandate file
cat /isan/etc/nxsecure/mandate/phase1-mandate.yaml
```

#### Active Monitoring Policies:

1. **Admin Process Execution** - Tracks all process executions by the admin user
2. **Privilege Escalation Detection** - Monitors when admin user processes gain root privileges  
3. **Non-Root VSH Access** - Logs Virtual Shell command execution by non-root users


## Monitoring and Logging

### Log Location

Tetragon logs are centrally located:

```bash
# Navigate to log directory
cd /nxos/nxsecure/tetragon_logs

# List available log files
ls -la

```

### Log Analysis

Common log analysis commands:

```bash
# Search for specific events
grep "ALERT" /nxos/nxsecure/tetragon_logs/*.log

```

### Sample Log Files

This repository includes three sample Tetragon log files demonstrating different types of events that Live Protect captures:

#### 1. Standard Tetragon Log (`sample-logs/tetragon-log-sample.json`)
- **Description**: Basic process execution event showing standard system activity
- **Event Type**: `process_exec` 
- **Details**: Shows a `cat` command execution with full process context including PID, UID, capabilities, and parent process information

#### 2. Privileged Execution Alert (`sample-logs/privilege-execution-by-admin.json`)
- **Description**: Security alert triggered by privileged command execution by an administrator
- **Event Type**: `process_exec` with security context
- **Details**: Demonstrates how Live Protect tracks administrative actions and potential privilege escalation scenarios

#### 3. VSH Execution by Non-Root User (`sample-logs/vsh-exec-non-root.json`)
- **Description**: NX-OS Virtual Shell (VSH) command execution by a non-root user
- **Event Type**: `process_exec` for `/isan/bin/vsh_perm`
- **Details**: Shows how Live Protect monitors NX-OS specific binaries and user interactions with the switch CLI

These sample logs help you understand the structure and types of events that Tetragon captures on Cisco NX-OS switches.


### Getting Help

- Check internal documentation: `/isan/etc/nxsecure/mandate/README.md`
- Monitor Tetragon logs: `tail -f /nxos/nxsecure/tetragon_logs/tetragon.log`

## Resources and Documentation

### Official Cisco Documentation
- [Cisco NX-OS Security Configuration Guide - Live Protect](https://www.cisco.com/c/en/us/td/docs/dcn/nx-os/nexus9000/106x/configuration/security/cisco-nexus-9000-series-nx-os-security-configuration-guide-release-106x/m-secure-nxos-with-cisco-live-protect.html#g-)
- [NX-OS Live Protect Solution Overview](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/nx-os-software/solution-overview-nx-os-live-protect.html)
- [Cisco Nexus 9000 NX-OS Release Notes 10.6(1)F](https://www.cisco.com/c/en/us/td/docs/dcn/nx-os/nexus9000/106x/release-notes/cisco-nexus-9000-nxos-release-notes-1061F.html)

### News and Announcements
- [Cisco Unveils Secure Network Architecture to Accelerate Workplace AI Transformation](https://newsroom.cisco.com/c/r/newsroom/en/us/a/y2025/m06/cisco-unveils-secure-network-architecture-to-accelerate-workplace-ai-transformation.html)

### Technical Blogs and Partnerships
- [Isovalent + Cisco Nexus: Better Together](https://isovalent.com/blog/post/isovalent-cisco-nexus-better-together/)

### Tetragon Resources
- [Cilium Tetragon](https://tetragon.io/) - Official Tetragon project website
- [Tetragon Getting Started Lab](https://isovalent.com/labs/tetragon-getting-started/) - Hands-on introduction to Tetragon
- [Tetragon Security Visibility Lab](https://isovalent.com/labs/tetragon-security-visibility/) - Deep dive into security observability features
- [Tetragon with Splunk Integration Lab](https://isovalent.com/labs/tetragon-splunk/) - Learn how to integrate Tetragon with Splunk for security analytics

### Video Resources

#### Cisco Live Protect Demo Video
[![Cisco Live Protect Demo](https://img.youtube.com/vi/9mKFCtyw_VU/maxresdefault.jpg)](https://youtu.be/9mKFCtyw_VU)

#### Live Demo: Cisco Live Protect in Action
[![Live Demo: Cisco Live Protect in Action](https://img.youtube.com/vi/JDvmlECqYg4/hqdefault.jpg)](https://www.youtube.com/watch?v=JDvmlECqYg4&t=1689)

> 🎥 **Note**: In this eCHO - eBPF and Cilium Office hours episode, Dan and I talked for the first half hour or so about systems where we could be running Tetragon - including NAS storage, Raspberry Pis, etc. - before showing a 25-minute demo of Live Protect on a virtual NX-OS device. The demo starts at timestamp 28:09.
