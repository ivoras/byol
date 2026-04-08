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

## Git Commit Rules

Do not commit files without user's explicit instructions.

When creating commits, follow this format exactly:

1. **Subject line**: One concise sentence describing the change (≤50 chars)
2. **Blank line**
3. **Body**: 1-2 sentences explaining *why* the change was made (goals/motivation)
4. **Blank line**
5. **Heroic poem**: Exactly one quatrain (4 lines), rhyming AABB or ABAB, in Homeric/Iliad style with elevated diction

Example:
```
Fix datetime import location and improve /models error message

Move datetime import to module top for consistency and fix misleading error when endpoint returns no models.

The import ascends to where it belongs,
The error speaks truth without wrongs.
```
