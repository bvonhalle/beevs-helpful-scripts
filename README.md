# beevs-helpful-scripts

Small scripts for everyday developer housekeeping.

## Scripts

### `git/clean-github-branches`

Reports stale local Git branches and optionally deletes local branches that are
confirmed merged.

It catches two common cases:

- branches Git can prove are merged into a base ref, such as `origin/main`
- branches whose matching GitHub pull request is `MERGED`, including squash or
  rebase merge workflows that plain `git branch --merged` can miss

It only deletes **local branches**. It does not delete remote branches or GitHub
pull requests.

## Install

```bash
mkdir -p ~/.local/bin
cp git/clean-github-branches ~/.local/bin/clean-github-branches
chmod +x ~/.local/bin/clean-github-branches
```

Make sure `~/.local/bin` is on your `PATH`.

Optional zsh helper:

```zsh
function cleanbranches() {
  clean-github-branches "$@"
}
```

## Usage

From inside a Git repository:

```bash
clean-github-branches
clean-github-branches --delete-merged
clean-github-branches --base origin/main --stale-days 60
```

The default run is a dry run. Use `--delete-merged` to delete only branches that
are confirmed merged by Git ancestry or GitHub PR state.

## Keep List

Some old branches are intentionally kept. Add them to:

```text
~/.config/clean-github-branches/keep
```

Example:

```text
design/new-homepage-concept
experiment/search-index-rewrite
```

Branches in the keep list are suppressed from stale reports, but if they later
become confirmed merged, the script can still report/delete them as merged.

See [examples/branch-keep-list](examples/branch-keep-list).

## Requirements

- `git`
- `gh` for GitHub PR-state checks
- authenticated GitHub CLI access via `gh auth login`

If `gh` is unavailable or unauthenticated, the script still runs using local Git
checks, but it cannot detect squash-merged GitHub PR branches.

## Safety

The script protects:

- the current branch
- branches checked out in any worktree
- protected branch names such as `main`, `master`, `test`, `prod`, and `develop`
- anything not confirmed merged unless you manually inspect it

Run the dry-run output first. Local branch cleanup is useful, but it deserves a
tiny speed bump.
