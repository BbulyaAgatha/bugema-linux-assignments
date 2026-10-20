Peer review — #15 `ci: add GitHub Actions workflow for ShellCheck and script validation (closes #15)`

Reviewed against the contribution criteria in `CONTRIBUTING.md`: branch name, quality of
the contribution, documentation, commit messages, testing, pull request description,
and relevance to the project.

The direction of this pull request is right, and it is work the project actually
needs: the roadmap commits the project to reproducible builds and automated testing,
and linting the shell scripts that will build the image is the first step towards
that. The branch name `feat/shellcheck-ci` is clear and consistent with the naming
conventions in `CONTRIBUTING.md` section 5, and the workflow itself is well
structured — the three stages are separated into their own steps, each with an
`echo` line that explains what it is doing, so that the Actions log reads as a test
report rather than as a wall of output.

There are a number of things to fix before this can be merged, and one design point
that I think is worth discussing.

**1. `docs/administration/ci-script-linting.md` is an empty file (blocking).**

The diff shows the file being added as an empty blob:

```text
diff --git a/docs/administration/ci-script-linting.md b/docs/administration/ci-script-linting.md
new file mode 100644
index 0000000..e69de29
```

`e69de29` is the well-known empty-blob hash, and the file is 0 bytes in the head of
this branch. The pull request description says the file "details how contributors can
run ShellCheck and syntax checks locally", so the documentation that the description
promises is not actually in the change. This is normally caused by staging the file
before writing the content:

```console
$ git add docs/administration/ci-script-linting.md   # staged while still empty
$ ... write the document ...
$ git commit -m "ci: ..."                              # content never re-added
```

Re-adding it and confirming with `git diff --cached --stat` should fix it.

**2. The workflow currently checks nothing, and it will report success while doing so.**

The repository contains no shell scripts today — `git ls-files '*.sh'` returns nothing
— so both `find` commands match zero files, both steps succeed, and the workflow
reports a green build. That is the most dangerous outcome: the first time a script is
added, the linting is already being relied upon, but nothing has ever proved that the
linting step actually catches an error.

Two things would make this trustworthy:

* **A self-test of the linter.** Add a small script under `scripts/` that contains a
  deliberate error, and a step that asserts the linter *fails* on it. If the workflow
  cannot fail, it is not a test.
* **An explicit "no files found" outcome.** For example, have the step report the
  number of files it examined, so that a green run reading "0 files checked" is
  visibly different from one reading "12 files checked".

**3. The `find` expressions will also walk the `.git` directory and any vendored
content.**

```yaml
find . -type f -name "*.sh" -not -path "*/.*" -exec bash -n {} +
```

The `-not -path "*/.*"` filter excludes hidden directories, so `.git` is skipped, but
it will also skip any script that is deliberately placed in a hidden directory, and it
does not exclude build output or vendored dependencies once the project has them. It
would be more precise to scope the search to the directories the project actually
owns, for example `find scripts/ -type f -name "*.sh"`, which also makes the intent
readable.

**4. The smoke-test step is fragile, and it can fail the build for the wrong reason.**

```yaml
for script in $(find scripts -type f -name "*.sh" 2>/dev/null); do
  if grep -q "\-\-help" "$script"; then
    bash "$script" --help
  fi
done
```

* `grep -q "\-\-help"` matches any script that merely *mentions* `--help` in a comment
  or in a usage string, and then the script is executed. If the script does not
  implement `--help` and exits non-zero, the whole workflow fails — for a reason that
  has nothing to do with the code under test.
* The `for` loop splits on whitespace, so a path containing a space breaks the loop.
  A `while read -r` loop over `find -print0` avoids both problems.
* The step name promises a smoke test, but nothing asserts that the script *did*
  anything — it would be better to assert that the help text is non-empty.

**5. The workflow should declare the permissions it needs, and pin its actions.**

For a workflow that only reads the repository, the default token permissions are
broader than necessary. The defence-in-depth model in
[ADR-005](../architecture/adr/ADR-005-security-architecture.md) applies to CI as
much as to the product, so a permissions block would be a good fit:

```yaml
permissions:
  contents: read
```

For the same reason, `actions/checkout@v4` is better pinned to a commit SHA, with the
version tag in a comment, so that a moved tag cannot change what runs in CI. This is
standard practice and costs one line.

**6. The `CODE_OF_CONDUCT.md` change does not belong in this pull request.**

The only change to that file is the removal of a `---` separator, which is unrelated
to CI. `CONTRIBUTING.md` section 7 asks pull requests to avoid unrelated changes, and
a reviewer looking for a security-relevant change in a file like the Code of Conduct
should be able to find it. It would be better as a separate, single-purpose pull
request — and if the separator is meant to be removed deliberately, the commit message
should say so, because the current message describes something else entirely.

**7. The commit history contains a vague message and a self-cancelling pair.**

```text
3b2966a  j
c4b0a71  Enhance Code of Conduct with communication guidelines
7e815e2  Delete respectful communication guidelines
a446cbb  ci: add GitHub Actions workflow for ShellCheck and script validation
```

* `j` tells a future reader nothing. `CONTRIBUTING.md` section 6 asks for a message
  such as `ci: add GitHub Actions workflow for ShellCheck and script validation`,
  which the last commit already uses.
* "Enhance Code of Conduct with communication guidelines" is immediately undone by
  "Delete respectful communication guidelines", and neither description matches the
  final diff. Squashing into one commit with a descriptive message keeps the history
  honest.

**8. The pull request description needs the "Expected Result" section, and the testing
evidence needs to be reproducible.**

The description has Summary, Changes Made and Testing & Evidence, but not the
**Expected Result** section that the contribution guidelines and the course both ask
for — a short statement of what the project or its contributors gain.

The testing evidence itself is currently "Executed `bash -n` syntax check and
`shellcheck` locally in VS Code terminal". That is a statement, not evidence, and it
cannot be repeated by a reviewer. It would be much stronger to show the actual
commands and their output, for example:

```console
$ bash -n scripts/example.sh && echo "syntax OK"
syntax OK
$ shellcheck scripts/example.sh
In scripts/example.sh line 4:
for f in $(ls); do
         ^-- SC2044: For loops over find output are fragile. Use find -exec or a while read loop.
```

and to record what the workflow does when it finds no scripts at all, which is the
state of the repository today.

**Summary of what is needed**

| Item | Action |
| --- | --- |
| `docs/administration/ci-script-linting.md` | Push the actual content of the document |
| Workflow coverage | Prove the linter can fail, and report how many files were checked |
| `find` scope | Restrict the search to the directories the project owns |
| Smoke-test step | Use a whitespace-safe loop and assert on the result |
| Security | Add `permissions: contents: read` and pin actions to a commit SHA |
| `CODE_OF_CONDUCT.md` | Move the unrelated separator change into its own pull request |
| Commit history | Drop the `j` commit and squash the self-cancelling pair |
| PR description | Add the "Expected Result" section and replace the testing statement with reproducible command output |

The substance of the workflow is good and the three-stage structure is the right
shape for it. Once the empty document is filled in, the linter is shown to be able to
fail, and the description carries reproducible evidence, this will be a solid
contribution to the project's automation work.
