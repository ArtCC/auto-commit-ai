# Auto Commit AI

Git hook for macOS that generates commit messages from your staged diff using OpenAI's Responses API.

The hook is designed for local use: it keeps your API key out of the script, avoids logging diffs by default, and never blocks a commit if the AI request fails.

## Features

- Generates a commit message from `git diff --cached`.
- Uses OpenAI's current `/v1/responses` endpoint.
- Uses a concise format: title plus bullet list.
- Reads the OpenAI key from `OPENAI_API_KEY` or macOS Keychain.
- Skips manual, merge, squash, and amend commits.
- Limits large diffs before sending them to OpenAI.
- Writes `Update changes` as a fallback if AI generation fails and the commit message is empty.
- Writes diagnostic logs to `prepare-commit-msg.log` next to the installed hook.
- Does not log diff contents unless explicitly enabled.

## Requirements

- macOS.
- Xcode or Command Line Tools with Swift available through `xcrun swift`.
- Git 2.x or later.
- OpenAI API key.

## Recommended Local Installation

Use a global Git hooks directory if you want the hook available in all local repositories.

1. Create a hooks directory:

   ```bash
   mkdir -p ~/.ai-githooks
   ```

2. Copy the hook:

   ```bash
   cp prepare-commit-msg ~/.ai-githooks/prepare-commit-msg
   ```

3. Make it executable:

   ```bash
   chmod +x ~/.ai-githooks/prepare-commit-msg
   ```

4. Configure Git to use that directory:

   ```bash
   git config --global core.hooksPath ~/.ai-githooks
   ```

5. Store your API key in macOS Keychain:

   ```bash
   security add-generic-password \
     -a "$USER" \
     -s auto-commit-ai-openai-api-key \
     -w "YOUR_OPENAI_API_KEY" \
     -U
   ```

To disable the global hook later:

```bash
git config --global --unset core.hooksPath
```

## Per-Repository Installation

Use this if you only want the hook in one repository.

```bash
cp prepare-commit-msg /path/to/your/repo/.git/hooks/prepare-commit-msg
chmod +x /path/to/your/repo/.git/hooks/prepare-commit-msg
```

Then configure the API key using either Keychain or `OPENAI_API_KEY`.

## API Key Options

### Option 1: macOS Keychain

Recommended for normal local use:

```bash
security add-generic-password \
  -a "$USER" \
  -s auto-commit-ai-openai-api-key \
  -w "YOUR_OPENAI_API_KEY" \
  -U
```

### Option 2: Environment Variable

Useful for terminal-only workflows:

```bash
export OPENAI_API_KEY="YOUR_OPENAI_API_KEY"
```

For GUI Git clients, environment variables may not be available. Keychain is usually more reliable on macOS.

## Configuration

All configuration is optional and uses environment variables.

| Variable | Default | Description |
| --- | --- | --- |
| `OPENAI_API_KEY` | unset | OpenAI API key. Used before Keychain if present. |
| `AUTO_COMMIT_AI_MODEL` | `gpt-5.4-mini` | OpenAI model. |
| `AUTO_COMMIT_AI_TIMEOUT` | `30` | Request timeout in seconds. |
| `AUTO_COMMIT_AI_MAX_DIFF_CHARS` | `60000` | Maximum diff characters sent to OpenAI. |
| `AUTO_COMMIT_AI_MAX_TOKENS` | `1200` | Maximum output tokens sent as `max_output_tokens`. |
| `AUTO_COMMIT_AI_FALLBACK_MESSAGE` | `Update changes` | Message used if AI generation fails and the commit message is empty. |
| `AUTO_COMMIT_AI_KEYCHAIN_SERVICE` | `auto-commit-ai-openai-api-key` | Keychain service name. |
| `AUTO_COMMIT_AI_LOG_DIFF` | unset | Set to `true` only if you want diff snippets in the log. |

## Usage

1. Stage your changes:

   ```bash
   git add .
   ```

2. Commit without providing a message:

   ```bash
   git commit
   ```

3. Review or edit the generated message in your Git editor.

The hook skips `git commit -m "..."` when the message is non-empty so manual messages are respected. An empty `git commit -m ""`, as used by some GUI clients such as SourceTree, is treated as no message and receives an AI-generated message. It does not skip Git templates, because those are loaded before the editor opens.

## Logs

The hook writes logs next to the installed script:

```bash
cat ~/.ai-githooks/prepare-commit-msg.log
```

Diff contents are not logged by default. Enable `AUTO_COMMIT_AI_LOG_DIFF=true` only for temporary debugging.

## Privacy Notes

The staged diff is sent to OpenAI. Do not use this hook for commits containing secrets or code that cannot leave your machine.

The hook limits large diffs, but it does not redact secrets automatically.

## Repository Structure

```text
auto-commit-ai/
├── prepare-commit-msg
├── README.md
└── LICENSE
```

## License

[Apache License 2.0](LICENSE)

---

Arturo Carretero Calvo - 2026
