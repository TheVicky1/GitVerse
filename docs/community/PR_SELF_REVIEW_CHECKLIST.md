*# Pull Request Self-Review Checklist*



*Use this checklist before submitting a pull request to GitVerse. It helps ensure your contribution is complete, easy to review, and consistent with project guidelines.*



*## 1. Before You Open a PR*



*- \[ ] Make sure your feature branch is up to date with the latest `main` branch.*

*- \[ ] Confirm that your changes address one clear issue or improvement.*

*- \[ ] Check that unrelated files or changes are not included.*

*- \[ ] Reference the related issue in your pull request description using `Closes #<number>`.*

*- \[ ] Review your changes with `git diff` before committing.*

*- \[ ] Ensure your commit messages follow the project's Conventional Commits guidelines.*



*## 2. Documentation Quality Checklist*



*- \[ ] Verify that every file is saved in the correct path specified by the issue.*

*- \[ ] Use a single top-level `#` heading and maintain a logical heading hierarchy.*

*- \[ ] Check spelling, grammar, punctuation, and Markdown formatting.*

*- \[ ] Verify that all relative links point to existing files or valid sections.*

*- \[ ] Remove broken links, unfinished notes, and placeholder `TODO` text.*

*- \[ ] Add language identifiers to fenced code blocks, such as `bash`, `go`, or `markdown`.*

*- \[ ] Ensure commands and examples are accurate and easy to follow.*

*- \[ ] Confirm that the documentation is understandable to first-time contributors.*



## 3. Technical Verification Checklist

Run the checks relevant to your contribution. You do not need to run every check for documentation-only changes.

**For Go code changes** (requires Go to be installed):

- [ ] Run `gofmt -l .` and ensure all Go files are formatted.
- [ ] Run `go test ./...` and confirm that all tests pass.
- [ ] Run `go run ./cmd/gendocs -check` when generated documentation is affected.

**For JavaScript changes** (requires Node.js):

- [ ] Run `node --test internal/webui/jstest/*.test.mjs` when web UI files are modified.

**For documentation-only changes:**

- [ ] Run `git diff --check` to check for whitespace errors.
- [ ] Verify Markdown formatting, links, and file paths.

- [ ] Review command output and resolve any errors related to your changes.