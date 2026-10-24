# myblock: notes for Claude

ABP-syntax filter list for Vivaldi's built-in ad blocker. See README.md for layout and conventions.
GitHub (mmascherpa/myblock, branch `main`) is the source of truth. Vivaldi subscribes to
https://raw.githubusercontent.com/mmascherpa/myblock/main/myblock.txt

## Before any change: sync first
`main` moves often: the maintainer edits from several machines and a GitHub Action commits
the rebuilt list.
- Local checkout: the SessionStart hook runs `git pull --ff-only`. If it reported a
  failure, or you're unsure, run `git fetch origin && git status -sb` and `git pull --ff-only`
  before editing. If the pull fails, stop and tell the user; never force-push or reset away others' work.
- Cloud sessions start from a fresh clone, so they are already current.

## Making a change
- Edit files in `rules/` only. Never edit `myblock.txt` by hand.
- New site → new file `rules/sites/<site-or-publisher>.txt` copied from `rules/_template.txt`,
  with the `! Sites / Problem / Added / Notes` header filled in.
- Run `python3 scripts/build.py`; it must succeed. Commit the rebuilt `myblock.txt` together with
  the rule change: the PR check (`build.py --check`) fails if it is stale.
- Changes reach Vivaldi only once they are on `main`. Small, descriptive commit messages,
  e.g. "gazzetta: block new adblock CDN path".
- Cloud sessions, and anyone without push access to `main`: push a branch and open a PR.
  PRs from `claude/*` branches of this repo (not forks) are merged automatically once the check
  passes. If the auto-merge fails (e.g. a conflict on `myblock.txt`), tell the user.

## Rule syntax
- Vivaldi runs ABP network rules, element hiding (`##`) and ABP snippets (`#$#`, only when the
  list is allowed to use snippets). It ignores ABP extended CSS (`#?#`) and does not run uBO or
  AdGuard-specific syntax. Prefer a network block, then a snippet, then a cosmetic rule.
- Always scope rules to the target domains.
