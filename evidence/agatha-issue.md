## Student Information

- **Name:** BBULYA AGATHA
- **Registration Number:** 24/BBC/BU/R/0001
- **GitHub Username:** [BbulyaAgatha](https://github.com/BbulyaAgatha)

## Area of Contribution

- [ ] Linux Administration
- [ ] Desktop & Branding
- [ ] Applications
- [x] Documentation
- [x] Testing & Quality Assurance
- [ ] Security
- [ ] DevOps / Build Automation
- [ ] Networking
- [ ] Package Management
- [ ] Research / Innovation
- [ ] Other

## Problem

Bugema Linux has no documented way to verify that a release image is actually good
before it is handed to a student, a member of staff, or a laboratory.

`ROADMAP.md` lists, among the acceptance criteria for **Bugema Linux Alpha 0.1**, the
items *"test ISO in VirtualBox"*, *"ISO checksum"*, and *"known-issues
documentation"*. The acceptance criteria also demand that the release include
"successful boot", "installation", "internet connectivity", "Git", "Python" and
"Java". However, the repository currently contains no document that describes how any
of this is to be *checked*. The only testing-related material in the project is the
bug report issue template, which tells a reporter how to write a bug once it has
already been found.

The consequence is that the checks are done ad hoc, or not at all:

* Nobody checks the SHA256 checksum of a downloaded image, so a truncated or tampered
  download is discovered as a mysterious installation failure rather than as a
  checksum mismatch.
* There is no agreed list of the checks a candidate image must pass, so two people
  testing the same build record different things and the results cannot be compared.
* When something *is* broken, the failure is reported in whatever form the reporter
  happened to use, so the report is missing the information a maintainer needs to
  reproduce it — the image checksum, the machine configuration, the exact command and
  its full output.
* Because there is no reference configuration for the test machine, a failure on a
  2 GB laptop and a failure on a 16 GB workstation are indistinguishable, and it is not
  possible to tell an image defect from a test-environment problem.

## Background

This matters for the project in four ways.

**It is on the roadmap, and the roadmap is the specification.** The Alpha 0.1 milestone
cannot be declared complete on the basis of opinion. A milestone item such as
"internet connectivity" or "Python" is only a fact about the release if somebody
actually checks it, in a way that anyone else can repeat and get the same answer. The
verification procedure is the thing that turns a list of aspirations into a list of
tested facts.

**The project is offline-first (ADR-004).** Bugema Linux is intended for laboratories
and community environments with limited or no connectivity. Such an environment cannot
absorb a broken image easily: if the image's package lists, resolver, or network
configuration are faulty, the machine may be unusable until it is physically
reinstalled. A smoke test that deliberately removes DNS and checks that the system
still works is what gives confidence that the offline-first claim is real.

**Trust and security (ADR-005).** Publishing a checksum alongside a release image, and
making the checksum check a mandatory first step, is what allows a user to know that
the file they downloaded is the file the project published. Without that step, an
image distributed over a shared drive or a USB stick is distributed on trust alone.

**The project teaches.** The Open Source Systems course asks students to contribute to
a real project. A documented verification procedure gives students a concrete,
self-contained piece of quality engineering to perform and to report on, which is
exactly the kind of work that real maintainers do and which a student would
otherwise never see.

## Proposed Solution

Add a single guide, `docs/testing/alpha-0.1-image-verification.md`, that defines how a
Bugema Linux release image is verified and smoke tested.

The guide will cover:

1. **Purpose and scope** — verifying an image before use and after installation, and
   the three ways an image can fail (corrupted transfer, tampered artefact,
   incomplete build).
2. **The expected content of the image** — a table mapping each check to the
   architecture decision it protects (ADR-001 Ubuntu 26.04 LTS, ADR-002 Xfce 4.20,
   ADR-003 APT/`.deb`, ADR-004 offline-first, ADR-005 defence-in-depth), so that a
   failed check points at a real requirement.
3. **Obtaining and verifying the image** — downloading the image together with the
   published `SHA256SUMS` file, verifying it with `sha256sum -c`, and the equivalent
   commands on Windows and macOS; recording the checksum as the build identifier.
4. **Writing the image to removable media** — identifying the target device with
   `lsblk` before writing, and the `dd` command with `conv=fsync` and `sync`.
5. **Booting the image** — the reference test configuration (2 vCPU, 4096 MB RAM,
   40 GB disk, NAT, 64-bit, UEFI) so that results are comparable between machines.
6. **The smoke test** — 24 checks in eight groups: system identity, resources and
   storage, package management, network and DNS, services and logs, the development
   tools required by the milestone, offline behaviour, and the graphical session. Each
   check states the command, the expected result, and what a failure means.
7. **Recording the result** — a standard result table (`PASS` / `FAIL` / `N/A`) plus
   the mandatory build block (image name, SHA256, host, platform, VM configuration,
   date, tester).
8. **Comparing builds** — a regression table for comparing a candidate build against
   the previous one, and the rule that a check changing from `PASS` to `FAIL` is a
   regression.
9. **Documenting a failure** — the known-issue format (severity, build, environment,
   check, expected, actual, reproduction steps, workaround), the rule to quote output
   rather than describe it, and the rule that a failure without the image checksum
   cannot be reproduced.
10. **Reporting a security problem** — pointing to `SECURITY.md` instead of a public
    issue.
11. **Improving the guide** — the standard contribution workflow, so that the document
    itself improves through pull requests.

## Testing

The guide will be tested as follows.

1. **Command verification.** Every command in the guide will be executed on a working
   Linux system and the output compared with the expected result recorded in the
   document, so that no command in the guide is unverified.
2. **Checksum verification.** The `sha256sum -c SHA256SUMS --ignore-missing` procedure
   will be run against a real checksum file, and a deliberately corrupted copy of an
   image will be used to confirm that the check actually *fails* when it should —
   a verification procedure that has never been seen to fail is not a verification
   procedure.
3. **Smoke test rehearsal.** The 24 checks will be executed in order on a live
   system, and the result table will be filled in from the real output, so that the
   expected results in the guide are the results that are actually observed.
4. **Reference configuration.** The test configuration will be checked to be the one
   the project recommends, and to be achievable on the laboratory hardware the
   project targets (2 vCPU, 4 GB RAM, 40 GB disk).
5. **Link and requirement validation.** Every internal link will be confirmed to
   resolve to a file that exists in the repository, and every check in the smoke test
   will be traced to a line in `ROADMAP.md` or to an accepted ADR, so that no check
   tests something the project has not asked for.
6. **Independent rehearsal.** A colleague who has not previously tested a Bugema
   Linux image will follow the guide from download to recorded result, and will report
   any step that was ambiguous or that they could not complete.

## Expected Outcome

* Every Bugema Linux image can be verified — checksum first, then the smoke test —
  before it is released, and the result is recorded in a comparable form.
* A failure is reported with the information needed to reproduce it: the image
  checksum, the machine configuration, the exact command, and its full output.
* A regression between two builds is visible in a side-by-side table instead of being
  discovered by a user.
* The roadmap items "ISO checksum", "known-issues documentation" and "test ISO in
  VirtualBox" acquire a documented, repeatable procedure, and the milestone criteria
  for boot, installation, connectivity and the development tools acquire a way to be
  tested rather than merely asserted.
* The project gains the quality-assurance documentation that a distribution — as
  opposed to a script repository — needs.

## Evidence of Work

* The completed guide, submitted as a pull request against `main`.
* The filled-in result table from the rehearsal of the smoke test, included in the
  pull request description.
* The pull request linked to this issue with `Closes #<issue-number>`.
* The environment used for the rehearsal is documented in the student's Assignment 4
  development environment report.

## Related Bugema Linux Component

* `docs/testing/` — new testing documentation (the directory does not yet exist in the
  repository).
* References: `ROADMAP.md` (Alpha 0.1 acceptance criteria), `SECURITY.md` (private
  disclosure of security problems), `CONTRIBUTING.md` (contribution workflow), and
  ADRs 001 to 005.

## Questions or Support Needed

* Confirmation of the intended behaviour of the checksum verification step: whether the
  project intends to publish a signed checksum file for each release, and whether the
  guide should include the `gpg --verify` step for that case, or keep the guide to
  the plain `sha256sum` check.
* Advice on the location of the document — `docs/testing/` is proposed for
  consistency with the project structure in `README.md`, but `docs/architecture/` or a
  future `docs/user-guide/` may be preferred by the maintainers.

## Student Declaration

I understand that I should not make direct changes to the `main` branch. I will follow the
Bugema Linux contribution workflow and submit my work for review.
