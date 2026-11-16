# gridcraft-web-safe

An audited, re-hosted copy of [storytold/gridcraft](https://github.com/storytold/gridcraft)'s
web/WASM build, for embedding in [VARKIS](https://github.com/ASDK-Systems)'s Office
Studio instead of fetching it from storytold's own infrastructure at runtime.

See [`AUDIT.md`](./AUDIT.md) for what was checked and why this is a verbatim mirror,
not a modified build. GridCraft is dual MIT/Apache-2.0 - see `LICENSE-MIT` and
`LICENSE-APACHE`.

**Status:** audit complete. The `v0.19.1` web build asset is being uploaded to this
repo's own release next (`gh release create` - the binary is too large for a
file-content API call). Native desktop/CLI binaries are not mirrored here; VARKIS
downloads those straight from storytold, still verified against the same pinned
SHA-256 either way.
