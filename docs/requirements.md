# Requirements and acceptance criteria

## Preservation

1. Encrypt/decrypt round trips restore the original protected text exactly, including whitespace, Unicode, line endings, and Markdown formatting.
2. Preserve frontmatter bytes, readable link tokens, task status and metadata, ordering, nesting, and line boundaries. Do not regenerate the entire document through a Markdown serializer.
3. Recognize links and task fields with syntax-aware parsing. Never classify arbitrary dates, emoji, tags, or brackets in prose as metadata based on a broad regex alone.
4. Configure the supported Tasks syntax explicitly, including emoji and Dataview formats, custom statuses, recurrence, IDs, dependencies, and inline fields. Preserve native Tasks semantics; encryption must not redefine task.happens.
5. Keep links inside task descriptions readable. Decide handling of tags, headings, block IDs, reference definitions, code, tables, callouts, task continuations, and inline fields before claiming whole-note support. Unimplemented constructs must cause a no-write error rather than silently remain plaintext.
6. Treat existing comments and existing encrypted spans as syntax, not new plaintext; avoid double encryption.

## Locked behavior

- Obsidian can still index frontmatter and readable links.
- Tasks can discover tasks and change checkbox/status/date metadata without decrypting descriptions or deleting their payloads.
- Native and queried task rendering show a lock rather than ciphertext. Verify actual Tasks renderer behavior; comments alone are not proof of compatibility.
- Clicking a lock reveals protected text in memory. Saving edits writes authenticated ciphertext, never a temporary plaintext note.
- A plugin-free view retains a lock and conceals ciphertext in Obsidian comments. Other Markdown renderers may display `%%` comments; this is not a confidentiality failure because their contents are encrypted.

## Secrets and failure handling

- Persist only nonsecret provider configuration. Do not put passwords, keys, CLI output, or plaintext in settings, logs, errors, files, Git, telemetry, or the clipboard without an explicit copy action.
- CLI execution uses an allowlisted operation, no shell, a configured executable, bounded output, timeout, cancellation, sanitized errors, and strict secret-reference validation. Treat stdout as secret and strip no password whitespace; use --no-newline.
- A cancelled 1Password request, wrong password, failed authentication tag, unsupported format, malformed payload, unsupported syntax, or concurrent source edit leaves the file unchanged.
- Fresh salt/nonce material for encryption; validated and bounded KDF costs and payload sizes on decrypt.
- Lock clears plugin references to decrypted buffers and secrets; do not promise reliable JavaScript memory erasure.
- Explicit permanent decryption is a separate action, with clear plaintext consequences.

## Verification gate before release

- Fixture-based preservation and round-trip checks across Markdown and supported Tasks syntax.
- Task renderer/parser integration checks and concurrent checkbox/date edits while content is unlocked.
- Tamper, wrong-password, malformed-input, KDF-bound, cancellation, stale-write, and CLI-failure checks.
- Standalone recovery with published format documentation and cross-implementation vectors where applicable.
- User-driven live checks in a disposable vault on supported platforms. No deployment into Continuum without authorization.
