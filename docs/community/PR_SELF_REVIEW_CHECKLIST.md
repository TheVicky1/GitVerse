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



*## 3. Technical Verification Checklist*



*Complete the relevant checks when your contribution modifies code, templates, or generated documentation.*



*- \[ ] Run `gofmt -l .` and ensure all Go files are formatted.*

*- \[ ] Run `go test ./...` and confirm that all tests pass.*

*- \[ ] Run `go run ./cmd/gendocs -check` and confirm that generated documentation has no drift.*

*- \[ ] Run relevant JavaScript tests if web UI files were modified: `node --test internal/webui/jstest/\*.test.mjs`.*

*- \[ ] Review command output and resolve any errors related to your changes.*



*For documentation-only contributions, run the checks relevant to the files you changed.*



*## 4. Writing a Great PR Description*



*- \[ ] Provide a clear and descriptive pull request title.*

*- \[ ] Summarize what you changed.*

*- \[ ] Explain why the change is useful.*

*- \[ ] Link the related issue using `Closes #<number>`.*

*- \[ ] Include screenshots or previews when relevant to UI or documentation changes.*

*- \[ ] Mention the tests or verification steps you completed.*

*- \[ ] Disclose any known limitations or unfinished work.*

*- \[ ] Ensure the description is clear and easy for maintainers to review.*



*## Final Review*



*- \[ ] All intended changes are committed and pushed to your branch.*

*- \[ ] No unrelated files or accidental changes are included.*

*- \[ ] The pull request targets the `main` branch.*

*- \[ ] The pull request is ready for maintainer review.*



*Thank you for contributing to GitVerse! Every thoughtful contribution helps make the project better for the community.*

