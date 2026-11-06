# Architecture and format decisions

This is a proposal, not an implemented format or security claim.

## Components

1. A syntax-aware planner identifies protected text spans and preserved source spans. It produces a preview and byte-preserving edits against an exact source revision.
2. Encryption profiles implement versioned authenticated envelopes using established libraries/platform APIs. Profiles expose independent encrypt/decrypt capabilities; readers retain old versions after defaults change.
3. Secret providers resolve user-configured credentials. Manual entry is the portable baseline. An optional desktop adapter uses 1Password CLI/app authorization. References are selected in trusted plugin settings, never read from note contents.
4. A transactional writer checks the source revision, validates the complete result, and uses the vault's supported write mechanism. Never overwrite concurrent edits, and never persist decrypted editor documents.
5. Reading-view and CodeMirror decorations reveal individual spans in memory. Avoid feeding decrypted documents into Obsidian's ordinary save pipeline. Edits to protected spans must be merged with external metadata edits or rejected safely on conflict.

## Comment envelope

Proposed outer shape: `🔒 %%selective-encrypt:v1:<base64url-envelope>%%`.

Use a restricted payload alphabet that cannot close an Obsidian comment. Define version, profile ID, KDF parameters, salt, nonce, ciphertext, authentication data, and strict length/cost bounds before implementation. Authenticate format parameters and span identity; assess binding to readable surrounding structure so ciphertext cannot be silently substituted between tasks. Renames and legitimate metadata edits must remain possible.

The final format must specify how discontiguous protected runs interleave with readable links and task tokens, how spacing and Markdown delimiters survive, and how plaintext is reconstructed without saving it. The example is not a stable wire format.

## Candidate profiles

- AES-256-GCM via Web Crypto: evaluate a portable bounded password KDF and parameter policy. Specify the complete profile rather than exposing arbitrary cipher/KDF combinations.
- age passphrase mode: evaluate maintained JavaScript/TypeScript support, mobile performance, recovery via the standard CLI, and a comment-safe encoding of the resulting age payload.
- Recipient-key encryption may follow later; it is a different key model and should not be disguised as a password option.

Choose defaults after a focused implementation review and measured device costs. Profile changes apply to new encryption; migration must be explicit, recoverable, and preserve older backups' credentials.

## 1Password integration

Resolve one configured field with `op read --no-newline <reference>` using direct process execution on desktop. An installed CLI and enabled desktop app integration are prerequisites. Do not request a service-account token for personal desktop use. Do not fetch real secrets during development checks; mock the CLI boundary.

Store the secret reference locally, not inside the ciphertext/comment. References are not password values, but can disclose vault/item names; prefer IDs and allow a local alias mapping for portable note key identifiers. Key identifiers in documents must never authorize arbitrary 1Password reads. Missing CLI/access can offer manual entry without changing the encryption profile. No silent manual fallback after a rejected request and no repeated authorization loops.

1Password supplies the encryption/decryption password; it does not encrypt the note itself. The secret necessarily enters plugin memory during use. Replacing the 1Password field does not rotate ciphertext: keep access to old passwords until data and backups are handled deliberately.

## Open decisions

- Final name, profile defaults, stable envelope, password-cache lifetime, and lock triggers.
- Supported Tasks plugin versions/formats and how queried task descriptions render controls.
- Headings/anchors, tags, inline fields, fenced code, multiline tasks, and existing comments.
- Minimal metadata binding that prevents meaningful payload substitution while tolerating legitimate edits.
- Recovery tool interface, migration behavior, and handling of mobile credentials.
