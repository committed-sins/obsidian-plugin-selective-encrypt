# Contributor instructions

- Sign every Git commit with the configured backend/key and verify the signature. Never bypass signing. If signing fails, preserve files uncommitted. Do not rewrite signed commits solely because verifier/trust configuration is missing.
- Use English commit messages.
- Preserve user data, frontmatter, readable links, and native Tasks semantics. Unsupported syntax or conflicting edits must fail without writing.
- Use established authenticated encryption implementations. Keep format versioning, recovery, and cryptographic parameters explicit.
- Never retrieve real 1Password secrets in development checks. Mock the CLI boundary, invoke without a shell, and never log secrets or child-process output.
- Do not deploy to a vault or convert existing notes without user authorization. Keep deployment paths and secret references out of versioned configuration.
- Static tests do not establish live Obsidian/Tasks behavior; the user handles live testing by default.
