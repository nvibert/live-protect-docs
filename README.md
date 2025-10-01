# Cisco Live Protect with Tetragon on NX-OS

## Overview

Cisco Live Protect integrates [Cilium Tetragon](https://tetragon.io/) runtime security observability and enforcement directly into Cisco NX-OS switches. This provides real-time security monitoring, threat detection, and policy enforcement at the network infrastructure level.

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

### Security Policies

Tetragon policies are located in the phase1 alerts directory:

```bash
# View available policies
ls -la /isan/etc/nxsecure/mandate/phase1/alerts/

# Examine policy files
cat /isan/etc/nxsecure/mandate/phase1/alerts/*.yaml
```
## Monitoring and Logging

### Log Location

Tetragon logs are centrally located:

```bash
# Navigate to log directory
cd /nxos/nxsecure/tetragon_logs

# List available log files
ls -la

# View recent logs
tail -f tetragon.log
```

### Log Analysis

Common log analysis commands:

```bash
# Search for specific events
grep "ALERT" /nxos/nxsecure/tetragon_logs/*.log

# Monitor real-time events
tail -f /nxos/nxsecure/tetragon_logs/tetragon.log | grep "process_exec"

# Check for policy violations
grep -i "violation\|blocked\|denied" /nxos/nxsecure/tetragon_logs/*.log
```


### Getting Help

- Check internal documentation: `/isan/etc/nxsecure/mandate/README.md`
- Monitor Tetragon logs: `tail -f /nxos/nxsecure/tetragon_logs/tetragon.log`

## Resources and Documentation

### Official Cisco Documentation
- [Cisco NX-OS Security Configuration Guide - Live Protect](https://www.cisco.com/c/en/us/td/docs/dcn/nx-os/nexus9000/106x/configuration/security/cisco-nexus-9000-series-nx-os-security-configuration-guide-release-106x/m-secure-nxos-with-cisco-live-protect.html#g-)
- [NX-OS Live Protect Solution Overview](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/nx-os-software/solution-overview-nx-os-live-protect.html)

### News and Announcements
- [Cisco Unveils Secure Network Architecture to Accelerate Workplace AI Transformation](https://newsroom.cisco.com/c/r/newsroom/en/us/a/y2025/m06/cisco-unveils-secure-network-architecture-to-accelerate-workplace-ai-transformation.html)

### Technical Blogs and Partnerships
- [Isovalent + Cisco Nexus: Better Together](https://isovalent.com/blog/post/isovalent-cisco-nexus-better-together/)

### Video Resources

#### Cisco Live Protect Demo Video
[![Cisco Live Protect Demo](https://img.youtube.com/vi/9mKFCtyw_VU/maxresdefault.jpg)](https://youtu.be/9mKFCtyw_VU)

#### Live Demo: Cisco Live Protect in Action
[![Live Demo: Cisco Live Protect in Action](https://img.youtube.com/vi/JDvmlECqYg4/hqdefault.jpg)](https://www.youtube.com/watch?v=JDvmlECqYg4&t=1689)

> 🎥 **Note**: Click the video thumbnails above to watch on YouTube. The second video starts at timestamp 28:09 where the Live Protect demo begins.
