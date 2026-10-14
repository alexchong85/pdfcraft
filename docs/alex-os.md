# Alex OS Web customization

Based on upstream v0.8.0, commit `9931329b9fae0b05373b4dc3ac7c58541f0891a4`.

The web app defaults to Traditional Chinese when no explicit language was saved. The Shiro palette uses warm paper `#f7f4ee`, ink `#1f1f1f` and sage buttons; Yakan uses `#151515`, `#22211e` and `#e8e3d8`. Brand graphics are replaced by an original window outline drawn in Rust. PdfCraft attribution and license notices remain.

## Website bridge

The iframe accepts `?host=parent&theme=shiro` or `theme=yakan`. The same-origin parent can send `{ type: "pdfcraft:theme", theme: "shiro" | "yakan" }`. Explicit editor theme changes are reported as `pdfcraft:theme-changed` with the same theme values. Updates are applied between frames without reloading the app or losing open documents. Unknown values and messages from other windows or origins are ignored. Existing file-open, save, ready, failure and dirty messages remain supported.

## Reproduce the build

Use Rust 1.95.0, the `wasm32-unknown-unknown` target and Trunk v0.21.14. Use the existing lockfile.

1. Check out `storytold/craft-fonts` at `3d9ab3a132ed0eff457e424226a435a901da30d3` outside tracked source (the ignored `craft-fonts/` directory works).
2. Download `https://github.com/lxgw/LxgwWenkaiTC/releases/download/v1.522/LXGWWenKaiTC-Regular.ttf` into `craft-fonts/fonts/alex-os/`. Verify SHA-256 `b1a0795862c1415bf3f393ea50b2a4ea6275012cf5bad3f94feeb1222f555731`.
3. Keep `https://raw.githubusercontent.com/lxgw/LxgwWenkaiTC/v1.522/OFL.txt` alongside it. This Fontworks Klee One derivative is OFL licensed, not a Source Han/Noto CJK derivative. No font files are committed to this repository.
4. Append this row to that build input's `fonts/manifest.txt`:

```text
LXGW WenKai TC | Regular | fonts/alex-os/LXGWWenKaiTC-Regular.ttf | Hans,Hant | OFL-1.1 | fonts/alex-os/OFL.txt | b1a0795862c1415bf3f393ea50b2a4ea6275012cf5bad3f94feeb1222f555731 | https://github.com/lxgw/LxgwWenkaiTC/releases/tag/v1.522
```

5. Set `CRAFT_FONTS_DIR` to the absolute build-input directory. Run `trunk build --release --locked` from `apps/pdfcraft-web`. Output is `dist/web/`.
6. Distribute the HTML, generated JS/WASM, required license notices and `OFL-LXGW-WenKai-TC.txt`. Keep a manifest of the source commit and generated asset hashes in the website.

The added fallback is for the interface. This customization does not change the PDF engine's text-writing font selection or claim to fix Chinese text appearances in PDFs.
