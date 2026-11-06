# Roadmap

1. Freeze supported syntax with representative synthetic fixtures and a dry-run transformation preview. Resolve headings, anchors, tags, and Tasks metadata rules.
2. Specify the versioned comment envelope and implement one reviewed authenticated encryption profile with standalone recovery and negative tests.
3. Implement a portable manual secret provider, then a mocked/tested desktop 1Password CLI adapter. Evaluate the second encryption profile independently.
4. Add lock rendering, in-memory reveal/edit, transactional encrypted saves, and conflict handling.
5. Verify native and queried Tasks edits, frontmatter indexing, links, Live Preview, and Reading view in a disposable vault.
6. Add migration/recovery documentation, CI, packaging, and user acceptance before any production-vault deployment.

No automatic plugin installation, no production-note conversion, and no real-secret retrieval are part of repository initialization.
