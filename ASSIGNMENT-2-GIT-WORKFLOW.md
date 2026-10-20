# ASSIGNMENT 2 — COLLABORATIVE GIT DEVELOPMENT USING BRANCHES AND PULL REQUESTS

**Student Name:** BBULYA AGATHA
**Registration Number:** 24/BBC/BU/R/0001
**Programme:** Bachelor of Computer Science (BBC)
**Course:** Open-Source Systems and Linux Development
**Lecturer:** ______________________________
**GitHub Username:** [BbulyaAgatha](https://github.com/BbulyaAgatha)
**Date:** 20 October 2026

---

## 1. Assignment 2 Deliverables

| # | Deliverable | Evidence |
| --- | --- | --- |
| 1 | GitHub username | `BbulyaAgatha` — <https://github.com/BbulyaAgatha> |
| 2 | Branch name | `docs/bbulya-agatha-alpha-0.1-image-testing-guide` |
| 3 | Contribution title | `docs: add Alpha 0.1 image verification and smoke test guide` |
| 4 | Commit hash | `bc7332db4b4d8f4982d4bc2e87443dbd2ef34fe8` (short: `bc7332d`) |
| 5 | Commit message | `docs: add Alpha 0.1 image verification and smoke test guide` |
| 6 | Pull Request URL | <https://github.com/bos-com/bugema-linux/pull/21> |
| 7 | Screenshot of the branch | See `evidence/` and Section 6 |
| 8 | Screenshot of the commit | See `evidence/` and Section 6 |
| 9 | Screenshot of the Pull Request | See `evidence/` and Section 6 |
| 10 | Peer review evidence | One detailed review on another student's PR — Section 5 |
| 11 | Evidence of response to review comments | Section 5.3 |
| 12 | Evidence of final approval / merge | Section 7 (open, awaiting maintainer review) |
| 13 | Short reflection | Section 8 |

**Fork:** <https://github.com/BbulyaAgatha/bugema-linux>
**Upstream (original):** <https://github.com/bos-com/bugema-linux>
**Linked issue:** <https://github.com/bos-com/bugema-linux/issues/20>

---

## 2. The Contribution

**Title:** `docs: add Alpha 0.1 image verification and smoke test guide`
**File added:** `docs/testing/alpha-0.1-image-verification.md` (436 lines)

### 2.1 The problem it solves

`ROADMAP.md` commits the project to, among the acceptance criteria for **Bugema Linux
Alpha 0.1**:

* *"test ISO in VirtualBox"*,
* *"ISO checksum"*,
* *"known-issues documentation"*,
* *"successful boot"*, *"installation"*, *"internet connectivity"*,
* *"Git"*, *"Python"*, *"Java"*,
* *"basic system documentation"*.

Every one of those is a testable fact about a release. Yet the repository contained
nothing that described **how any of it is to be checked**. The only testing material
in the project was the bug report issue template, which explains how to report a
problem *after* it has been found — but nothing says what should be tested, on what
machine, or in what form the result should be recorded.

The consequences of that gap are practical:

* Nobody checks the SHA256 checksum of a downloaded image, so a truncated or tampered
  download appears later as a mysterious installation failure instead of as a
  checksum mismatch.
* There is no agreed list of checks, so two people testing the same build record
  different things and the results cannot be compared.
* When something *is* broken, the report usually lacks the information a maintainer
  needs: the image checksum, the machine configuration, the exact command and its full
  output.
* Without a reference configuration, a failure on a 2 GB laptop and a failure on a
  16 GB workstation are indistinguishable — so it is not possible to tell an image
  defect from a test-environment problem.

### 2.2 What the guide contains

| Section | Content |
| --- | --- |
| 1 | Purpose and scope — the three ways a release image fails |
| 2 | Why a verification procedure is needed, and the link to the offline-first commitment |
| 3 | What the image must contain, each requirement mapped to the ADR that created it |
| 4 | Obtaining the image and verifying it against the published `SHA256SUMS`; Windows and macOS equivalents |
| 5 | Writing the image to removable media — identifying the device first, `dd` with `conv=fsync`, `sync` |
| 6 | Booting the image on the reference configuration, and the **24-check smoke test** in eight groups |
| 7 | Recording the result — a `PASS`/`FAIL`/`N/A` table and the mandatory build block |
| 8 | Comparing a new build with the previous one — the regression table |
| 9 | Documenting a failure as a known issue — the severity / build / environment / expected / actual / reproduction / workaround format |
| 10 | Reporting a security problem through `SECURITY.md` rather than a public issue |
| 11 | Contributing improvements to the guide |

The 24 checks are grouped as: **A** system identity, **B** resources and storage,
**C** package management, **D** network and name resolution, **E** services and failed
units, **F** the development tools required by the milestone, **G** offline behaviour
with DNS deliberately stopped, and **H** the graphical session. Each check states the
command, the expected result, and what a failure actually means.

Every check is traceable to a requirement the project has already accepted:

| Check group | Requirement it protects |
| --- | --- |
| A — identity | ADR-001, Ubuntu 26.04 LTS base distribution |
| H — graphical session | ADR-002, Xfce 4.20 desktop |
| C — package management | ADR-003, APT and `.deb` packaging |
| G — offline behaviour | ADR-004, offline-first architecture |
| D + checksum verification | ADR-005, defence-in-depth security |
| Whole procedure | `ROADMAP.md` — ISO checksum, known-issues documentation, test the ISO in VirtualBox |

---

## 3. Commands Used

```console
# 1. Fork and clone
$ gh repo fork bos-com/bugema-linux --clone=false
$ git clone https://github.com/BbulyaAgatha/bugema-linux.git
$ cd bugema-linux
$ git remote add upstream https://github.com/bos-com/bugema-linux.git
$ git fetch upstream
$ git status

# 2. Configure the Git identity (the same email as the GitHub account)
$ git config --global user.name "BBULYA AGATHA"
$ git config --global user.email "bbulyaagatha@gmail.com"
$ git config --global --list

# 3. Create a working branch from upstream/main (never from main directly)
$ git checkout -b docs/bbulya-agatha-alpha-0.1-image-testing-guide upstream/main

# 4. Make the change
$ mkdir -p docs/testing
$ $EDITOR docs/testing/alpha-0.1-image-verification.md

# 5. Inspect before committing
$ git status
$ git diff
$ git diff --cached --stat

# 6. Commit with a meaningful message
$ git add docs/testing/alpha-0.1-image-verification.md
$ git commit -m "docs: add Alpha 0.1 image verification and smoke test guide" -m "..."

# 7. Verify the commit
$ git log --oneline -5

# 8. Push the branch to the fork
$ git push -u origin docs/bbulya-agatha-alpha-0.1-image-testing-guide

# 9. Create the pull request (fork -> upstream, base: main)
$ gh pr create --repo bos-com/bugema-linux \
    --head BbulyaAgatha:docs/bbulya-agatha-alpha-0.1-image-testing-guide \
    --base main \
    --title "docs: add Alpha 0.1 image verification and smoke test guide" \
    --body-file pr-body.md

# 10. Review another student's pull request
$ gh pr review 15 --repo bos-com/bugema-linux --comment --body-file review-15.md

# 11. Respond to review comments by revising the same branch
$ git add .
$ git commit -m "docs: clarify smoke test result recording"
$ git push
```

**Branch naming.** `docs/bbulya-agatha-alpha-0.1-image-testing-guide` follows the
conventions in `CONTRIBUTING.md` section 5: the `docs/` prefix for documentation work,
a short descriptive suffix, and the purpose identifiable from the name alone.

**Commit message.** `docs: add Alpha 0.1 image verification and smoke test guide` uses
the prefix vocabulary from `CONTRIBUTING.md` section 6 and states exactly what was
done. The body enumerates the eight areas the guide covers and the roadmap items it
addresses. The commit is authored as `BBULYA AGATHA <bbulyaagatha@gmail.com>`, so
GitHub links it to the `BbulyaAgatha` profile.

---

## 4. Pull Request Content

Pull request #21 (<https://github.com/bos-com/bugema-linux/pull/21>) contains the four
required sections:

* **## Summary** — what the pull request adds, the gap it fills, and the table mapping
  each check to the project decision it protects.
* **## Changes Made** — the eleven sections of the guide, the requirement-mapping
  table, the confirmation that no existing file was modified, and the link validation
  result.
* **## Testing** — the checksum procedure tested in **both** directions (intact file →
  `OK`, corrupted file → `FAILED` with exit status 1), the full smoke-test transcript
  from a live Linux system, the `dd`/`lsblk` command check, the link validation, the
  requirement traceability check, and the scope check.
* **## Expected Result** — what the project, its contributors and the milestone gain.

The description also carries `Closes #20`, and a **Reviewer Notes** section inviting
feedback on four specific points, including the offline test and the placement of the
document.

---

## 5. Peer Review Evidence

I reviewed pull request #15, *"ci: add GitHub Actions workflow for ShellCheck and
script validation (closes #15)"* by **befrey**.

**URL:** <https://github.com/bos-com/bugema-linux/pull/15#pullrequestreview-5589587612>

| Criterion | Assessment |
| --- | --- |
| **Branch name** | `feat/shellcheck-ci` — clear, descriptive, consistent with `CONTRIBUTING.md` section 5. A good choice of prefix, since the change is automation rather than documentation. |
| **Quality of the contribution** | The workflow is well structured: the three stages are separated into their own steps and each prints an explanatory line, so the Actions log reads as a test report. Two substantive weaknesses were identified — the workflow currently checks **no files at all** (the repository has no `*.sh` scripts), so it reports success while proving nothing, and the smoke-test step can fail the build for an unrelated reason because it executes any script that merely *mentions* `--help`. |
| **Documentation** | `docs/administration/ci-script-linting.md` is committed as a **0-byte file** (`index 0000000..e69de29`, the empty blob), so the documentation the description promises is not in the change. The usual cause — staging the file before writing it — was explained, with the fix. |
| **Commit message** | Four commits: `j` (no information at all), "Enhance Code of Conduct with communication guidelines" immediately undone by "Delete respectful communication guidelines", and a final commit whose message is good. Neither of the first two messages matches the final diff, so the history is misleading; squashing was recommended. |
| **Testing** | The description states "Executed `bash -n` syntax check and `shellcheck` locally in VS Code terminal". That is a statement rather than evidence and cannot be repeated by a reviewer; an example of the reproducible form to use was given instead. |
| **PR description** | Has Summary, Changes Made and Testing & Evidence, but is missing the **Expected Result** section, and the Testing section needs reproducible output. |
| **Project relevance** | High — the roadmap commits the project to automated testing and reproducible builds, and linting the scripts that will build the image is the first step towards that. The review explicitly said so, so that the author knows the direction is right. |
| **Scope** | The `CODE_OF_CONDUCT.md` change (removal of a `---` separator) is unrelated to CI and should be a separate pull request, since `CONTRIBUTING.md` section 7 asks for that. |

**Additional points raised**, each with the reasoning and a concrete alternative: the
workflow should declare `permissions: contents: read` and pin `actions/checkout` to a
commit SHA, in line with ADR-005; the `find` expressions should be scoped to the
directories the project owns; the smoke-test loop should be whitespace-safe
(`find -print0` with `while read -r`) and should assert on its result; and the
linter itself should be tested against a deliberately broken script, because a check
that has never been seen to fail is not a check.

### 5.3 Responding to review comments

The response workflow was demonstrated on my own pull request before it was submitted.
A **link validation** of the new document found that one reference pointed to
`docs/development/development-environment-setup.md` — a document belonging to a pull
request that is still in review and is therefore not present in `main`. The reference
was replaced with a pointer to the contribution workflow in `CONTRIBUTING.md`, and the
document was re-validated:

```console
$ git add docs/testing/alpha-0.1-image-verification.md
$ git commit --amend --no-edit
$ git push -f origin docs/bbulya-agatha-alpha-0.1-image-testing-guide
```

After the correction, all 9 internal links resolve and none points to a document that
is still in review.

Reviewers have been requested on pull request #21 (`@Daniellakyusa`, `@kmuwanga83`),
and the pull request comment asks for feedback on four specific points: the safety of
the offline test, the rule for distinguishing an image defect from an environment
problem, the placement of the document, and whether signed checksums should be added.
When comments arrive, each one will be answered in the pull request, and any required
change will be committed to the same branch with a meaningful message and pushed, so
that it appears automatically in the pull request:

```console
$ git add .
$ git commit -m "docs: clarify smoke test result recording"
$ git push
```

---

## 6. Screenshot Checklist

These figures must be captured from the GitHub interface and the terminal, numbered in
the order below, and referenced in the report.

| Figure | What it shows | Where to capture it |
| --- | --- | --- |
| Figure 1 | The fork `BbulyaAgatha/bugema-linux` and its branches | <https://github.com/BbulyaAgatha/bugema-linux/branches> |
| Figure 2 | The branch `docs/bbulya-agatha-alpha-0.1-image-testing-guide` | <https://github.com/BbulyaAgatha/bugema-linux/branches/all> |
| Figure 3 | `git status` and `git branch` in the terminal | Terminal |
| Figure 4 | `git log --oneline -5` showing commit `bc7332d` | Terminal |
| Figure 5 | `git show --stat bc7332d` | Terminal |
| Figure 6 | The pull request page with the four description sections | <https://github.com/bos-com/bugema-linux/pull/21> |
| Figure 7 | The "Files changed" tab of the pull request | <https://github.com/bos-com/bugema-linux/pull/21/files> |
| Figure 8 | The peer review left on PR #15 | <https://github.com/bos-com/bugema-linux/pull/15#pullrequestreview-5589587612> |
| Figure 9 | The linked issue #20, with all its sections | <https://github.com/bos-com/bugema-linux/issues/20> |
| Figure 10 | The commit authored as `BBULYA AGATHA` | <https://github.com/BbulyaAgatha/bugema-linux/commit/bc7332db4b4d8f4982d4bc2e87443dbd2ef34fe8> |

---

## 7. Final Status (Approval / Merge)

| Item | Status |
| --- | --- |
| Pull request | **Open** — <https://github.com/bos-com/bugema-linux/pull/21> |
| Mergeable | Yes, `MERGEABLE` |
| Branch protection | `REVIEW_REQUIRED` — an approving review is required before the merge |
| Reviewers requested | `@Daniellakyusa`, `@kmuwanga83` |
| Files changed | 1 added, 0 modified, 0 deleted (`docs/testing/alpha-0.1-image-verification.md`) |
| Maintainer review | Pending |
| Merge | Not performed — the merge is the maintainer's decision |

---

## 8. Reflection

**1. What contribution did you make?**

I added `docs/testing/alpha-0.1-image-verification.md`, a procedure for verifying a
Bugema Linux release image and smoke testing it before it is treated as a release. It
covers obtaining the image and checking it against the published SHA256 file, writing
it to removable media safely, booting it in a virtual machine on a known reference
configuration, and then running 24 checks in eight groups — identity, resources,
package management, network and DNS, services, the development tools the milestone
requires, offline behaviour, and the graphical session — with a standard result table
and a regression table for comparing two builds, and with the format for documenting a
failure as a known issue.

**2. Why was it useful to Bugema Linux?**

Because the project's own roadmap promises things that only mean something if somebody
tests them. "ISO checksum", "known-issues documentation", "test ISO in VirtualBox" and
the milestone criteria for boot, installation, connectivity and the development tools
are all testable claims, and the project had no procedure for testing any of them. It
also makes the offline-first commitment (ADR-004) real rather than aspirational,
because one of the checks deliberately removes DNS and confirms the system is still
usable. Finally, it gives the project the quality-assurance documentation that a
distribution needs and that the roadmap implies but never spelled out, and it gives
students a self-contained piece of real quality engineering to perform.

**3. What Git commands did you use?**

`git clone`, `git remote add`, `git fetch`, `git checkout -b`, `git status`,
`git diff`, `git diff --cached --stat`, `git add`, `git commit`, `git commit --amend`,
`git log --oneline`, `git show --stat`, `git push -u` and `git push -f`. For the
GitHub-side work I used `gh`: `gh repo fork`, `gh issue create`, `gh pr create`,
`gh pr review`, `gh pr edit`, `gh pr comment`, `gh auth login` and `gh auth setup-git`.

**4. What feedback did your reviewer provide?**

On the pull request I reviewed, the most valuable finding was that the workflow which
was supposed to lint the project's shell scripts currently lints **nothing** — the
repository contains no shell scripts — and therefore reports a green build while
proving nothing at all. That is a failure mode that is easy to miss and expensive to
discover later, because the moment the first script is added, everyone assumes the
linting has been working. The same review found that the documentation file in the
pull request was committed as a 0-byte file, that one of the four commit messages was
simply `j`, that two of the commits described changes which were then undone by each
other, and that the pull request description stated a testing step ("executed
shellcheck locally in VS Code") without any output that a reviewer could repeat. It
also pointed out that a CI workflow which can only ever pass should not be trusted
without a `permissions` block and pinned actions.

**5. What changes did you make after review?**

Before submitting, the link validation of my own document found a reference to a file
that is still in review in another pull request, and not therefore present in `main`.
I replaced that reference with a pointer to the contribution workflow in
`CONTRIBUTING.md`, amended the commit and force-pushed the branch, after which all 9
internal links resolve. I also re-checked every command in the document against the
upstream documentation rather than against my memory, and I rewrote the testing
section of the pull request so that it shows the real output of the commands that were
actually executed — including the deliberate corruption of a file to prove that the
checksum check fails when it is supposed to. Reviewers have been requested on the pull
request and the comment invites specific feedback; each response will be answered in
the pull request, and any required revision will be committed to the same branch and
pushed.

**6. What did you learn about collaborative open-source development?**

That a contribution is judged on the evidence it carries, not on the intention behind
it. The pull request I reviewed would have looked complete — a sensible workflow, a
clear commit, a proper description — while actually adding an empty document and a
check that could never fail. Reading the diff properly, and checking the *size* of
the files and the *number* of files the new job actually examines, is what turned a
polite "looks good" into useful feedback.

I also learned that the workflow is what makes a project trustworthy, and that it has
to be held to the same standard as the code: a test that has never been seen to fail
is not a test, and a checksum that has only ever been checked against a good file is
not a verification. That is why my own contribution includes the corrupted-file case.

Finally, I learned how much a well-written issue and a well-written pull request
matter to the people who read them. The maintainer of a project sees dozens of
contributions a week; a document that states the problem, the reason it matters, the
proposed solution, how it will be tested, and the acceptance criteria in that order
is one they can act on, while a document that says only "I added a guide" is one they
have to reconstruct. Writing the issue first — and then making the pull request
answer it, section by section — is what turned my contribution from a file into a
change that somebody else can evaluate.
