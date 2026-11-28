# VS Code Setup Guide for GitVerse

This guide details the optimal Visual Studio Code (VS Code) setup for contributing to GitVerse.

## Recommended Extensions

To ensure a consistent development experience, we recommend installing the following extensions:

* **golang.Go**: Official Go language extension (IntelliSense, test running, `gofumpt`, and `gotmpl` template support).
* **esbenp.prettier-vscode**: Markdown and JavaScript formatting.
* **EditorConfig.EditorConfig**: Enforcing repository indent standards.
* **jinliming2.vscode-go-template** *(optional)*: Provides dedicated HTML + Go template syntax highlighting if you work extensively on `internal/webui/templates/`.

## Workspace Settings

Create a `.vscode/settings.json` file in the root of the repository and add the following configuration:

```json
{
  "editor.formatOnSave": true,
  "go.lintTool": "golangci-lint",
  "files.associations": {
    "*.html": "gotmpl"
  }
}
```

> **Note:** `golang.Go` registers the `gotmpl` language ID for Go templates. If you prefer standard HTML highlighting on non-template static files under `site/`, you can scope the association to `"internal/webui/templates/*.html": "gotmpl"`.

## Running & Debugging from VS Code

You can run and debug GitVerse components directly within VS Code.

### Debugging Configuration

Create a `.vscode/launch.json` file with the following configurations to debug `cmd/gitverse` and `cmd/devserver`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug GitVerse",
      "type": "go",
      "request": "launch",
      "mode": "auto",
      "program": "${workspaceFolder}/cmd/gitverse",
      "args": ["torvalds"]
    },
    {
      "name": "Debug DevServer",
      "type": "go",
      "request": "launch",
      "mode": "auto",
      "program": "${workspaceFolder}/cmd/devserver",
      "cwd": "${workspaceFolder}"
    }
  ]
}
```

* **CLI Debugging (`Debug GitVerse`):** Supplying `"args": ["torvalds"]` targets a public profile. If you omit `args` to check your own profile, you must first be logged in via GitHub CLI (`gh auth login`).
* **DevServer Debugging (`Debug DevServer`):** Setting `"cwd": "${workspaceFolder}"` ensures the server locates `.env.local` and `site/` in the repository root rather than inside `cmd/devserver/`. Before launching `Debug DevServer`, create a `.env.local` file in the repository root with all required variables expected by `cmd/devserver/main.go` and `web.FromEnv()`: `GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`, `GH_BOT_TOKEN`, and `STATE_SECRET` (`STATE_SECRET` must be at least 32 bytes, e.g. generated with `openssl rand -base64 32`).

## Running Tests

You can run tests directly from the VS Code Test Explorer:

1. Open the **Test Explorer** from the Activity Bar.
2. Select the test or package you want to run.
3. Click the **Run Test** ("Play") button.

## GitHub Codespaces & DevContainers

If you prefer not to configure your local machine, you can spin up a cloud environment in your browser:

1. Go to the repository page on GitHub.
2. Click the **Code** button.
3. Select the **Codespaces** tab.
4. Click **Create codespace on main** (or on your branch).

This will open VS Code in your browser. The repository does not provide a `.devcontainer` or `.vscode` configuration, so install the recommended extensions and apply the workspace settings described above in the codespace.
