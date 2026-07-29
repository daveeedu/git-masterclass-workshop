# Git & GitHub Cheat Sheet
### Tech4Dev TCLA Masterclass — Solving Complex Merge Conflicts and Managing Branches Safely

---

## Most Useful Commands

| Command | What it actually does |
|---|---|
| `git status` | Shows what's changed, staged, and unstaged right now |
| `git add <file>` | Marks a change to be included in the next commit |
| `git commit -m "msg"` | Permanently records staged changes with a message |
| `git log --oneline --graph` | Shows commit history as a readable graph |
| `git diff` | Shows exact line-by-line changes (unstaged vs. last commit) |
| `git diff --staged` | Shows what's about to be committed |
| `git switch <branch>` | Moves HEAD to a different branch |
| `git switch -c <branch>` | Creates a new branch and switches to it |
| `git branch` | Lists local branches |
| `git branch -d <branch>` | Deletes a branch (only if fully merged) |
| `git fetch` | Downloads remote changes without merging them |
| `git pull` | Fetches + merges remote changes into your current branch |
| `git push` | Uploads your commits to the remote |
| `git remote -v` | Shows which remote(s) you're connected to |

---

## Undo Commands (know the difference!)

| Command | Effect | Safe on shared history? |
|---|---|---|
| `git restore <file>` | Discards uncommitted changes in a file | ✅ Yes (local only) |
| `git reset --soft HEAD~1` | Undoes last commit, keeps changes staged | ⚠️ Local branches only |
| `git reset --hard HEAD~1` | Undoes last commit, discards changes entirely | ⚠️ Local branches only |
| `git revert <hash>` | Creates a **new** commit that reverses a previous one | ✅ Yes — safe for shared/pushed history |
| `git stash` | Shelves uncommitted changes for later | ✅ Yes (local only) |
| `git stash pop` | Restores the most recently stashed changes | ✅ Yes (local only) |

**Rule of thumb:** `reset` rewrites history — only use it on commits nobody else has pulled yet. `revert` adds new history — safe anytime, including on `main`.

---

## Conflict Resolution Workflow

1. Run the merge/rebase/cherry-pick — Git tells you exactly which files conflict.
2. Open each conflicted file. Read the markers:
   ```
   <<<<<<< HEAD
   (what you currently have)
   =======
   (what's coming in)
   >>>>>>> branch-name
   ```
3. Decide: keep yours, keep theirs, or write something new that combines both.
4. **Delete every marker line** (`<<<<<<<`, `=======`, `>>>>>>>`).
5. Run `git diff` — confirm **zero** marker characters remain. *(This step prevents the #1 most common mistake.)*
6. `git add <file>`
7. Finish it:
   - After a **merge** → `git commit`
   - After a **rebase** → `git rebase --continue`
   - After a **cherry-pick** → `git cherry-pick --continue`
   - Changed your mind? → `git merge --abort` / `git rebase --abort` / `git cherry-pick --abort`

---

## Merge vs. Rebase — Decision Tree

```
Has anyone else already pulled/pushed this branch's commits?
│
├── NO  → Safe to rebase.
│         git rebase main
│         → clean, linear history; commits get new hashes
│
└── YES → Merge instead.
          git merge main
          → preserves exact history; creates a two-parent merge commit
```

**One-line rule:** *Rebase your own local, unpushed work. Never rebase a branch other people already work from.*

---

## GitHub Workflow

1. `git switch -c <your-branch>` — never work directly on `main`
2. Make your changes, commit with a clear message
3. `git push -u origin <your-branch>`
4. Open a **Pull Request** on GitHub into `main`
5. Request review from a teammate
6. Address feedback, push updates (the PR updates automatically)
7. Once approved + checks pass → **Merge**
8. Delete the branch (locally: `git branch -d`, remotely: GitHub does this automatically on merge, or `git push origin --delete <branch>`)

**Branch protection basics:** require PR before merging · require ≥1 approval · require status checks to pass · no direct pushes to `main`, even for admins.

---

## Recovery Commands (the safety net)

| Situation | Command |
|---|---|
| "I think I lost a commit" | `git reflog` — find the hash, it's almost certainly still there |
| "Found it — now what?" | `git branch recovered-work <hash>` |
| "I need just that one commit elsewhere" | `git cherry-pick <hash>` |
| "I'm in detached HEAD and confused" | `git switch main` (or any branch) — nothing is lost, see `git reflog` |
| "I need to undo a commit that's already shared" | `git revert <hash>` — never `reset --hard` on shared history |
| "I force-pushed and overwrote something" | Ask the affected teammate for their local reflog — their copy likely still has it |

> **Remember:** `git reflog` keeps a record of everywhere HEAD has pointed for about 90 days by default — even commits no branch points to anymore. Almost nothing committed in Git is ever truly gone.

---

*Tech4Dev Community Learning Academy · Facilitated by David Edu · 31 July & 1 August 2026*
*Full workshop repo, lab guide, and recorded sessions linked in the course README.*
