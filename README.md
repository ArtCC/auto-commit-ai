# Auto Commit AI

A Git hook for macOS that automatically generates detailed, well-structured commit messages using the OpenAI API (model `gpt-4o-mini`). Perfect for maintaining a clear and consistent commit history without manual effort.

## Features

- **Automatic message generation** based on the staged diff (`git diff --cached`).
- **Structured output**: paragraphs, bullet points, or line breaks for multiple changes.
- **Up to 2500 characters** to cover all relevant modifications.
- **Detailed logging** in `prepare-commit-msg.log` within the hooks directory.
- **Compatible with both the command line and Git GUI clients** (e.g., SourceTree: create commit with empty text).

## Requirements

- macOS with Swift installed (Xcode or Command Line Tools).
- Git 2.x or later.
- OpenAI API key.

## Global Installation

Optional: To apply this hook to all your Git repositories without copying it manually into each .git/hooks folder, follow these steps:

1. **Create a directory for global hooks:
   ```bash
   mkdir -p ~/.ai-githooks
   ```
2. **Copy the hook script into that directory:
   ```bash
   cp auto-commit-ai/prepare-commit-msg ~/.ai-githooks/prepare-commit-msg
   ```
3. **Make it executable:
   ```bash
   chmod +x ~/.ai-githooks/prepare-commit-msg
   ```
4. **Configure Git to use your global hooks directory:
   ```bash
   git config --global core.hooksPath ~/.ai-githooks
   ```
5. **Insert your OpenAI API key in the script (only once inside ~/.ai-githooks/prepare-commit-msg):
   ```bash
   let finalKey = "YOUR_API_KEY_HERE"
   ```

Optional: To revert to the default Git hooks behavior:
   ```bash
   git config --global --unset core.hooksPath
   ```

## Installation in project repository

1. **Clone this repository** into your project:
   ```bash
   git clone https://github.com/your-username/auto-commit-ai.git
   ```

   ```bash
   git@github.com:ArtCC/auto-commit-ai.git
   ```
2. **Copy the hook** into your repo’s hooks folder:
   ```bash
   cp auto-commit-ai/prepare-commit-msg .git/hooks/prepare-commit-msg
   ```

   ```bash
   rm -rf auto-commit-ai
   ```
3. **Make it executable**:
   ```bash
   chmod +x .git/hooks/prepare-commit-msg
   ```
4. **Insert your OpenAI API key** in the script:
   ```swift
   // Inside .git/hooks/prepare-commit-msg
   let finalKey = "YOUR_API_KEY_HERE"
   ```

## Configuration

- Change the **shebang** to your Swift interpreter if needed:
  ```bash
  #!/usr/bin/env swift
  ```
- Adjust the **timeout** by modifying the `sem.wait(timeout: .now() + 15)` value.
- Switch to a different OpenAI model by updating `model: "gpt-4o-mini"`.

## Usage

1. Make your code changes.
2. Stage them:
   ```bash
   git add .
   ```
3. Commit as usual:
   ```bash
   git commit
   ```
4. The hook will send the staged diff to OpenAI and prepend the generated message to `COMMIT_EDITMSG`.
5. Inspect the log for details or errors on installation folder:
   ```bash
   cat prepare-commit-msg.log
   ```

## Repository Structure

```
auto-commit-ai/
├── prepare-commit-msg      # Swift hook script
└── README.md               # Project documentation
└── LICENSE                 # Project license
```

## Contributing

Contributions are welcome! Please:

- Open issues to report bugs or suggest enhancements.
- Submit pull requests with new features or fixes.

Ensure your code follows the established Swift style and include tests or examples where appropriate.

## License

[Apache License](LICENSE)

---

**Arturo Carretero Calvo - 2025**
