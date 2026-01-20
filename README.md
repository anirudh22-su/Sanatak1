# SOP for Disk Management and ulimit Configuration

**Project:** OT Microservices Platform
**Repository:** [https://github.com/OT-MICROSERVICES](https://github.com/OT-MICROSERVICES)
**Author:** Anirudh
**Sprint:** 0

---

## 📑 Table of Contents

1. [Introduction](#1-introduction)
2. [Prerequisites](#2-prerequisites)
3. [Procedure](#3-procedure)

   * [Checking Disk Usage](#31-checking-disk-usage)
   * [Checking Mount Points](#32-checking-mount-points)
   * [Configuring ulimit Settings](#33-configuring-ulimit-settings)
4. [Best Practices](#4-best-practices)
5. [Troubleshooting Tips](#5-troubleshooting-tips)
6. [Contact Information](#6-contact-information)
7. [References](#7-references)

---

## 1. Introduction

This Standard Operating Procedure (SOP) defines the process for **disk management** and **resource limit (ulimit) configuration** on Ubuntu systems used in the **OT Microservices Platform**.

Microservices running in this platform depend on:

* Stable disk availability
* Correct mount point configuration
* Proper process-level resource limits

Incorrect disk or ulimit settings may cause:

* Runtime failures
* File descriptor exhaustion
* Service instability or downtime

This SOP standardizes operational steps to ensure reliability across all OT Microservices nodes.

---

## 2. Prerequisites

Before performing disk or ulimit operations, ensure:

* Ubuntu Linux system hosting OT microservices
* SSH or terminal access to the node
* `sudo` privileges
* Basic understanding of Linux filesystems and services

Verify access:

```bash
whoami
```

---

## 3. Procedure

### 3.1 Checking Disk Usage

Check overall disk usage:

```bash
df -h
```

Displays total size, used space, available space, and mount points.

Check disk usage for application-relevant directories (example: logs, volumes):

```bash
du -sh /var/*
```

Helps identify directories consuming excessive disk space.

List disks and partitions:

```bash
lsblk
```

---

### 3.2 Checking Mount Points

View all mounted filesystems:

```bash
mount | column -t
```

Verify persistent mount configuration:

```bash
cat /etc/fstab
```

Ensures required volumes are mounted automatically after reboot.

Check usage of a specific mount point:

```bash
df -h /mount-point
```

---

### 3.3 Configuring ulimit Settings

Check current ulimit values:

```bash
ulimit -a
```

Set a temporary ulimit value (current session only):

```bash
ulimit -n 65535
```

Configure permanent ulimit settings for the OT Microservices user:

Edit limits configuration:

```bash
sudo vi /etc/security/limits.conf
```

Add:

```bash
anirudh soft nofile 65535
anirudh hard nofile 65535
```

Configure ulimit for systemd-managed microservices:

```bash
sudo systemctl edit <service-name>
```

Add:

```ini
[Service]
LimitNOFILE=65535
```

Reload systemd and restart service:

```bash
sudo systemctl daemon-reexec
sudo systemctl restart <service-name>
```

---

## 4. Best Practices

* Monitor disk usage regularly on microservice nodes
* Maintain consistent mount points across environments (dev, stage, prod)
* Apply ulimit settings before deploying new services
* Avoid unlimited resource values unless explicitly required
* Always restart services after modifying ulimit configurations

---

## 5. Troubleshooting Tips

| Issue                   | Solution                              |
| ----------------------- | ------------------------------------- |
| Disk space full         | `du -sh /*`                           |
| Mount not available     | `mount -a`                            |
| ulimit not applied      | Log out and log in again              |
| Service ignoring limits | Verify systemd override configuration |

---

## 6. Contact Information

**Owner:** Anirudh
**Project:** OT Microservices Platform

---

## 7. References

| Topic              | Link                                                                                                                                     | Description             |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- | ----------------------- |
| Disk Usage         | [https://manpages.ubuntu.com/manpages/jammy/en/man1/df.1.html](https://manpages.ubuntu.com/manpages/jammy/en/man1/df.1.html)             | Disk usage command      |
| Disk Usage (du)    | [https://manpages.ubuntu.com/manpages/jammy/en/man1/du.1.html](https://manpages.ubuntu.com/manpages/jammy/en/man1/du.1.html)             | Directory size analysis |
| ulimit / setrlimit | [https://man7.org/linux/man-pages/man2/setrlimit.2.html](https://man7.org/linux/man-pages/man2/setrlimit.2.html)                         | Linux resource limits   |
| systemd Limits     | [https://www.freedesktop.org/software/systemd/man/systemd.exec.html](https://www.freedesktop.org/software/systemd/man/systemd.exec.html) | Service-level limits    |

---
