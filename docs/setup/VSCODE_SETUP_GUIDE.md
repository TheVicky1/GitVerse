# VS Code Setup Guide for GitVerse

This guide details the optimal Visual Studio Code (VS Code) setup for contributing to GitVerse.

## Recommended Extensions

To ensure a consistent development experience, we recommend installing the following extensions:
* **golang.Go**: Official Go language extension (IntelliSense, test running, gofumpt).
* **esbenp.prettier-vscode**: Markdown and JavaScript formatting.
* **EditorConfig.EditorConfig**: Enforcing repository indent standards.

## Workspace Settings

Create a `.vscode/settings.json` file in the root of the repository and add the following configuration:

```json
{
  "editor.formatOnSave": true,
  "go.lintTool": "golangci-lint",
  "files.associations": {
    "*.html": "go-html"
  }
}

Running & Debugging from VS Code
You can run and debug GitVerse components directly within VS Code.

Debugging Configuration
Create a .vscode/launch.json file with the following configurations to debug cmd/gitverse and cmd/devserver:

{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug GitVerse",
      "type": "go",
      "request": "launch",
      "mode": "auto",
      "program": "${workspaceFolder}/cmd/gitverse"
    },
    {
      "name": "Debug DevServer",
      "type": "go",
      "request": "launch",
      "mode": "auto",
      "program": "${workspaceFolder}/cmd/devserver"
    }
  ]
}

Running Tests
You can run tests directly from the VS Code Test Explorer. Open the Test Explorer from the Activity Bar, select the test you want to run, and click the "Play" button.

GitHub Codespaces & DevContainers
If you prefer not to configure your local environment, you can spin up a cloud environment with one click:

Go to the repository page on GitHub.

Click the Code button.

Select the Codespaces tab.

Click Create codespace on main (or on your branch).

This will open a fully configured VS Code in your browser.

