# Passengers

An Observable Notebook Kit project. The notebook is [nbks/index.html](nbks/index.html); the page template and all custom CSS live in [nbks/custom.tmpl](nbks/custom.tmpl).

```sh
npm run preview   # live preview
npm run build     # static build into dist/
```

## Layout of `#line`

`#line` holds the morphing text. `lineLetters` fills it (via `innerHTML`) with one `<span id="text{i}">` per character position, each containing two absolutely positioned `.text` spans (`.visible` and `.invisible`) at `left: calc(1ch * (i + 1))`.

The row is centered in the viewport, horizontally and on the text baseline vertically:

- **Horizontal.** `#line` is `position: fixed` with `left: 0; right: 0; margin-inline: auto` and `width: calc(var(--len) * 1ch)`. `--len` is the length of the longest line. The notebook sets it on `#line` just before filling the row ([nbks/index.html](nbks/index.html), in `morph`). The block has a fixed width, so it doesn't move while lines morph; shorter lines stay left-aligned inside it. `transform: translateX(-1ch)` cancels the 1ch offset of the glyph spans.
- **Vertical.** `#line` has `top: 50%` and `height: 0`. Each `#line span.text` uses `text-box: trim-end text alphabetic`, which trims the bottom of its box to the alphabetic baseline, then `transform: translateY(-100%)` lifts it so the baseline sits on `#line`'s top edge, i.e. the vertical midpoint of the viewport. Nothing depends on font metrics.
- **`.text:before`.** The gradient/border box is pinned to its original geometry (`top: .075em; height: 1.35em`, i.e. 5% / 90% of the untrimmed 1.5em line box) so trimming doesn't shrink it.
- **Browser support.** `text-box` needs Chrome 133+ or Safari 18.2+. Firefox doesn't support it, so the baseline will be about half a line-height off there.

The old `margin: auto`, `width: calc(100vw - 4vw)`, `top: 35%`, `padding-top`, `min-height` and `background-color` on `#line` were removed. They didn't center anything: an absolutely positioned box needs both `left` and `right` for auto margins to center it.

## Fonts

All fonts are loaded from the network (Google Fonts) through a single `<link>` in the template: Noto Sans Mono (used by `#line`), plus Inter, Source Serif 4 and Spline Sans Mono for the Notebook Kit's own styles.

- The `@fontsource-variable/*` imports were removed. They made the build bundle local `.woff2` files into `dist/assets`.
- The kit's `--serif`, `--sans-serif` and `--monospace` CSS variables are overridden in the template to use the network family names (`"Source Serif 4"`, `"Inter"`, `"Spline Sans Mono"`). The rest of the kit CSS (`observable:styles/index.css`) is used as is.
- The duplicate `observable:styles/index.css` import was removed.
- Not changed: `dist/index.html` also loads `@observablehq/inputs` CSS from jsdelivr, which is a network resource too.

## Notes

- `dist/` was built before these changes (it has no Google Fonts link); run `npm run build` to refresh it.
- `package.json` now has `@observablehq/notebook-kit` at `latest` (was `^2.1.3`) and no longer lists `esbuild`. This change was already in the working tree and isn't part of the layout/font work.
