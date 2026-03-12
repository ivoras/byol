# byol

Bring Your Own LLM (into a SSH session with OpenCode).

## byol

`byol` is a single-file Python script that configures [OpenCode](https://opencode.ai) to connect to an OpenAI-compatible LLM inference provider. It writes the provider configuration to `~/.config/opencode/opencode.json`.

### Requirements

- Python 3.10+
- Standard library only (no external packages)

### Usage

**With a URL argument:**

```bash
python byol https://api.example.com/v1
```

**With environment variable (fallback when no argument is given):**

```bash
export BYOL_OPENAPI_URL=https://api.example.com/v1
python byol
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

### SSH: auto-set env var and tunnel to local LLM

When you SSH into a remote server but want OpenCode on the remote side to use an
LLM running on your local machine, you can pass `BYOL_OPENAPI_URL` via SSH
options and create a reverse tunnel in the same command.

Example (local LLM on `127.0.0.1:11434`, remote forwarded port `18080`):

```bash
ssh \
  -o SetEnv=BYOL_OPENAPI_URL=http://127.0.0.1:18080/v1 \
  -R 127.0.0.1:18080:127.0.0.1:11434 \
  user@remote-host
```

Then on the remote host:

```bash
python byol
```

How this works:

- `-R 127.0.0.1:18080:127.0.0.1:11434` exposes your local LLM port to the remote
  host as `127.0.0.1:18080`
- `-o SetEnv=...` sets `BYOL_OPENAPI_URL` in the SSH session so `byol` picks it
  up automatically

Notes:

- Your SSH server must allow the environment variable (with the
  `AcceptEnv` setting in `/etc/ssh/sshd_config`) before the `ssh` client
  can set the environment variable. A line like: `AcceptEnv LANG LC_* BYOL*`
  should exist in that config file.
- If your local LLM uses a different port or path, adjust the URL and tunnel
  ports accordingly.
