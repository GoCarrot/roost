# GoCarrot fork (teak-hq)

This checkout is GoCarrot/roost, a fork of AvesAlight/roost managed in arboretum and used from teak-hq at
`tooling/roost`. This file exists on the fork only; it never goes upstream.

## Commit format

Commits here use roost's own format from CLAUDE.md ("Committing"), on the fork and upstream, since every
change ends up upstream. teak-hq's own commit-message block doesn't apply. Don't "fix" it to match.

## Fork

The fork carries symlinks that replace roost's own `script/worktree`, `.claude/agents`, and
`.claude/commands/worker.md`, so fork main has commits upstream doesn't. Roost's CLAUDE.md rules otherwise
hold: tests bring their own ergo, and `brew upgrade` and dispatcher restarts stay operator steps. Never
touch the installed roost, the shared ergo, or the shared dispatcher.

- Tracking: the GitHub issue on AvesAlight/roost is authoritative; Linear just links to it.
- Fork-only changes (anything teak-hq-specific): PR to GoCarrot/roost main with
  `gh pr create --repo GoCarrot/roost`. On a fork `gh` defaults to the upstream, so always pass `--repo`.
- Upstream PRs: never cut from fork main. Add the remote once (`git remote add upstream
  git@github.com:AvesAlight/roost.git`), then `git fetch upstream` and
  `git worktree add -b <branch> tooling/roost-<branch, slashes as dashes> upstream/main`. In that tree run
  `script/set_worktree_identity tooling/roost <tree>` and `bun install`. It has none of the symlinks, so
  no teak-hq paths, issue IDs, or conventions can leak in. Fresh commits, roost format. Push the branch to
  the fork and open it with `gh pr create --repo AvesAlight/roost --head GoCarrot:<branch>`. A tree cut
  from `upstream/main` tracks upstream, so a bare `git push` would aim there: push with an explicit
  refspec and `-u origin` (`git push -u origin <branch>:<branch>`). Never force-push.
- Fork sync: when an upstream PR merges, `git fetch upstream`, merge `upstream/main` into a branch off fork
  main, and PR it to fork main as a merge commit. Never force-push. If upstream touched `script/worktree`
  (it does, about monthly), `.claude/agents`, or `.claude/commands/worker.md`, keep the fork's side.

## Spawning teak-coord

teak-coord is a long-running agent, so it follows the PM-class flags in `roost spawn --help` ("Agent class guidance"):

```bash
roost spawn teak-coord --agent teak-coord --cache-ttl 1h --steer-compact \
  --channels '#teak-coord,#sdk-planning,#teak-heavy' \
  --ask-irc '#teak-coord' --ask-target alex
```

The agent definition is `.claude/agents/teak-coord.md` in teak-hq, and `--agent` resolves against the cwd's `.claude/agents/`. Run the recipe from a teak-hq checkout that has the file merged, or pass `--cwd` pointing at one.
