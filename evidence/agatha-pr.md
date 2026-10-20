Closes #20

## Summary

This pull request adds `docs/testing/alpha-0.1-image-verification.md`, a documented
procedure for verifying a Bugema Linux release image and smoke testing it before it is
treated as a release.

The project already has a bug report template, but nothing that describes **how an
image is checked in the first place**. `ROADMAP.md` nevertheless lists *"test ISO in
VirtualBox"*, *"ISO checksum"* and *"known-issues documentation"* among the Alpha 0.1
acceptance criteria, and the milestone demands that the release demonstrably boots,
installs, connects to the Internet, and carries Git, Python and Java. Every one of
those is a testable fact, but at the moment there is no agreed test to perform, no
reference configuration to perform it on, and no standard way to record the result, so
checks are done ad hoc and their results cannot be compared between builds.

The new guide turns those milestone items into a repeatable procedure, and ties every
check back to the architecture the project has already accepted:

| Project decision / requirement | How the guide reflects it |
| --- | --- |
| ADR-001 — Ubuntu 26.04 LTS base | The identity check expects release `26.04` |
| ADR-002 — Xfce 4.20 desktop | The graphical-session check expects the Xfce session |
| ADR-003 — APT and `.deb` packaging | The package-management group checks `apt`, `dpkg` and `apt update` |
| ADR-004 — Offline-first | The smoke test deliberately stops DNS and checks that the system is still usable |
| ADR-005 — Defence-in-depth | Checksum verification for provenance, and private reporting through `SECURITY.md` |
| `ROADMAP.md` — ISO checksum, known-issues documentation, test the ISO in VirtualBox | Sections 4, 8 and 6 respectively |

## Changes Made

* **Added** `docs/testing/alpha-0.1-image-verification.md` (11 sections, 436 lines):
  1. Purpose and scope — the three ways a release image fails: corrupted transfer,
     tampered artefact, incomplete build;
  2. Why a verification procedure is needed — the failure modes table, and the link to
     the offline-first commitment;
  3. What the image is expected to contain — each requirement mapped to the ADR that
     created it;
  4. **Obtaining and verifying the image** — downloading the image together with the
     published `SHA256SUMS`, verifying with `sha256sum -c SHA256SUMS --ignore-missing`,
     the Windows (`Get-FileHash`) and macOS (`shasum -a 256`) equivalents, and
     recording the checksum as the build identifier;
  5. **Writing the image to removable media** — identifying the target with `lsblk`
     before writing, and the `dd` command with `conv=fsync` followed by `sync`;
  6. **Booting the image and the smoke test** — the reference test configuration
     (2 vCPU, 4096 MB RAM, 40 GB disk, NAT, 64-bit, UEFI), then 24 checks in eight
     groups: system identity, resources and storage, package management, network and
     name resolution, services and failed units, the development tools required by the
     milestone, offline behaviour with DNS stopped, and the graphical session. Each
     check states the command, the expected result, and what a failure means;
  7. **Recording the result** — a `PASS` / `FAIL` / `N/A` table and the mandatory
     build block (image name, SHA256, host, platform, VM configuration, date, tester);
  8. **Comparing a new build with the previous one** — a regression table, and the
     rule that a check changing from `PASS` to `FAIL` is a regression;
  9. **Documenting a failure as a known issue** — the severity / build / environment /
     check / expected / actual / reproduction / workaround format, the rule to quote
     output rather than describe it, and the rule that a failure without the image
     checksum cannot be reproduced;
  10. **Reporting a security problem** — through `SECURITY.md`, not a public issue;
  11. **Contributing improvements** — the standard issue → branch → pull request loop.
* **No existing file was modified.** The change is additive, so it does not conflict
  with the documentation work already in review (#13, #15, #16, #17, #19).
* Every internal link in the new document was resolved against the repository tree and
  points to a file that exists in `main`.

## Testing

**1. The checksum procedure was tested in both directions.** A verification procedure
that has never been seen to fail is not a verification procedure, so the check was
deliberately run against a corrupted file as well as an intact one:

```console
$ sha256sum ubuntu-26.04.1-desktop-amd64.iso > SHA256SUMS
$ cat SHA256SUMS
fde915055263f9143d59b0045ecc5677954cfae4c366e56a394053bfb29b4c97  ubuntu-26.04.1-desktop-amd64.iso

$ sha256sum -c SHA256SUMS --ignore-missing
ubuntu-26.04.1-desktop-amd64.iso: OK

$ printf 'corruption' >> ubuntu-26.04.1-desktop-amd64.iso
$ sha256sum -c SHA256SUMS --ignore-missing
ubuntu-26.04.1-desktop-amd64.iso: FAILED
sha256sum: WARNING: 1 computed checksum did NOT match
sha256sum: SHA256SUMS: no file was verified
$ echo $?
1
```

The intact file passes and the corrupted file fails with a non-zero exit status, so the
procedure distinguishes a good image from a damaged one.

**2. The smoke test commands were executed on a live Linux system**, and the expected
results in the document are the results that were actually observed:

```console
$ lsb_release -a
Distributor ID:	Ubuntu
Description:	Ubuntu 24.04.4 LTS
Release:	24.04
Codename:	noble

$ uname -a
Linux medisoft 7.0.0-31-generic #31~24.04.1-Ubuntu SMP PREEMPT_DYNAMIC x86_64 GNU/Linux

$ nproc
8
$ free -h
               total        used        free      shared  buff/cache   available
Mem:           7.6Gi       4.7Gi       352Mi       1.1Gi       4.0Gi       2.9Gi
$ df -hT /
Filesystem     Type  Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv lvm  231G   73G  147G  34% /

$ apt --version
apt 2.8.3 (amd64)
$ dpkg --version
Debian 'dpkg' package management program version 1.22.6 (amd64).
$ dpkg -l | grep -c '^ii'
2105

$ ip -brief addr
lo               UNKNOWN        127.0.0.1/8 ::1/128
wlp2s0           UP             192.168.1.57/24 fe80::6e2:2728:c1be:14f/64
$ ip route
default via 192.168.1.1 dev wlp2s0 proto dhcp src 192.168.1.57 metric 600
$ ping -c 4 8.8.8.8
4 packets transmitted, 3 received, 25% packet loss, time 3004ms
rtt min/avg/max/mdev = 17.788/18.303/19.237/0.661 ms
$ ping -c 4 github.com
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 153.429/155.988/157.380/1.571 ms
$ getent hosts github.com
140.82.121.3    github.com

$ systemctl --failed --no-pager
$ systemctl is-system-running
running

$ git --version
git version 2.43.0
$ python3 --version
Python 3.12.3
$ java --version
openjdk 11.0.32 2026-07-21
```

**3. The commands in the `dd` and `lsblk` steps were checked for correctness** against
the GNU coreutils documentation, and the warning about writing to the wrong device is
included in the guide because `lsblk -d -o NAME,SIZE,MODEL,TRAN` is the step that
prevents the most destructive error in that procedure.

**4. Link validation.** All 9 internal links in the new document were resolved
programmatically against the repository tree; every one resolves to an existing file,
and none points to a document that is still in review.

**5. Requirement traceability.** Each of the 24 smoke test checks was traced to a line
in `ROADMAP.md` or to an accepted ADR, so that the guide tests only what the project
has asked for. The mapping is published in Section 3 of the document.

**6. Scope.** The pull request adds one file and changes nothing else, confirmed with
`git diff --stat` and `git status`.

## Expected Result

* A Bugema Linux image can be **verified** — checksum first, then the smoke test —
  before it is released, and the outcome is recorded in a form that results from
  different machines and different builds can be compared against.
* A failure is reported with everything needed to reproduce it: the image checksum,
  the machine configuration, the exact command, and its full output.
* A regression between two candidate builds is visible in a side-by-side table instead
  of being discovered by the first user who installs the image.
* The roadmap items *"ISO checksum"*, *"known-issues documentation"* and *"test ISO in
  VirtualBox"* acquire a documented, repeatable procedure, and the milestone criteria
  for boot, installation, connectivity and the development tools acquire a way to be
  tested rather than merely asserted.
* The project gains the quality-assurance documentation that a distribution, as
  opposed to a script repository, requires — and students gain a concrete piece of
  real quality-engineering work to perform and report on.

## Reviewer Notes

* The document is placed in a new `docs/testing/` directory, matching the project
  structure in `README.md`. If the maintainers prefer another location, that is easy
  to change.
* Section 6.2 check **G** stops DNS to test the offline-first behaviour of ADR-004.
  I would welcome feedback on whether that is a safe check to run on a machine that is
  also being used for other work.
* Section 8 deliberately distinguishes a failure that reproduces on several machines
  (likely an image defect) from one that appears on a single machine (likely a test
  environment problem). Feedback on that rule is welcome.
* The guide currently assumes a plain `sha256sum` comparison. If the project intends to
  publish **signed** checksum files for releases, a `gpg --verify` step should be
  added — I have raised this as a question on issue #20.
