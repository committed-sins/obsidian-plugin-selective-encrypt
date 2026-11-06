# Selective Encrypt for Obsidian

Selective encryption for ordinary Markdown notes, preserving the structure that Obsidian and Tasks need.

**Status: design repository. No working plugin, release, or vault deployment yet.**

## Intended behavior

- Leave YAML frontmatter byte-for-byte unchanged.
- Keep Markdown links, wikilinks, and embeds readable and functional, including those inside protected prose and task descriptions.
- Preserve task indentation, list markers, checkbox status, dates, priority, recurrence, IDs, dependencies, and other configured task metadata.
- Encrypt prose and the descriptive parts of tasks. Display a clickable lock in their place.
- Store ciphertext in Obsidian comments (`%% ... %%`) inside the existing `.md` file.
- Reveal and edit protected text in memory; save only ciphertext. Checkbox and task-metadata edits must also work while locked.
- Let the user choose an authenticated encryption profile and a separate password/key provider.
- Offer manual password entry and an optional desktop 1Password CLI provider. Store a secret reference, never the password.

Illustrative locked source (the payload below is a placeholder, not an encryption format):

```md
---
contexts:
  - "[[Project A]]"
---

🔒 %%selective-encrypt:v1:PAYLOAD%%

[[Contact A]]

- [ ] 🔒 %%selective-encrypt:v1:PAYLOAD%% [[Project A]] 📅 2026-11-10
```

The lock remains visible even without the plugin. An enabled plugin would replace it with a reveal/edit control in Reading view and Live Preview.

## Encryption and password options

Proposed profiles to evaluate: Web Crypto AES-256-GCM with a bounded password KDF, and an interoperable age passphrase profile. These are candidates, not implemented or audited choices. No custom cryptographic primitives or unauthenticated encryption.

A profile chooses the encryption format. A provider supplies the secret: manual entry or a user-configured 1Password field. These choices are independent. Changing either default must not silently change or strand existing data.

The proposed desktop provider invokes `op read --no-newline <op://reference>` directly with an argument array and no shell. Authorization belongs to the 1Password desktop app. References and executable paths are local settings; notes never choose commands or secret references. Mobile starts with manual entry; desktop CLI integration is not a mobile solution.

## Visibility and limits

Frontmatter, filenames, links (including labels/aliases), task metadata, and document structure intentionally remain visible. Search and task-description filters cannot inspect locked text. Preserving a link does not guarantee that an encrypted heading or block target still resolves; anchor handling needs an explicit design.

Past plaintext can remain in Git history, backups, undo history, or recovery snapshots. Encryption does not remove those copies. Plaintext in a running editor is accessible to that application and potentially other plugins. This is not protection against a compromised Obsidian session.

## Design documents

- [Requirements and acceptance criteria](docs/requirements.md)
- [Architecture and format decisions](docs/architecture.md)
- [Implementation roadmap](docs/roadmap.md)

## References

- [1Password CLI read](https://developer.1password.com/docs/cli/reference/commands/read/)
- [1Password desktop app integration](https://developer.1password.com/docs/cli/app-integration/)
- [age specification](https://c2sp.org/age)
