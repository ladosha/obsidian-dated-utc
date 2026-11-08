# Dated (UTC)

A fork of [Dated](https://github.com/spwx/obsidian-dated) by spwx that writes its stamps in **UTC**. It writes `created` and `modified` date-times into the front matter of your notes, so they survive syncing, copying and git checkouts that reset file timestamps. Because the stamps are in UTC, they mean the same moment on every device, whatever time zone it's set to.

```yaml
---
created: 2026-11-08T22:36Z
modified: 2026-11-08T22:37Z
---
```

It has its own plugin id (`dated-utc`), so it can't be overwritten by an update to the original plugin. Don't run both at once: each would keep rewriting the other's stamps. Disable **Dated** before enabling **Dated (UTC)**.

## Behaviour

- **New notes** get both `created` and `modified`, set to the current time.
- **Edited notes** get `modified` updated once you stop typing for 5 seconds, so the file isn't rewritten under the cursor on every keystroke. A missing `created` is filled in at the same time, from the file system's created time. An existing `created` is never changed.
- **Arriving files**: writes in the first 2 seconds after a file appears (such as a copy finishing) don't count as edits.
- **Templates**: notes inside the core Templates plugin's folder are left alone, so their stamps don't leak into notes made from them.
- **Ignored files**: `CLAUDE.md`, `AGENTS.md` and `README.md` are left alone by default. See [Settings](#settings).
- Only Markdown notes are touched. The format is ISO 8601 in UTC, to the minute: `YYYY-MM-DDTHH:mmZ`.

Notes that already exist when you install the plugin aren't stamped until you edit them, or until you run the command below on them. Existing values in another format, such as local time from the original Dated, are left as they are until the note is next edited. Then `modified` is rewritten in UTC, but `created` is kept.

## Commands

- **Dated (UTC): Add timestamps**: adds `created` and `modified` to the current note if it lacks either, taken from the file system's created and modified times. Existing values are kept.

## Settings

- **Ignored files**: files the plugin never stamps, one per line. A bare name such as `README.md` matches that file in any folder. A path such as `Archive/Old.md` matches only that file. A path ending in `/`, such as `Archive/`, matches everything inside that folder. The default list is `CLAUDE.md`, `AGENTS.md` and `README.md`.

## Installation

The plugin isn't in the community directory, so install it with [BRAT](https://github.com/TfTHacker/obsidian42-brat). This works on desktop, Android and iOS.

1. In Obsidian, install and enable **BRAT** from **Settings → Community plugins → Browse**.
2. Run the command **BRAT: Add a beta plugin for testing**, enter `ladosha/obsidian-dated-utc`, and add the plugin.
3. Disable **Dated** if you have it. Then enable **Dated (UTC)** under **Settings → Community plugins**.

BRAT checks for new releases and updates the plugin.

Manually: download `main.js` and `manifest.json` from the [latest release](https://github.com/ladosha/obsidian-dated-utc/releases/latest) into `<vault>/.obsidian/plugins/dated-utc/`, then enable the plugin under **Settings → Community plugins**.

## Development

The source is `src/main.js`. [esbuild](https://esbuild.github.io) bundles it into the `main.js` that ships, which is why `main.js` is not in the repo.

```sh
npm ci        # install
npm run dev   # rebuild on change
npm run build # production build
```

## Releasing

Run the release script with the new version (no `v`), from a clean `main`:

```sh
npm run release -- 1.4.1          # bump, build, commit, tag
npm run release -- 1.4.1 --push   # ...and push main and the tag
```

It bumps `version` in `manifest.json` (and matches `package.json` to it), adds the same version to `versions.json` mapped to the manifest's `minAppVersion`, runs the production build, then commits and tags. The commit message opens in `$EDITOR` prefilled with `Bump to <version>` so the body can describe what changed; `--no-edit` keeps just that line. It refuses to run on a dirty tree, off `main`, when `main` and `origin/main` have diverged, or when the version is not newer than the current one.

Pushing the tag starts the Release workflow, which builds from source, checks the tag against `manifest.json` and `versions.json`, attests `main.js` and `manifest.json`, and publishes the GitHub release. BRAT picks the new release up from there.

## License

[MIT](LICENSE), from the original Dated by spwx.
