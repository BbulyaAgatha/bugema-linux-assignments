Hello maintainers and fellow students — this pull request is my Open Source Systems course
contribution, and it is ready for peer and maintainer review.

**What it does**

It adds `docs/testing/alpha-0.1-image-verification.md`, a documented procedure for
verifying a Bugema Linux release image and smoke testing it before it is treated as a
release. It was raised as issue #20, and this pull request closes it.

**Why this contribution**

`ROADMAP.md` lists *"test ISO in VirtualBox"*, *"ISO checksum"* and *"known-issues
documentation"* among the Alpha 0.1 acceptance criteria, and the milestone requires the
release to demonstrably boot, install, connect to the Internet, and carry Git, Python
and Java. Every one of those is a testable fact, but the project currently has no
document that says *how* to test them, no reference configuration to test on, and no
standard way to record the result — so checks are done ad hoc and the results of two
people testing the same build cannot be compared.

**How it was tested**

* The checksum procedure was run in **both** directions — against an intact file
  (result `OK`) and against a deliberately corrupted one (result `FAILED`, exit status
  1) — because a verification procedure that has never been seen to fail is not a
  verification procedure. The transcript is in the pull request description.
* Every smoke test command was executed on a live Linux system and the expected
  results in the document are the results that were actually observed.
* All 9 internal links were resolved programmatically against the repository tree, and
  each of the 24 checks was traced to a line in `ROADMAP.md` or to an accepted ADR.

**Review requested**

I would especially welcome feedback on:

1. **Section 6.2, check G** — the offline test deliberately stops DNS to check the
   offline-first behaviour required by ADR-004. I am not yet sure this is a safe check
   to run on a machine that is also being used for other work, and I would rather
   remove it than have it disturb somebody's session.
2. **Section 8** — the guide distinguishes a failure that reproduces on several
   machines (likely an image defect) from one that appears on a single machine
   (likely a test-environment problem). I would like to know whether the maintainers
   agree with that rule.
3. **The location of the document** — I have placed it in a new `docs/testing/`
   directory for consistency with the project structure in `README.md`, but if
   `docs/architecture/` or a future `docs/user-guide/` is preferred, I am happy to
   move it.
4. **Checksum signing** — the guide currently assumes a plain `sha256sum` comparison.
   If the project intends to publish signed checksum files for each release, a
   `gpg --verify` step should be added. I have raised this as a question on issue #20.

The pull request adds one file and modifies nothing else, so it should not conflict
with the documentation work already in review. Please leave comments on any section —
I will revise the guide and push the changes to this branch.
