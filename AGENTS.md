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

The build command must **not** contain `hugo mod get -u`. That flag upgrades
Blowfish past what `HUGO_VERSION` supports and the build dies with
`function "try" not defined`. Hugo already downloads the modules declared in
`go.mod` at build time, so no explicit fetch step is needed.

Current pins: `HUGO_VERSION = 0.136.2`, Blowfish `v2.78.0` in `go.mod`. Upgrading
the theme to v2.106.0+ requires Hugo **extended** (`module.toml` currently
declares `extended = false`) and would need `layouts/partials/home/custom.html`
revalidated against the newer theme partials.

## Gotchas

- `resources/_gen` contains files tracked by git even though `resources/` is in
  `.gitignore`. `hugo --gc` deletes them. Run `git checkout -- resources/` before
  staging, or they land in the commit as deletions.
- The homepage uses a local override, `layouts/partials/home/custom.html`. It
  renders the author's name, headline and links, but **not** `author.bio`. The bio
  only shows in the author box under articles. This is intentional.

## Verifying locally

There is no Hugo in the repo tooling. Install the version Netlify pins and build:

```sh
go install github.com/gohugoio/hugo@v0.136.2
hugo --minify -b https://aletecnstuff.com/ -d /tmp/pub
```

Confirm both languages appear in the build table and that the expected URLs exist.
