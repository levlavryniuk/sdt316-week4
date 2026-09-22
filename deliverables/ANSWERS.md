# SDT316 Week 4 — Git Deliverables

- **Repository:** https://github.com/levlavryniuk/sdt316-week4
- **Pull request:** https://github.com/levlavryniuk/sdt316-week4/pull/1
- **Branches pushed:** `main`, `feature-merge`, `feature-rebase`, `tidy`
- Screenshot of the PR Commits tab: [`pr-commits-tab.png`](pr-commits-tab.png)

---

## Task 1 — Merge a conflicting branch

**The merge commit:** `Merge branch 'feature-merge' into main` (in `task1-graph.txt`).

- It has **two parents**: `eace763` (`feature: update line 2`) and `60c5f96` (`main: update line 2`).
- You can tell history forked and rejoined because the graph shows two lines leaving the same point
  and coming back together: `|/` sits just above `bc3c7ec initial commit` where the lines split, and
  the `|\` under the merge commit is where they rejoin into that single two-parent commit.

```
*   7ce2433 Merge branch 'feature-merge' into main
|\
| * eace763 feature: update line 2
* | 60c5f96 main: update line 2
|/
* bc3c7ec initial commit
```

The conflict on `app.txt` line 2 was resolved so both intentions survive: `line 2 (main) (feature)`.

---

## Task 2 — Rebase instead

SHA before rebase: `df7f42a` — SHA after rebase: `895d1ca` (both `feature: add feature.txt`).

**Why did the SHA change even though the file contents did not?**
A commit's identity is a hash over its tree **and its ancestry** (the parent commit's SHA), not just
the files it touches. Rebasing replayed the commit on top of the new `main` tip (`180fc30`), so its
parent pointer changed and therefore so did its hash. Identical diff, different parent, different commit.

**Why did GitHub reject the normal push?**
After the rebase the local branch tip (`895d1ca`) is no longer a descendant of the remote tip
(`df7f42a`) — the old commit was rewritten. That makes the push non-fast-forward, and GitHub refuses
to overwrite already-published commits unless you force it. The exact rejection is saved in
`task2-push-rejection.txt`; the fix was `git push --force-with-lease`.

**How does this graph differ from Task 1's?**
Task 1 contains a fork and a rejoin — two parallel lines meeting at a two-parent merge commit.
Task 2 contains no fork at all: `main` simply *fast-forwarded* onto `feature-rebase`, producing one
straight linear line of commits with no merge commit.

**Why is rebasing a branch a teammate has already pulled risky?**
Rebasing rewrites commits and changes their SHAs, so a teammate who already pulled the old commits
now has a diverging history and must force-push or re-do work to reconcile it.

---

## Task 3 — Curate commits, then a pull request

### 3.1 Result

`git log --oneline main..tidy` shows exactly three commits, each one coherent change
(see `task3-tidy-stat.txt`):

```
1fcb5cf Add usage.md with a Usage section
ffcfe9a Add config.ini with initial project settings
2ccd404 Add notes.txt with project notes
```

The `typo` commit was folded into `add notes` and `oops forgot a setting` into `add config`
with `fixup`; the three survivors were given rewritten messages with `reword`.

### 3.2 Pull request

Title: **Add project notes, config, and usage docs** (42 characters).
Description (`pr-body.md`): what changed, why it changed, and how to verify it.
The Commits tab screenshot (`pr-commits-tab.png`) shows all three rewritten commit messages.

### 3.3 Review and merge

Inline comment left on `notes.txt` line 2: the line reads as an editing action ("fixed a typo")
rather than content, so a later reader learns nothing from it.

**What each merge option does to `main`'s history:**
- **Create a merge commit** — adds one new two-parent merge commit on `main` that preserves the
  three PR commits exactly as they are, with their own identities and messages.
- **Squash and merge** — combines all PR commits into a single brand-new commit on `main`; the
  three curated commits and their messages disappear.
- **Rebase and merge** — replays the three commits individually onto `main`, producing a linear
  history with no merge commit.

**Which task produced which shape:** Task 1 was built by the "Create a merge commit" shape (a real
merge commit with two parents). Task 2's `main` was fast-forwarded to a rebased branch, which is the
linear, no-merge-commit shape that "Rebase and merge" produces on GitHub.

**Which option would destroy the curated commits:** **Squash and merge** — it flattens the three
commits into one, exactly undoing the point of Task 3.

The PR was merged with **Create a merge commit** (`b30b19a`), and all three commits are visible on
`main` in `task3-graph.txt`:

```
*   b30b19a Merge pull request #1 from levlavryniuk/tidy
|\
| * 1fcb5cf Add usage.md with a Usage section
| * ffcfe9a Add config.ini with initial project settings
| * 2ccd404 Add notes.txt with project notes
|/
* 895d1ca feature: add feature.txt
```

**Why the two removed commits were disposable and the three kept ones were not:**
`typo` and `oops forgot a setting` were not independent changes — each one only edited a line
introduced by the commit immediately before it, so neither carried a standalone intent, and their
messages described mistakes rather than changes. Folding them in leaves the final files identical
while making each surviving commit a complete, self-consistent step. The three kept commits each
introduce a distinct artifact (`notes.txt`, `config.ini`, `usage.md`) and carry a message that
explains the change on its own terms, so six months later they still read as three deliberate steps.