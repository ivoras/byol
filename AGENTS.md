# BYOL Project Goals and Architecture

## Project Goals

- Provide a single-file Python CLI (`byol`) that configures OpenCode for a user-supplied OpenAI-compatible endpoint.
- Keep implementation dependency-free (Python standard library only), targeting Python 3.10+.
- Prefer safe setup behavior: verify provider connectivity and a simple inference before writing config.
- Preserve existing OpenCode global configuration while updating only this project's provider block.

## Primary Artifact

- `byol`: executable Python script in repository root.

## Runtime Behavior

1. Resolve endpoint URL:
   - First positional CLI argument, if valid URL.
   - Fallback to `BYOL_OPENAPI_URL`.
2. Discover models from `<baseURL>/models`.
3. Run a pre-write inference check on `<baseURL>/chat/completions` with prompt `what's 2+2`.
4. If successful, write/update `~/.config/opencode/opencode.json`.
5. Set `provider.byol` with:
   - `npm: "@ai-sdk/openai-compatible"`
   - `name: "BYOL"`
   - `options.baseURL`
   - `models` discovered from the endpoint.

If any HTTP/discovery/inference step fails, the script exits non-zero and does not write config.

## Configuration Contract

- Target file: `~/.config/opencode/opencode.json`.
- Existing JSON is loaded and preserved.
- Top-level `provider` is created if missing.
- `provider.byol` is replaced atomically with the generated provider configuration.

## Environment Variables

- `BYOL_OPENAPI_URL`: fallback endpoint URL when no CLI URL is provided.
- `BYOL_OPENAPI_MODEL`: optional override model used for inference check.
- `BYOL_OPENAPI_KEY`: preferred bearer token for API requests.
- `OPENAI_API_KEY`: fallback bearer token if `BYOL_OPENAPI_KEY` is unset.

## Key Internal Functions (byol)

- `resolve_url()`: validates URL source priority.
- `discover_models()`: reads and maps `/models` response to OpenCode model map.
- `verify_inference()`: performs pre-write accessibility + basic response validation.
- `read_config()/write_config()`: robust JSON file I/O for OpenCode config.

## Non-Goals

- No external package manager integration.
- No automatic model capability inference beyond available `id` and optional `name`.
- No modifications outside `provider.byol` in OpenCode config.

## Git commit rules

- Think about what the goals behind the diff-ed code are, and then: write an informative subject line, a blank line, and 1 or 2 sentences descibing the whys and goals behind the commit.
- Add a blank line, and a very short heroic poem about the commit, in the style of the Iliad.
