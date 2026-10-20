# ASSIGNMENT 3 — IDENTIFYING, DOCUMENTING AND MANAGING OPEN-SOURCE PROJECT ISSUES

**Student Name:** BBULYA AGATHA
**Registration Number:** 24/BBC/BU/R/0001
**Programme:** Bachelor of Computer Science (BBC)
**Course:** Open-Source Systems and Linux Development
**Lecturer:** ______________________________
**GitHub Username:** [BbulyaAgatha](https://github.com/BbulyaAgatha)
**Date:** 20 October 2026

---

## 1. Assignment 3 Deliverables

| # | Deliverable | Evidence |
| --- | --- | --- |
| 1 | Issue URL | <https://github.com/bos-com/bugema-linux/issues/20> |
| 2 | Issue number | **#20** |
| 3 | Issue type | **Documentation issue** (`[DOCS]`) with a testing component — raised through the repository's *Student Contribution Proposal* template |
| 4 | Screenshot of the issue | See `evidence/` and Section 8 |
| 5 | Problem statement | Section 3.1 |
| 6 | Proposed solution | Section 3.2 |
| 7 | Testing procedure | Section 3.3 |
| 8 | Acceptance criteria | Section 3.4 |
| 9 | Why the issue matters | Section 3.5 and Section 5 |
| 10 | Proposed branch name | `docs/bbulya-agatha-alpha-0.1-image-testing-guide` |

**Issue title:**
`[STUDENT] Contribution Proposal - [DOCS] Alpha 0.1 image verification and smoke test guide - BBULYA AGATHA`

The issue was raised on the upstream project, `bos-com/bugema-linux`, so that the
maintainers and the whole cohort can see the requirement. The implementation that
follows from it is pull request
<https://github.com/bos-com/bugema-linux/pull/21>, which carries `Closes #20`.

---

## 2. Issue Type and Why

**Type selected: Documentation issue (C), with a testing component.**

* It is not a **bug**: no existing component of Bugema Linux fails to work. The
  repository's scripts, configuration and documentation all behave as intended.
* It is not a **feature**: it introduces no new program, no new build target and no
  new capability. It changes nothing about what the project does.
* It is a **documentation issue**: a required document — the procedure for verifying
  and testing a release image — is **missing**, even though the project has already
  committed to needing it in `ROADMAP.md`, and even though the project's own bug
  report template implies that images are tested somewhere.

The issue was raised through the repository's own
`.github/ISSUE_TEMPLATE/student_contribution.md` template, because that template
exists specifically for contributions from the Open Source Systems course. Every
template field is filled in, and the sections the assignment requires — Problem,
Background, Proposed Solution, Testing, Acceptance Criteria — are included inside it.

---

## 3. The Issue

### 3.1 Problem statement

> Bugema Linux has no documented way to verify that a release image is actually good
> before it is handed to a student, a member of staff, or a laboratory.
>
> `ROADMAP.md` lists, among the acceptance criteria for **Bugema Linux Alpha 0.1**,
> the items *"test ISO in VirtualBox"*, *"ISO checksum"*, and *"known-issues
> documentation"*. The acceptance criteria also demand that the release include
> "successful boot", "installation", "internet connectivity", "Git", "Python" and
> "Java". However, the repository currently contains no document that describes how
> any of this is to be *checked*. The only testing-related material in the project is
> the bug report issue template, which tells a reporter how to write a bug once it has
> already been found.
>
> The consequence is that the checks are done ad hoc, or not at all:
>
> * Nobody checks the SHA256 checksum of a downloaded image, so a truncated or tampered
>   download is discovered as a mysterious installation failure rather than as a
>   checksum mismatch.
> * There is no agreed list of the checks a candidate image must pass, so two people
>   testing the same build record different things and the results cannot be compared.
> * When something *is* broken, the failure is reported in whatever form the reporter
>   happened to use, so the report is missing the information a maintainer needs to
>   reproduce it — the image checksum, the machine configuration, the exact command and
>   its full output.
> * Because there is no reference configuration for the test machine, a failure on a
>   2 GB laptop and a failure on a 16 GB workstation are indistinguishable, and it is
>   not possible to tell an image defect from a test-environment problem.

### 3.2 Proposed solution

Add a single guide, `docs/testing/alpha-0.1-image-verification.md`, that defines how a
Bugema Linux release image is verified and smoke tested:

1. **Purpose and scope** — verifying an image before use and after installation, and
   the three ways an image can fail: corrupted transfer, tampered artefact, and
   incomplete build.
2. **The expected content of the image** — a table mapping each check to the
   architecture decision it protects (ADR-001 Ubuntu 26.04 LTS, ADR-002 Xfce 4.20,
   ADR-003 APT/`.deb`, ADR-004 offline-first, ADR-005 defence-in-depth), so that a
   failed check points at a real requirement rather than at a personal preference.
3. **Obtaining and verifying the image** — downloading the image together with the
   published `SHA256SUMS` file, verifying it with `sha256sum -c`, the equivalents on
   Windows and macOS, and recording the checksum as the build identifier.
4. **Writing the image to removable media** — identifying the target device with
   `lsblk` before writing, and the `dd` command with `conv=fsync` followed by `sync`.
5. **Booting the image** — the reference test configuration (2 vCPU, 4096 MB RAM,
   40 GB disk, NAT, 64-bit, UEFI) so that results are comparable between machines.
6. **The smoke test** — 24 checks in eight groups: system identity, resources and
   storage, package management, network and name resolution, services and failed
   units, the development tools required by the milestone, offline behaviour, and the
   graphical session. Each check states the command, the expected result, and what a
   failure means.
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

### 3.3 Testing procedure

1. **Command verification.** Every command in the guide will be executed on a working
   Linux system and the output compared with the expected result recorded in the
   document, so that no command in the guide is unverified.
2. **Checksum verification, in both directions.** The
   `sha256sum -c SHA256SUMS --ignore-missing` procedure will be run against a real
   checksum file, and a deliberately corrupted copy of an image will be used to
   confirm that the check actually *fails* when it should — a verification procedure
   that has never been seen to fail is not a verification procedure.
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

**Result of the testing already carried out:** the checksum procedure was verified in
both directions — an intact file returned `OK`, and the same file after a deliberate
one-byte corruption returned `FAILED` with exit status 1; every smoke test command was
executed and its real output recorded; all 9 internal links resolve; and each of the
24 checks was traced to a roadmap item or an ADR. The full transcript is in the pull
request description and in `evidence/`.

### 3.4 Acceptance criteria

```text
[ ] The three ways a release image can fail are documented.
[ ] The expected content of the image is mapped to the ADR that requires it.
[ ] Obtaining the image and the published checksum file is documented.
[ ] SHA256 verification is documented, including the Windows and macOS equivalents.
[ ] The checksum procedure is tested in both directions - intact and corrupted.
[ ] Writing the image to removable media is documented, with the device-identification step before writing.
[ ] The reference test configuration (CPU, RAM, disk, network, architecture, firmware) is documented.
[ ] The smoke test contains 24 checks in eight groups.
[ ] Each check states its command, its expected result, and what a failure means.
[ ] A standard result table (PASS / FAIL / N/A) is provided.
[ ] The build block that must accompany every result is specified.
[ ] A regression table for comparing two builds is provided.
[ ] The known-issue format is documented, with the rule to quote output and to state the build.
[ ] Security problems are directed to SECURITY.md rather than to a public issue.
[ ] Every internal link resolves to a file that exists in the main branch.
[ ] Every check is traceable to a line in ROADMAP.md or to an accepted ADR.
[ ] Another student can verify an image and record a result using the guide alone.
```

### 3.5 Why the issue matters

* **The roadmap is the specification, and it is currently untestable.** The Alpha 0.1
  milestone cannot be declared complete on the basis of opinion. An item such as
  "internet connectivity" or "Python" is only a fact about the release if somebody
  actually checks it, in a way that anyone else can repeat and get the same answer.
* **The project is offline-first (ADR-004).** Bugema Linux is intended for
  laboratories and community environments with limited or no connectivity. Such an
  environment cannot absorb a broken image easily: if the package lists, the resolver
  or the network configuration are faulty, the machine may be unusable until it is
  physically reinstalled. A smoke test that deliberately removes DNS and checks that
  the system still works is what gives confidence that the offline-first claim is
  real.
* **Trust and security (ADR-005).** Publishing a checksum alongside a release image,
  and making the checksum check a mandatory first step, is what allows a user to know
  that the file they downloaded is the file the project published. Without it, an
  image distributed over a shared drive or a USB stick is distributed on trust alone.
* **The project teaches.** The Open Source Systems course asks students to contribute
  to a real project. A documented verification procedure gives students a concrete,
  self-contained piece of quality engineering to perform and to report on, which is
  exactly the kind of work real maintainers do and which a student would otherwise
  never see.
* **It closes a gap that the project itself has already noticed.** The existence of a
  bug report template, and of roadmap items about checksums and known issues, shows
  that the project intends to test its releases. What is missing is the procedure
  that connects those intentions to the work.

---

## 4. Connecting the Issue to Development

```text
Issue #20
   ↓
Requirement: a documented, repeatable way to verify and smoke test a release image
   ↓
Branch: docs/bbulya-agatha-alpha-0.1-image-testing-guide
   ↓
Implementation: docs/testing/alpha-0.1-image-verification.md (436 lines)
   ↓
Testing: checksum procedure verified in both directions, 24 smoke test commands
          executed, 9 internal links resolved, every check traced to a requirement
   ↓
Pull Request: https://github.com/bos-com/bugema-linux/pull/21  (contains "Closes #20")
   ↓
Review: peer review requested from @Daniellakyusa and @kmuwanga83
   ↓
Maintainer review and merge: pending
```

GitHub links the pull request to the issue automatically because the description
contains `Closes #20`; merging the pull request will close issue #20.

---

## 5. Reflection

**1. Why is issue management important in software development?**

An issue is a written record of a requirement, and writing one forces the requirement
to be examined before any work is done. Describing a problem clearly separates what
you *observed* from what you *assume* — while writing the problem statement for this
issue, I realised that the project's roadmap already contained the requirement
("test ISO in VirtualBox", "ISO checksum", "known-issues documentation") and that
nobody had yet turned it into something testable, which is a stronger argument than
the one I had started with. An issue also makes the work visible: it is where the
reasoning is recorded, where an approach can be corrected before effort is wasted on
it, and where several people can contribute to one requirement without duplicating
each other. Most importantly, an issue gives a change an identity. When the
requirement, the branch, the commit, the pull request and the test evidence all refer
back to the same number, the history of a project stays explainable — which is what
makes it possible for a project whose contributors are students who leave after their
courses to survive.

**2. What makes my issue a genuine project requirement?**

Four things. First, it is grounded in the repository itself: the requirement is
written in `ROADMAP.md` and is not met anywhere in the project, so this is not my
preference but the project's own stated need. Second, the problem has visible
consequences — a checksum that nobody verifies, a checklist that does not exist, and
failure reports that omit the information needed to reproduce them. Third, it is
consistent with the architecture the project has accepted: the offline-first
commitment and the defence-in-depth model both depend on a release image being
verifiable and testable, and neither can be demonstrated without this procedure.
Fourth, it is a requirement that other people have already encountered — the same
verification work would otherwise be repeated by every student and every staff member
who is asked to test an image. It is also a requirement that is already in the
project's language: the milestone criteria, the ADR titles, and the roadmap wording
all point at exactly this work.

**3. How would a developer implement my issue?**

In five steps. First, read the roadmap and the accepted ADRs so that the procedure
tests what the project has actually committed to, and not what the author believes
should have been committed to. Second, work backwards from the milestone criteria:
list every claim the release makes — it boots, it installs, it has connectivity, it
has Git, Python and Java, its checksum is published — and turn each one into a check
with a command and an expected result, so that nothing in the specification is left
untested. Third, fix the variables that would otherwise make results incomparable:
one reference machine configuration, one result table, one mandatory build block that
identifies the image by checksum. Fourth, write the failure format, so that a problem
is reported in a form that a maintainer can act on — severity, build, environment,
the exact command, the full output, and the steps to reproduce — and route security
findings to `SECURITY.md` rather than to a public issue. Fifth, validate the document
the way it asks to be validated: run every command, run the checksum check against a
deliberately corrupted file so that the failure path is proven, resolve every link,
and trace every check back to the requirement it protects.

**4. How would I test the solution?**

By using it, and by trying to break it. Concretely: I would verify that the checksum
step passes on an intact file and fails on a corrupted one, because the failure path
is the part that matters; I would run the 24 checks in order on a live system and
fill the result table from the real output, so that the expected values in the guide
are values that were observed rather than values that were assumed; I would repeat the
whole sequence on a second machine with different resources to confirm that the
reference configuration is achievable and that the results are comparable; and I would
give the guide to a colleague who has never tested an image and record every point at
which they hesitate, since those are precisely the places where the document is
ambiguous. I would also check the document against the two things that most often
make a test procedure worthless: that it does not depend on a tool the image does not
contain, and that its expected results match what the project has actually decided
rather than what the author assumed.

**5. What would constitute successful completion?**

The issue is complete when `docs/testing/alpha-0.1-image-verification.md` exists in the
`main` branch of `bos-com/bugema-linux` and every item on the acceptance-criteria
list in Section 3.4 is satisfied: the failure modes documented, the expected content
mapped to the ADRs, the download and checksum verification documented for all three
platforms, the checksum procedure proven in both directions, the removable-media
procedure documented with the device-identification step, the reference configuration
specified, the 24 checks present with their expected results, the result table and the
mandatory build block, the regression table, the known-issue format, the security
reporting route, all internal links resolving, and every check traceable to a
roadmap item or an ADR. On top of that, the document must have been followed
end-to-end by somebody other than its author — a colleague who downloaded an image,
verified it, installed it, ran the smoke test and recorded a result using only the
guide — and the pull request must have been reviewed and merged by a maintainer. The
practical test of success is simple: a student should be able to answer the question
"is this image good enough to give to the laboratory?" using this one document, and
should be able to record the answer in a form that another student can check.
