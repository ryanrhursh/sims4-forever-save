# Sims 4 forever save: instructions for Claude

Repo for the owner's modded Sims 4 forever world (PC, all expansion packs). Served with GitHub Pages at https://ryanrhursh.github.io/sims4-forever-save/ (deploys from `main`, root; allow up to 10 minutes after a push).

## Files
- `index.html`: install-order checklist (batches b0 to b7, with test gates). Ticks live in the owner's browser (localStorage), so Claude cannot read or change them. Edit only when mod advice changes.
- `tracker.html`: viewer and editor. Never put data in it. The owner can turn on editing with a fine-grained GitHub token (Contents read/write on this repo) kept in their browser; Save then commits straight to `tracker-data.json` on `main`.
- `mods-manifest.json`: the last scan of the owner's real Mods folder, written by the page's Mods folder tab (file names, sizes and dates only, never mod files). Do not hand-edit it. It may not exist yet.
- `tracker-data.json`: the single source of truth for the tracker. Every tracker change is an edit to this file.

## Main workflow: session rundown
The owner opens a new session per task and pastes a rundown of what they did in a play session. Do this:
0. The owner also edits `tracker-data.json` from the web page, so run `git pull --rebase origin main` first and work from the latest file. If a push is rejected, pull again and redo the edit on top of theirs.
1. Read `tracker-data.json` (the previous state is not in your context).
2. Translate the rundown into edits, only for things the owner actually said:
   - Mod installed, tested, updated, broken or removed: set that mod's `status`, `version`, `testedOn` (game version), `lastChecked` (today, `YYYY-MM-DD`). Statuses: Not installed, Testing, Working, Needs update, Broken, Retired.
   - A problem: add to `issues` (date, symptom, version, suspect, test, rounds, fix, status Open or Resolved, resolved). Update the entry when it is later solved instead of adding a duplicate.
   - New game patch: add to `patches`, set top-level `gameVersion`, and flag mods whose `testedOn` is older (set to Needs update only if the owner says they are affected, otherwise leave the status and say they are untested on the new version).
   - Backup taken: add to `backups`.
   - New or changed Sims: edit `sims`. First row is the main Sim (restaurant owner and botanist).
   - Worlds and lots: `worlds` has one entry per world (`name`, `pack`, `description`, `languages`, `notes`, `screenshots`). `screenshots` is a list of `{"path": "images/worlds/<file>.jpg", "caption": ""}`; the page uploads them itself, so keep existing entries untouched. If the owner attaches screenshots in a session, resize to at most 1600 px on the long side, save as JPEG in `images/worlds/`, and add them. Never delete image files unless asked. `lots` has one entry per property (`name`, `world` matching a world `name` exactly, `type` such as Residential, Community or Restaurant, `size` as the owner states it, `tenants` as household or Sim names, `description`, `notes`, `screenshots` using the same format with paths under `images/lots/`). Add a world before its lots. When tenants move, update the lot's `tenants` and the matching Sim's `homeWorld`/`household`.
   - Mods folder check: each mod may have `match`, a list of lowercase keywords with no spaces or punctuation (for example `wickedwhims`). The page matches them as substrings against the normalized path of every file in the scan. An empty list means the mod lives outside the Mods folder. Top-level `modsIgnore` lists folder or file names (normalized) to skip, such as `cc`. If `mods-manifest.json` exists, cross-check it against mod statuses and report mismatches, but only change statuses the owner states.
   - Add one entry to `sessions` every time: `date`, `summary` (their words, short), `changes` (what you edited).
   - Mods not in the list: add a row with a unique kebab-case `id`, and a batch and category that match the others.
3. Set `updated` to today. Keep the JSON valid and pretty-printed with two-space indents and a trailing newline, the same format the page writes, so diffs stay small. Do not rename or remove keys: the page reads them by name. Blank fields are empty strings.
4. Commit and push to `main`. Reply with a short list of what changed, anything ambiguous you left alone, and anything that looks stale (installed mods not checked in more than `staleDays`).

## Rules
- Do not invent versions, dates, test results or statuses. If the rundown does not say it, leave the field alone and ask.
- Use absolute dates in `YYYY-MM-DD`. Use the date of the play session if the owner gives one, otherwise today.
- Statements about whether a mod works on a patch come from the owner or from a source you fetched this session. Cite the source in `notes`. Do not rely on memory for current mod status.
- Mod facts: the owner's game build was 1.128.90.1030 (DX11) on 2026-10-12. The EA forum "Broken and Updated Mods" thread for each patch is the first place to check.
- Script Mods Allowed in game settings resets after each patch. Remind the owner when logging a patch.
- Keep `index.html` text plain and free of claims you have not checked. Roommate Stories is optional because the game has its own roommate feature.
- Do not commit `.xlsx` copies or other generated duplicates of the data.

## Git
- Commit messages: short imperative summary, then the attribution trailers the session asks for.
- If `gh` auth fails, use the repo connection (add_repo with push access) and plain `git push`.
- After a push that changes the live pages, fetch the live URL once to confirm it deployed.
