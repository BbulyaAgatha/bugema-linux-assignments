# ASSIGNMENT — CROSS-VM QA, PEER TESTING, AND PULL REQUEST REVIEW

**Student Name:** BBULYA AGATHA  
**Registration Number:** 24/BBC/BU/R/0001  
**Programme:** Bachelor of Computer Science (BBC)  
**Course:** Open-Source Systems and Linux Development  
**GitHub Username:** [BbulyaAgatha](https://github.com/BbulyaAgatha)  
**Email:** `bbulyaagatha@gmail.com`  
**Assignment:** Week 2 — Quality Assurance and Collaborative Development  
**Deadline:** 2 November 2026  
**Date Submitted:** 2 November 2026  

---

## 1. Practical Deliverables Summary

| Deliverable | Value / Evidence Link |
|---|---|
| **Classmate Contribution Tested** | Sharon Talemwa (`talemwasharon`) — *VirtualBox Installation Guide for Ubuntu Hosts* |
| **Contribution File** | `docs/virtualbox-ubuntu-host.md` |
| **Contribution Issue** | [bos-com/bugema-linux#8](https://github.com/bos-com/bugema-linux/issues/8) |
| **Contribution Pull Request** | [bos-com/bugema-linux#22](https://github.com/bos-com/bugema-linux/pull/22) |
| **Contribution Commit Tested** | [`536fb6d7a52c54ff22b0a6fa6381df06241ca058`](https://github.com/bos-com/bugema-linux/commit/536fb6d7a52c54ff22b0a6fa6381df06241ca058) |
| **QA Test Branch** | `test/issue-8-peer-qa` |
| **QA Peer Review Pull Request** | [bos-com/bugema-linux Pull Request](https://github.com/bos-com/bugema-linux/pulls) |
| **Substantive Review Comment on PR #22** | Submitted on [bos-com/bugema-linux#22](https://github.com/bos-com/bugema-linux/pull/22) |
| **Primary Submission Report** | [`docs/testing/peer-test-report.md`](docs/testing/peer-test-report.md) |
| **Test Environment** | Dell Latitude 7390, Ubuntu 24.04.4 LTS, Kernel Linux 7.0.0-31-generic, 7.6 GiB RAM |

---

## 2. Objective & Scope

The objective of this assignment is to validate a classmate's documentation contribution on an independent Linux host, record reproducible test results with zero fabrication, and provide constructive, substantive peer review via GitHub.

The selected contribution is **talemwasharon's** Pull Request [#22](https://github.com/bos-com/bugema-linux/pull/22) (`docs: add VirtualBox installation guide for Ubuntu hosts`), which provides an end-to-end tutorial for setting up a Bugema Linux development virtual machine on an Ubuntu host.

---

## 3. Test Environment Specifications

- **Operating System:** Ubuntu 24.04.4 LTS (Noble Numbat)
- **Kernel Version:** Linux `7.0.0-31-generic` x86_64 (`#31~24.04.1-Ubuntu SMP PREEMPT_DYNAMIC`)
- **Hardware Platform:** Dell Inc. Latitude 7390 (physical laptop host)
- **CPU:** Intel(R) Core(TM) i5-8350U CPU @ 1.70GHz (4 cores, 8 threads, VT-x hardware virtualization active)
- **Memory (RAM):** 7.6 GiB total (5.0 GiB used, 771 MiB free, 2.7 GiB available), 4.0 GiB swap
- **Storage Subsystem:** 238.5 GB NVMe SSD (`/dev/mapper/ubuntu--vg-ubuntu--lv` 231 GB total, 138 GB available / 38% used)
- **Network Configuration:** Ethernet `enp0s31f6` (UP, `192.168.1.188/24`), Wi-Fi `wlp2s0` (UP, `192.168.1.57/24`), `docker0` (DOWN)
- **Developer Tools:** Git 2.43.0, Python 3.12.3

---

## 4. Summary of Verification & Peer Review Findings

### 4.1 Step-by-Step Guide Verification
All executable terminal commands from Sections 1, 2, 3, 7, 8, 9, 10, 11, and 12 were re-run live and confirmed reproducible on the independent host:
- Host specification checks (`lsb_release`, `lscpu`, `free`, `df`, `uname`) — **PASS**
- Hardware virtualization check (`lscpu | grep -i virtualization`) — **PASS** (`VT-x` confirmed)
- VirtualBox installation and Secure Boot check (`mokutil --sb-state`) — **PASS**
- System identity and permissions (`whoami`, `hostname`, `id`, `uname -a`) — **PASS**
- Network connectivity and DNS resolution (`ip route`, `ping 8.8.8.8`, `ping google.com`) — **PASS**
- Git setup and repository cloning (`git clone`, `git status`) — **PASS**
- Development toolchain checks (`git --version`, `python3 --version`) — **PASS**

### 4.2 Key Findings Submitted in Peer Review
- **Finding 1 (ISO Naming & Availability):** In Section 4, the guide points users to download `ubuntu-26.04.1-desktop-amd64.iso` from `ubuntu.com/download/desktop`. Ubuntu 26.04 LTS is currently in active development, and point release `26.04.1` is not served on the general desktop download page. Recommended providing the direct daily/development build URL or referencing the 24.04 stable release.
- **Finding 2 (MOK Password Clarification):** In Section 3, recommended clarifying that the MOK enrollment password is a temporary single-use password for the immediate reboot, preventing student confusion with root credentials.
- **Finding 3 (Git Email Privacy):** Recommended adding a note in Section 10 about using GitHub's privacy email (`noreply`) to avoid publishing personal email addresses in commit logs.
- **Finding 4 (Architecture Context):** Suggested noting the distinction between VirtualBox (desktop GUI workflows) and KVM/QEMU (headless CI workflows).

### 4.3 Tooling & Automated Checks
- **Bash Health-Check Script:** PR #22 contains documentation only; no shell script is present (`bash -n` is N/A).
- **ShellCheck:** Checked locally; ShellCheck is not installed in the reviewer environment (`which shellcheck`).

---

## 5. Peer Review Record

- **PR Reviewed:** [bos-com/bugema-linux#22](https://github.com/bos-com/bugema-linux/pull/22)
- **Reviewer:** [BbulyaAgatha](https://github.com/BbulyaAgatha)
- **Review Type:** Substantive Review Comment
- **Upstream Pull Request for QA Report:** [bos-com/bugema-linux](https://github.com/bos-com/bugema-linux/pulls)

---

## 6. Full Report Document

The full peer QA test report is maintained in:
- Upstream Pull Request Branch: `test/issue-8-peer-qa`
- Local Path: [`docs/testing/peer-test-report.md`](docs/testing/peer-test-report.md)
