# myblock

Personal Adblock Plus-syntax filter list for Vivaldi's built-in blocker: small fixes that
make specific sites nicer to use (anti-adblock walls, nag overlays, recovery popups).

## Subscribe in Vivaldi

Add this URL as a custom source under Settings > Privacy and Security > Tracker and Ad Blocking
> Manage Sources:

```
https://raw.githubusercontent.com/mmascherpa/myblock/main/myblock.txt
```

Then tick **Allow List to Use ABP Snippet Rules** for it, because some fixes use `#$#` snippets.

## Layout

```
rules/
  sites/      one file per site or publisher, e.g. rcs-gazzetta-corriere.txt
  generic/    rules that apply across many sites, e.g. cookie-walls.txt
  _template.txt
scripts/
  build.py    merges rules/ into myblock.txt and lints it
  pre-commit  optional git hook that runs the build
myblock.txt   GENERATED, the file Vivaldi subscribes to. Do not edit by hand.
```

## Adding a rule

GitHub `main` is the source of truth, so edit from anywhere: Claude on your phone (with GitHub
connected), the GitHub web editor, or the laptop.

1. Find the culprit: DevTools > Network for the script or request to block, Elements for the
   overlay to hide. On a phone, describe the symptom and the site to Claude.
2. Add to the matching `rules/sites/<site>.txt`, or copy `rules/_template.txt` to a new file.
   Fill in the `! Sites / Problem / Added / Notes` header.
3. Commit to `main`. The **build** GitHub Action lints the rules, rebuilds `myblock.txt` and
   commits it. If a rule is invalid the Action fails and the published list stays unchanged.
4. Vivaldi picks it up on its next refresh (it doesn't publish a schedule; users report every
   couple of days). To get it immediately: Settings > Privacy > Manage Sources, untick and
   re-tick the list. raw.githubusercontent.com may also serve the old file for ~5 minutes.

On the laptop, always `git pull --ff-only` before editing: the Action adds its own commits, so a
stale checkout will conflict. The `.claude/settings.json` hook does this automatically for
Claude Code sessions.

## Conventions

- Scope every rule to the site it fixes: `example.com##.popup`, `$domain=example.com`.
  The build rejects unscoped cosmetic rules in `rules/sites/`.
- Order of preference: network block (`||host/path`) > snippet (`#$#`) > cosmetic (`##`).
  Blocking the script is more robust than hiding what it renders.
- Use ABP syntax only. The build rejects uBlock Origin scriptlets (`##+js`), AdGuard
  `#%#` rules and procedural filters (`:has-text`, `:xpath`, ...). Vivaldi doesn't run them.
- When a rule stops being needed, delete it rather than commenting it out; git keeps the history.

## Local build (optional)

```
python3 scripts/build.py          # rebuild myblock.txt
python3 scripts/build.py --check  # lint + verify it's up to date
ln -sf ../../scripts/pre-commit .git/hooks/pre-commit   # rebuild on every commit
```
