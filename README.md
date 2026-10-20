# Bugema Linux Open-Source Project — Assignments 2, 3 and 4

**Student:** BBULYA AGATHA
**Registration Number:** 24/BBC/BU/R/0001
**Programme:** Bachelor of Computer Science (BBC)
**Course:** Open-Source Systems and Linux Development
**GitHub:** [BbulyaAgatha](https://github.com/BbulyaAgatha)

The written deliverables for the three connected practical assignments on the
[Bugema Linux](https://github.com/bos-com/bugema-linux) project.

---

## Quick links to the GitHub evidence

| Item | Link |
| --- | --- |
| Fork used for the contribution | <https://github.com/BbulyaAgatha/bugema-linux> |
| Issue — Assignment 3 | <https://github.com/bos-com/bugema-linux/issues/20> |
| Pull Request — Assignment 2 | <https://github.com/bos-com/bugema-linux/pull/21> |
| Contribution branch | <https://github.com/BbulyaAgatha/bugema-linux/tree/docs/bbulya-agatha-alpha-0.1-image-testing-guide> |
| Commit `bc7332d` | <https://github.com/BbulyaAgatha/bugema-linux/commit/bc7332db4b4d8f4982d4bc2e87443dbd2ef34fe8> |
| Peer review on PR #15 | <https://github.com/bos-com/bugema-linux/pull/15#pullrequestreview-5589587612> |

---

## Contents

| File | Contents |
| --- | --- |
| [`ASSIGNMENT-2-GIT-WORKFLOW.md`](ASSIGNMENT-2-GIT-WORKFLOW.md) | Assignment 2: deliverables, the contribution, every Git command used, the pull request content, the peer review evidence, the response to review, the final status, and the reflection answering all six reflection questions |
| [`ASSIGNMENT-3-ISSUE-MANAGEMENT.md`](ASSIGNMENT-3-ISSUE-MANAGEMENT.md) | Assignment 3: issue type and justification, problem statement, proposed solution, testing procedure, acceptance criteria, why it matters, the link from issue to pull request, and the reflection answering all five reflection questions |
| [`ASSIGNMENT-4-DEV-ENVIRONMENT-REPORT.md`](ASSIGNMENT-4-DEV-ENVIRONMENT-REPORT.md) | Assignment 4: the full development environment report in the seventeen sections required by the handout, with the verification results, the troubleshooting table, and the appendices |
| [`docs/alpha-0.1-image-verification.md`](docs/alpha-0.1-image-verification.md) | The contribution itself, as submitted in pull request #21 |
| [`evidence/`](evidence/) | The verification transcript and the exact text submitted to GitHub (issue body, pull request body, the peer review, the pull request comment) |
| [`scripts/`](scripts/) | The environment build script, the evidence collection scripts, the parallel downloader, and the console screenshot tool |

---

## The contribution in one paragraph

`docs/testing/alpha-0.1-image-verification.md` adds the procedure the project is
missing for deciding whether a release image is fit to be used: it covers obtaining
the image and proving it is the file the project published with a SHA256 comparison
against the published `SHA256SUMS` file, writing it to removable media safely,
booting it in a virtual machine on a fixed reference configuration, and then running a
**24-check smoke test** in eight groups — identity, resources, package management,
network and DNS, services, the development tools the milestone requires, offline
behaviour, and the graphical session — with a standard result table, a build block
that identifies the image by checksum, a regression table for comparing two builds,
and a known-issue format that makes a failure reproducible. Each check is traced to
the requirement it protects: ADR-001 to ADR-005 and the Alpha 0.1 criteria in
`ROADMAP.md`, including *"ISO checksum"*, *"known-issues documentation"* and
*"test ISO in VirtualBox"*.

---

## Note on the remaining manual work

The virtual machine could not be booted within the time available, because the network
link on the host delivered between 0.3 KB/s and 0.9 MB/s for the duration of the
assignment and the Ubuntu 26.04.1 desktop image alone is 6.5 GB. The virtualisation
platform (QEMU 8.2.2 with KVM acceleration) was installed and verified, the virtual
machine is fully specified with the exact creation and boot commands, and every
verification command that could be run on a real Linux system was run and its real
output is recorded in `evidence/host-verification.txt`. Section 13 of the Assignment 4
report lists every problem encountered together with its cause, its solution and its
result, including the unresolved download; Section 14.3 lists the exact remaining
steps to execute manually, and the scripts in `scripts/` perform them unattended. No
output in any document was invented — where something was not executed, the report
says so explicitly.
