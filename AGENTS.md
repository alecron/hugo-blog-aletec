# AGENTS.md

Hugo blog published at https://aletecnstuff.com via Netlify. Theme: Blowfish (Hugo module).

## Writing articles

### NEVER use em-dashes

Em-dashes (`—`) must **never** appear in articles. This is a hard rule, in every
language, in body text, titles, descriptions and front matter alike.

Use a comma, parentheses, a colon, or simply start a new sentence. Do not swap an
em-dash for an en-dash (`–`) either, that defeats the point.

Check before committing:

```sh
grep -n '—\|–' content/posts/*/*.md   # must return nothing
```

### Voice

Articles are first-person and conversational. Keep the author's colloquialisms
(including things like "xd") rather than formalising them. Fix only orthography:
Spanish needs its opening `¿` and `¡`, and correct accents.

## Languages

Spanish is the default language and is served from the root. English lives under
`/en/`.

- **Every content file must carry an explicit language suffix**: `index.es.md`,
  `index.en.md`. A file without a suffix is treated as *Spanish*, so an English
  post named `index.md` would be served with `lang=es`, and `index.md` alongside
  `index.es.md` collides in the same language.
- Both translations share one page bundle directory, so images in the bundle are
  available to both.
- Each translation sets its own `slug`, which is what decides the URL. The bundle
  directory name does not. **Never change an already published slug**, it breaks
  the live URL.
- Config is split per language: `config/_default/languages.{es,en}.toml` and
  `menus.{es,en}.toml`. Adding a language means adding both files.

## Front matter

TOML, delimited by `+++`.

`buildFuture = false`, so a post dated even slightly in the future is **silently
dropped from every language** with no error. Watch the time and the UTC offset:
Netlify builds with `TZ=UTC`.

## Netlify / build

Blowfish v3 compiles SCSS, so it needs the **extended** Hugo build, and it pins a
supported range of `0.162.0` to `0.165.0`. Netlify installs `HUGO_VERSION` via
binrc, which makes no promise about giving you the extended build, so
`netlify.toml` downloads the exact extended binary itself and calls it directly.
Do not "simplify" that back to a bare `HUGO_VERSION`.

Never put `hugo mod get -u` in the build command. Hugo downloads the modules in
`go.mod` at build time on its own, and `-u` walks the theme forward to a version
the pinned Hugo cannot run (this is what once broke the deploy with
`function "try" not defined`).

`GO_VERSION` must stay set: Hugo uses the go tool to fetch modules.

Current pins: Hugo `0.165.0+extended`, Blowfish `v3.6.0`.

The theme's major version is part of the Go module path, so `hugo mod get -u`
only ever walks within the current major. Moving to v4 later means editing the
import path in `config/_default/module.toml`.

## Config keys that moved

- Hugo 0.158 renamed `languageCode` to `locale` and `languageName` to `label`.
  Both language files and the root `hugo.toml` use the new names.
- Blowfish v3 dropped the `customCSS` param. `assets/css/custom.css` is picked up
  automatically and concatenated into `css/main.bundle.min.*.css`, so it will not
  appear as its own `<link>` tag.

## Article features

- **Thumbnails**: drop a `featured.png` (or `.jpg`) into the post's bundle
  directory. Both translations share it. `[article] showHero` with
  `heroStyle = "thumbAndBackground"` renders it at the top, and
  `[list] showCards` puts it on the cards. A post with no `featured.*` simply gets
  no thumbnail.
- **Videos**: use `{{< youtubeLite id="VIDEO_ID" label="Title" >}}`. It renders a
  `lite-youtube` element that loads only a thumbnail until clicked, rather than a
  full iframe.
- **Reading progress bar**: `[article] showReadingProgress = true`.
- The theme ships 46 shortcodes (`gallery`, `carousel`, `timeline`, `steps`,
  `chart`, `mermaid`, `katex`, `alert`, `badge`, `figure`, `tabs`, ...). Check
  `layouts/shortcodes/` in the theme module before hand-rolling markup.

## Gotchas

- The homepage uses a local override, `layouts/partials/home/custom.html`. It
  renders the author's name, headline and links, but **not** `author.bio`. The bio
  only shows in the author box under articles. This is intentional.

## Verifying locally

There is no Hugo in the repo tooling, and the extended build has to be compiled
with CGO:

```sh
CGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@v0.165.0
hugo --minify -b https://aletecnstuff.com/ -d /tmp/pub
```

A clean run prints no `WARN` lines. Confirm both languages appear in the build
table and that the expected URLs exist. To reproduce what Netlify actually does,
build from a fresh checkout with a cold module cache:

```sh
git archive --format=tar "$(git write-tree)" | tar -x -C /tmp/fresh
cd /tmp/fresh && HUGO_CACHEDIR=/tmp/coldcache hugo --gc --minify -b https://aletecnstuff.com/
```
