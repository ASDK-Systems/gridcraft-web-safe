# Audit: gridcraft-web v0.19.1

This repo re-hosts GridCraft's web/WASM build for embedding in [VARKIS](https://github.com/ASDK-Systems)'s
Office Studio, and this file records a static security audit of that build, done
before it was uploaded here. Companion to [wordcraft-web-safe](https://github.com/ASDK-Systems/wordcraft-web-safe)'s
own `AUDIT.md`, same methodology.

## What was checked

Downloaded `gridcraft-web-0.19.1.zip` directly from
[storytold/gridcraft](https://github.com/storytold/gridcraft)'s `v0.19.1` release,
verified it against that release's own `SHA256SUMS.txt`, then read every line of:

- `index.html` (1.7 KB)
- `gridcraft-web-fdc723373f2aa0d8.js` - the wasm-bindgen glue (174 KB)
- `snippets/eframe-d31a8450ea12dfc1/inline0.js` (3 lines)
- `_headers`, `HOSTING.md`

and grepped the raw `.wasm` binary's string table (26 MB) for any `http(s)://`-shaped
string, since the glue JS is the only surface through which compiled Rust can reach
the network at all - a `.wasm` module has no ambient network access of its own.

## Findings

**No outbound network call reachable from any JS import.** The glue JS has exactly
one `fetch()` call - the module's own same-origin load of its `.wasm` file, the normal
wasm-bindgen `init()` sequence - and one `.src =` assignment, which is a WebGPU/canvas
texture binding, not a content element. `postMessage` appears only as a generic
wasm-bindgen wrapper around the browser's own `window.postMessage` API, used by the
optional `?host=parent` embedding bridge documented in `HOSTING.md`; the exact
`targetOrigin` string it's called with is constructed inside the compiled `.wasm`,
not visible as a literal in the glue JS. VARKIS does not use this cross-origin mode -
the iframe is always same-origin - and VARKIS's own listener checks the message's
`source`/`origin` before acting on it, which is the actual mitigation here, not an
audit finding about GridCraft's own choice of target.

**Spreadsheet functions that could reach the network are explicitly disabled in this
build.** `WEBSERVICE` and `FILTERXML` both exist in the function catalog (515 functions
total, confirmed via a live `list_functions` call against the real engine) but their
own descriptions say "(not available; returns #VALUE!)" - calling either returns an
error, never a request.

**No Python/Pyodide/CDN dependency.** Grepped the glue JS for `pyodide`, `python`,
`cdn.`, `jsdelivr`, `unpkg`, `cloudflare` - zero matches.

**The `.wasm` binary's only URL-shaped strings are inert.** OOXML/XML namespace
constants (`schemas.microsoft.com`, `schemas.openxmlformats.org`, `w3.org` - required
by the `.xlsx` file format itself, never fetched) and a handful of static
about-box/panic-message links (GitHub issue URLs for `egui`/`wgpu`, one MDN
documentation link, the upstream project's own site and Discord). None of these are
reachable from any JS import - they're baked into error/about text the Rust code
prints or displays, not network targets.

**Conclusion: nothing to strip.** Like `wordcraft-web-safe`, this is a verbatim
mirror of upstream's own build, not a modified one - no Apache-2.0 4(b) "changed
files" notice applies.

## What this repo hosts

`LICENSE-MIT` and `LICENSE-APACHE` are copied verbatim from
`storytold/gridcraft@v0.19.1`. The `v0.19.1` web build release asset is live:
uploaded via `gh release create`, then re-downloaded fresh and confirmed byte-for-byte
identical to the pinned SHA-256 both before upload and after. VARKIS's own
`ensureWebBuild('gridcraft')` was run directly against this repo's release URL
(cache cleared first) and verified it downloads, verifies and extracts correctly.
