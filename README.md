# Natours — Advanced CSS and Sass course project

**This is not my design.** "Natours" is the flagship project of [**Advanced CSS and Sass**](https://www.udemy.com/course/advanced-css-and-sass/) by **Jonas Schmedtmann**. The visual design, the layout, the copy, the images, the icon font and the Sass file architecture are all his, taught in the course. What is mine is that I typed it, understood it, and finished it — between **16 February and 27 March 2020**.

I am recording it because completing a good CSS course is a real fact about that year. The only way to get it wrong would be to let it look original, so this paragraph is the first thing in the file.

---

## Provenance

> This repository was reconstructed in 2026 from a filesystem archive. The code was never under version control at the time.
>
> **Commit dates are the real modification times of the original files**, recovered with `stat`, accurate to the second. The work happened between **2020-02-16 08:42** and **2020-03-27 11:05** local time (UTC+03:30). One commits are dated 2026 — the README, plus any rendered previews or corrections. Each says which it is in its commit message.
>
> One limit worth stating: a modification time is not an authorship time. It records when a file was last written, which for a copied or re-saved file is later than when the work was done. This drive holds duplicated trees, so some files were certainly moved between them. These dates are therefore a faithful transcription of the filesystem's timeline and a **floor** on when the work happened — not independent proof of authorship on that date.

## Attribution

| Thing | Whose |
|---|---|
| Design, layout, colour, copy | Jonas Schmedtmann — *Advanced CSS and Sass*, Udemy |
| Sass architecture (`abstract` / `base` / `components` / `layout` / `pages`) | Jonas Schmedtmann, taught as the 7-1 pattern |
| `img/` — all 21 photographs, both logos, `video.mp4`, `video.webm` | Course starter assets, dated 2019-06-13, downloaded not made |
| `css/icon-font.css` and `css/fonts/linea-basic-10.*` | The **Linea** icon font, dated 2019-05-14, bundled with the course |
| Typing all 21 `.scss` partials from the videos, over six weeks | Mine |

`package.json` says `"name": "natours"`, `"author": "homayoon-asghari"`. The author field is the only original line in it, and it is the reason this repository needs a disclaimer at the top.

The course video files were still sitting on the same drive, at `tutorial/Design/`, when this archive was recovered.

## What it does

A one-page marketing site for a fictional outdoor tour company. The one build script is all there is:

```json
"scripts": { "compile:sass": "node-sass sass/main.scss css/style.css -w" }
```

`node-sass ^4.13.1` in `devDependencies`, watch mode, output straight into `css/style.css`. No bundler, no autoprefixer, no minifier, no task runner. 22 KB of `index.html` and 20 KB of compiled CSS.

## Layout

```
sass/
  abstract/    _variable  _function  _mixins
  base/        _base  _typography  _animations  _utilities
  components/  _button _card _composition _bg-video _popup _story _form _future-box
  layout/      _header _navigation _grid _footer
  pages/       _home
  main.scss    the manifest that imports all of them
```

Twenty-one partials and a manifest — the 7-1 pattern from the course, file for file.

## The parts worth having learned

The course picks its exercises well, and these are the ones that were genuinely new:

- **`_card.scss`** — the 3D flip card, `perspective` on the parent, `backface-visibility: hidden`, `rotateY(180deg)` on the back face.
- **`_composition.scss`** — the overlapping photo stack, absolutely positioned with `outline-offset` on hover so the hovered image lifts and the others scale down. This is the last file I touched, on 27 March, a month after the rest.
- **`_bg-video.scss`** — a full-bleed `<video>` behind content with an `object-fit: cover` filter overlay.
- **`_popup.scss`** — a modal built with `:target` and no JavaScript at all.
- **`_mixins.scss`** — a `respond($breakpoint)` mixin that turns raw pixel media queries into four names, `phone` / `tab-port` / `tab-land` / `big-desc`, expressed in `em` rather than `px` so they survive a browser zoom. Six months earlier I had hard-coded `width: 1366px` into my own portfolio because that was the width of my laptop. This was the lesson that fixed that.

## What is wrong with it

- **It is a transcription.** Six weeks of following videos. There is no design decision in this repository that I made.
- **`node-sass 4.13.1` is dead.** It was deprecated in 2020 and its native bindings do not build on any current Node. `npm install` will fail. Use `sass` (Dart Sass) instead — the `.scss` here is plain enough that it compiles unchanged.
- **`_function.scss` is a 0-byte file**, and `main.scss` imports it anyway on line 1.
- **`_future-box.scss` has a broken selector that shipped into the compiled CSS.** Inside the `&:hover` block:

  ```scss
  &:hover{
      transform: scale(1.1) translateY(-.2rem);
      &h3,p{ transform: scale(0) translateY(0); }
  }
  ```

  `&h3` concatenates rather than nesting, and the `p` after the comma is not prefixed by `&` at all. `css/style.css` line 272 is the result:

  ```css
  .future-box:hoverh3, .future-box:hover p { ... }
  ```

  The first selector matches nothing — there is no `:hoverh3` pseudo-class — and the second collapses every paragraph inside a hovered feature box to `scale(0)`. I copied it, compiled it, looked at it, and did not notice.
- **`main.scss` imports `abstract/variable` *after* `abstract/mixins`.** It happens to work because the mixins do not reference a variable at definition time. It is luck, not ordering.
- **The compiled `css/style.css` is committed.** It is build output and should not be in the tree, but it was on disk and removing it would misrepresent the archive. It is 20 KB, unminified, unprefixed.
- **`img/` is 22 MB**, 16 MB of which is one background video in two codecs — course assets, committed verbatim.
- No accessibility work at all: no `alt` text discipline, no focus states, no `prefers-reduced-motion`, and the `:target` popup traps nothing.
- A `debug.log` from a Chromium crash (`Insufficient system resources`) was sitting in the project root. It is excluded.

## Excluded

- `node_modules/` — restored with `npm install` if you can get `node-sass` to build, which you cannot.
- `debug.log` — an unrelated browser crash dump.

## Building

```
npm install -g sass
sass --watch sass/main.scss css/style.css
```

Then open `index.html`. The original `npm run compile:sass` will not work on a modern Node.
