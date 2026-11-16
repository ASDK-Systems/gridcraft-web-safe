# gridcraft-web-safe

An audited, re-hosted copy of [storytold/gridcraft](https://github.com/storytold/gridcraft)'s
web/WASM build, for embedding in [VARKIS](https://github.com/ASDK-Systems)'s Office
Studio instead of fetching it from storytold's own infrastructure at runtime.

See [`AUDIT.md`](./AUDIT.md) for what was checked and why this is a verbatim mirror,
not a modified build. GridCraft is dual MIT/Apache-2.0 - see `LICENSE-MIT` and
`LICENSE-APACHE`.

**Status:** live. The `v0.19.1` web build release asset is uploaded and VARKIS's own
`ensureWebBuild('gridcraft')` has been verified end-to-end against this repo's
`/releases/download/v0.19.1/` URL (download, SHA-256 verify, extract). Native
desktop/CLI binaries are not mirrored here; VARKIS downloads those straight from
storytold, still verified against the same pinned SHA-256 either way.
