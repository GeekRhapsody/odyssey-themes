# Odyssey Launcher themes

This is the list of themes [Odyssey Launcher](https://github.com/GeekRhapsody/OdysseyLauncher)'s theme downloader offers (Settings, Look, **Download themes**). The launcher clones this repository when the downloader opens and reads `themes.json` and the screenshots. Each theme is a git repository of its own, which the launcher clones into the user's `themes/` folder from its default branch, and updates from it when the user asks, as ES-DE does.

The launcher's owner tests every theme before it's listed, and moderates the list: a theme is added, updated or removed only by a change here.

## What's here

| Path | What |
|---|---|
| `themes.json` | The list, format 1. Comments and trailing commas are allowed. |
| `screenshots/<id>/` | Each theme's screenshots. |
| `.github/workflows/validate.yml` | The checks every pull request runs (below). |

## Making a theme

[docs/THEMING.md](https://github.com/GeekRhapsody/OdysseyLauncher/blob/main/docs/THEMING.md) in the launcher's repository is the guide, and its section 12, "Publishing a theme", is what a listed theme needs. In short:

- `theme.toml` is at the top of your repository: the repository *is* the theme folder.
- The history is small, since every user downloads all of it. Commit models when they're finished, and squash early work before you submit.
- Binary files are marked binary in a `.gitattributes` (`*.glb binary`, `*.png binary`, `*.webp binary`, `*.mp4 binary`).
- It loads without errors. From the launcher's repository, after `dotnet build`, check it with the command the list's owner runs, with the folder named after the id you want:

  ```
  .\tools\scrape-cli\bin\Debug\net8.0\odyssey-scrape.exe inspect-theme --json <your theme's folder>
  ```

## Submitting a theme

Open an issue or a pull request with:

- the repository's https URL: users get its default branch
- the id: the folder it's installed in, in lower-case letters, digits, `_` and `-` (`neon-arcade`), and not a built-in theme's (`memory-card`, `console`, `slab`)
- its name, your name as the author, and a sentence or two of description
- the oldest launcher version it needs, if it uses something new (`min_app_version`): an older launcher shows the theme but won't install it
- two to four screenshots, 16:9, at least 1280 × 720: the systems and a system's games at least, taken with `--capture` (THEMING.md section 10). WebP is smallest; PNG and JPEG are fine. The list shows the first at 192 × 108, and the details show them all full screen.

In a pull request, add the entry to `themes.json` and the screenshots to `screenshots/<id>/` (`01.webp`, `02.webp`, ...). In an issue, attach the screenshots and the owner adds them. Leave out `covers`, `new` and `version` if you like: the owner fills them in.

An entry looks like this:

```jsonc
{
  "id": "neon-arcade",
  "name": "Neon Arcade",
  "author": "Your Name",
  "description": "Arcade cabinets for every system, under neon light.",
  "url": "https://github.com/your-name/odyssey-neon-arcade.git",
  "version": "1.0.0",
  "min_app_version": "0.16.0",
  "new": true,
  "covers": { "system_cards": 20, "templates": 1, "menus": true, "card_videos": false, "game_videos": false },
  "screenshots": [
    { "image": "screenshots/neon-arcade/01.webp", "caption": "Systems" },
    { "image": "screenshots/neon-arcade/02.webp", "caption": "PlayStation 2's games" }
  ]
}
```

| Key | What |
|---|---|
| `id` | The folder it's installed in, `themes/<id>/`. Required. |
| `name`, `author`, `description` | Shown in the list and the details. `name` is required. |
| `url` | The theme's git repository, https: users get its default branch. Required. |
| `version` | Shown in the details only. |
| `min_app_version` | The oldest launcher that can use it. |
| `new` | Shown as new. |
| `deprecated` | Shown faded, as leaving the list soon. |
| `covers` | Optional: what it brings, as `inspect-theme --json` prints it: system cards and logos, templates, its own menus, card and game videos. Without it, the theme's details just don't say. |
| `screenshots` | Each one's `image` (a path in this repository) and `caption`. |

## What the owner does

Before a theme is listed, the owner installs it from the list, tries it in the launcher, and runs `inspect-theme`. Only the owner accepts pull requests to this list. A theme can be turned down, or listed later, for any reason; the owner says why in the issue or pull request.

## Updates

Once your theme is listed, its updates are yours, as with ES-DE's themes: whatever you push to its default branch reaches users the next time they press A on it in Download themes. Nobody reviews updates, so keeping the theme working is up to you: try changes before you push them (on a branch of their own while you work), and don't rewrite the default branch's history. A version a user's launcher can't load is refused there, and they keep the one they had. Open an issue or pull request for new screenshots, a new description or a new `version`.

A theme that's no longer kept up is marked `deprecated` for a while, then taken off the list. Users who have it keep it.

## The checks

Every pull request runs `.github/workflows/validate.yml`, which:

1. Reads `themes.json`: format 1, every entry's keys, ids that are valid, unique and not a built-in theme's, https URLs, and two to four screenshots per theme that exist in `screenshots/<id>/`, 16:9 and at least 1280 × 720.
2. Builds the launcher's `odyssey-scrape` from its `main` branch and reads the list the way the launcher does (`odyssey-scrape --themes-list=. themes list`), failing on any warning.
3. Installs each theme from its default branch as the launcher does (`themes install <id>`), runs `inspect-theme --json` on it, and checks its `covers` match when it declares them.

## Trying a list locally

The launcher and `odyssey-scrape` read a list from a folder with `--themes-list=<folder>`, and a local list may name a repository by a `file:///` URL, so you can install your theme the way users will before you push it. From the launcher's repository:

```
.\tools\scrape-cli\bin\Debug\net8.0\odyssey-scrape.exe --user-dir=<an empty folder> --themes-list=<this folder> themes list
godot --path godot -- --user-dir=<absolute folder> --open=themes --themes-list=<absolute folder with themes.json>
```
