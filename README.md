# byol

Bring Your Own LLM (into a SSH session with OpenCode).

## byod

`byod` is a single-file Python script that configures [OpenCode](https://opencode.ai) to connect to an OpenAI-compatible LLM inference provider. It writes the provider configuration to `~/.config/opencode/opencode.json`.

### Requirements

- Python 3.10+
- Standard library only (no external packages)

### Usage

**With a URL argument:**

```bash
python byod https://api.example.com/v1
```

**With environment variable (fallback when no argument is given):**

```bash
export BYOL_OPENAPI_URL=https://api.example.com/v1
python byod
```

URL resolution priority: command-line argument first, then `BYOL_OPENAPI_URL`.

### What it does

- Adds or updates the `provider.byod` entry in `~/.config/opencode/opencode.json`
- Uses `@ai-sdk/openai-compatible` for OpenAI-compatible APIs (`/v1/chat/completions`)
- Preserves existing config entries (e.g. other providers, model, autoupdate)

### Example output

```
Configured provider 'byod' at /home/user/.config/opencode/opencode.json
  baseURL: https://api.example.com/v1
```
