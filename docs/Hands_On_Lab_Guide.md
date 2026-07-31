# Part 5: Hands-On Lab Guide

Seven progressive labs, Labs 1–3 on Day 1 and Labs 4–7 on Day 2. Each lab assumes the previous one is complete — they build on the same working repository. Full repo setup instructions are in Part 6 (GitHub Workshop Repository Specification); this guide assumes that repo already exists and participants have cloned it.

**Setup reminder for all labs:**
```bash
git clone https://github.com/tech4dev/git-masterclass-workshop.git
cd git-masterclass-workshop
git config user.name "Your Name"
git config user.email "you@example.com"
```

---

## Lab 1 — Basic Branching
**Day 1 · ~10 minutes (part of the 25-minute Hands-on block)**

### Objectives
- Create a branch without fear — understand it costs almost nothing.
- Practice `git switch -c` and confirm your position with `git status` / `git branch`.
- See a branch as a disposable, private workspace.

### Instructions
1. Confirm you're on `main` and it's up to date:
   ```bash
   git switch main
   git pull origin main
   ```
2. Create and switch to a new branch named `yourname/lab1-branching` (use your real first name, no spaces):
   ```bash
   git switch -c ada/lab1-branching
   ```
3. Open `participants/README.md` and add a line with your name and today's date.
4. Stage and commit:
   ```bash
   git add participants/README.md
   git commit -m "add my note for lab 1"
   ```
5. Run `git log --oneline --graph --all` and take a screenshot or just look — find your commit and notice `main` hasn't moved.
6. Switch back to `main`: `git switch main`. Confirm your note is **not** there (it's only on your branch).

### Expected Outcome
You have a branch that exists only locally so far, with one commit main doesn't have. `git status` on `main` shows no trace of your change.

### Hints
- If `git switch -c` errors with "branch already exists," someone reused a name — add a number: `ada/lab1-branching-2`.
- Forgot to switch before editing? No harm — `git switch -c` from anywhere just branches from your *current* commit.

### Solution (if stuck)
```bash
git switch main
git switch -c ada/lab1-branching
echo "- Ada, 31 July 2026: hello from my branch!" >> participants/README.md
git add participants/README.md
git commit -m "add my name for lab 1"
git log --oneline --graph --all
```

### Discussion Questions
- Why didn't `main` change when you committed?
- What would happen if you deleted this branch right now, before merging? Is your commit gone immediately?

---

## Lab 2 — Merge
**Day 1 · ~8 minutes**

### Objectives
- Merge a branch cleanly into `main`.
- Distinguish a fast-forward merge from a message that says otherwise.

### Instructions
1. Switch to `main` and make sure it's current: `git switch main && git pull`.
2. Merge your Lab 1 branch:
   ```bash
   git merge ada/lab1-branching
   ```
3. Read the output carefully. Did it say "Fast-forward," or did it open an editor for a merge commit message?
4. Push your updated `main`:
   ```bash
   git push origin main
   ```
5. Run `git log --oneline --graph` again — compare it to the graph from Lab 1.

### Expected Outcome
Your note is now on `main`, and depending on whether anyone else merged in between, you saw either a fast-forward or a new merge commit.

### Hints
- If `git push` is rejected ("fetch first"), someone else pushed to `main` before you — run `git pull` (this may itself be your first real merge!), resolve if needed, then push again.
- Not sure if it fast-forwarded? Fast-forward merges never open an editor or mention "Merge branch" in the log.

### Solution (if stuck)
```bash
git switch main
git pull origin main
git merge ada/lab1-branching
git push origin main
```

### Discussion Questions
- If two people finish Lab 1 at the same time and both try to merge to `main`, what determines who fast-forwards and who doesn't?
- Is a fast-forward merge "better" than a merge commit? When might you want the merge commit on purpose?

---

## Lab 3 — Conflict Resolution
**Day 1 · ~15 minutes (centerpiece of Day 1's hands-on block)**

### Objectives
- Trigger a real merge conflict, on purpose, on the same file/line as another change.
- Read conflict markers correctly and resolve without leaving any markers behind.
- Build the `git diff` safety-check habit before committing a resolution.

### Instructions
1. From current `main`, create two branches (do this as a pair if working with a partner, or simulate both yourself):
   ```bash
   git switch -c team-a/readme-update
   ```
   Edit `README.md`, line "## Getting Started" — change the install command described there. Commit.
   ```bash
   git switch main
   git switch -c team-b/readme-update
   ```
   Edit the **same line** in `README.md`, but change it differently. Commit.
2. Merge the first branch into `main` cleanly:
   ```bash
   git switch main
   git merge team-a/readme-update
   ```
3. Now merge the second — this should conflict:
   ```bash
   git merge team-b/readme-update
   ```
4. Open `README.md`. Find the `<<<<<<<`, `=======`, `>>>>>>>` markers.
5. Decide: keep one version, the other, or write a combined line. Delete **all three marker lines** entirely.
6. Run the safety check: `git diff` — confirm no line starts with `<`, `=`, or `>` as a marker.
7. Stage and commit:
   ```bash
   git add README.md
   git commit
   ```
   (Git pre-fills a merge commit message — you can keep it or add context.)
8. Push: `git push origin main`.

### Expected Outcome
A resolved `README.md` with no conflict markers, a new merge commit in the log with two parents, and a working file that makes sense to read.

### Hints
- If `git status` still shows `README.md` as "both modified" after you think you've fixed it, you likely haven't run `git add` yet.
- If VS Code is open, its built-in merge editor shows "Accept Current / Accept Incoming / Accept Both" buttons — this is a fully legitimate way to resolve, not a shortcut for people who "can't do it properly."
- Stuck in an editor for the commit message (looks like `vim`)? Type `:wq` and press Enter to save and exit.

### Solution (if stuck)
```bash
# after the conflicting merge command:
# open README.md, manually edit around the markers, e.g.:
## Getting Started
Run `npm run dev` to start the app in development mode.
# (markers deleted, one clear line remains)

git diff              # confirm clean
git add README.md
git commit -m "Merge team-b/readme-update, resolve README conflict"
git push origin main
```

### Discussion Questions
- What would you tell a teammate who just saw this for the first time? *(This is the Think-Pair-Share prompt from the agenda — discuss before reading on.)*
- Why did Git only conflict on this one line, and not the entire file?
- What's the risk of committing without running `git diff` first?

---

## Lab 4 — Rebase
**Day 2 · ~10 minutes**

### Objectives
- Perform an interactive rebase to clean up a messy local history.
- Practice `pick`, `squash`, and `reword` in the rebase todo list.
- Reinforce the rule: only rebase branches nobody else has pulled.

### Instructions
1. Create a throwaway branch: `git switch -c yourname/lab4-rebase`.
2. Make three small, deliberately messy commits:
   ```bash
   echo "step one" >> playground/scratch.md && git add . && git commit -m "wip"
   echo "step two" >> playground/scratch.md && git add . && git commit -m "fix typo"
   echo "step three" >> playground/scratch.md && git add . && git commit -m "another wip"
   ```
3. Start an interactive rebase over the last 3 commits:
   ```bash
   git rebase -i HEAD~3
   ```
4. In the editor that opens, change the plan so the first commit is `pick`, and the second and third are `squash` (or `s`). Save and close.
5. A second editor screen will ask you to write a combined commit message — write one clear message like `"add scratch notes for lab 4"`. Save and close.
6. Run `git log --oneline` — you should see one clean commit instead of three messy ones.
7. **Do not push this branch to be merged** — it's a throwaway exercise. Delete it when done: `git switch main && git branch -D yourname/lab4-rebase`.

### Expected Outcome
A single, well-named commit replacing three sloppy ones, and direct experience with the rebase todo list editor without fear.

### Hints
- If the editor that opens looks unfamiliar (often `nano` or `vim`), the instructions are usually printed at the top of the file itself — read them before panicking.
- Made a mistake mid-rebase? `git rebase --abort` cancels and puts you back exactly where you started.

### Solution (if stuck)
```bash
git switch -c ada/lab4-rebase
echo "step one" >> playground/scratch.md && git add . && git commit -m "wip"
echo "step two" >> playground/scratch.md && git add . && git commit -m "fix typo"
echo "step three" >> playground/scratch.md && git add . && git commit -m "another wip"
git rebase -i HEAD~3
# change lines 2 and 3 from "pick" to "squash", save
# write final message "add scratch notes for lab 4", save
git log --oneline
git switch main
git branch -D ada/lab4-rebase
```

### Discussion Questions
- What changed about the commit hashes after the rebase, and why does that matter if this branch had already been pushed and someone else pulled it?
- When would you choose NOT to clean up history like this before merging?

---

## Lab 5 — GitHub Pull Requests
**Day 2 · ~15 minutes (part of the Real-Time Collaborative Workshop)**

### Objectives
- Push a branch and open a real Pull Request on GitHub.
- Review a peer's PR with an actual comment.
- Merge through the GitHub UI once approved.

### Instructions
1. On your assigned branch (see Part 6 for the branch-to-participant assignment list), make your assigned change (e.g., editing `site/navbar.html` or `site/footer.html`).
2. Commit and push:
   ```bash
   git add .
   git commit -m "implement navbar update"
   git push origin yourname/navbar
   ```
3. On GitHub, open a Pull Request from your branch into `main`. Fill in the PR template (title + the "What changed" and "How to test" sections).
4. In chat, pair up with another participant. Open their PR, read the diff, and leave at least one comment (a question or a suggestion — not just "LGTM").
5. Once you have one approval, merge your own PR using the **"Squash and merge"** button (unless told otherwise by the facilitator).
6. Pull the latest `main` locally: `git switch main && git pull`.

### Expected Outcome
A merged PR with your name as author, a real review comment thread from a peer, and your change now live on `main`.

### Hints
- Can't push? Check you're a collaborator on the repo, or that you forked it and are pushing to your fork (facilitator will clarify which model the workshop repo uses).
- PR shows conflicts before you even open it? That's expected if someone touched your file first — this is your on-ramp into Lab 3's skills again, on GitHub's web interface this time (see Part 8, Scenario 8 for the walkthrough).

### Solution (if stuck)
Full click-path: Repo page → "Pull requests" tab → "New pull request" → select your branch as compare → "Create pull request" → fill template → "Create." After approval: "Squash and merge" → "Confirm."

### Discussion Questions
- What did you notice as a reviewer that you wouldn't have noticed just reading the final file?
- How is this different from Lab 3, where you resolved a conflict entirely on your own local machine?

---

## Lab 6 — Branch Protection
**Day 2 · ~8 minutes**

### Objectives
- Configure branch protection rules on `main`.
- Observe a direct push to `main` get rejected.
- Connect the technical setting to the "why" from the Governance concept slide.

### Instructions
1. As the repo owner/admin (facilitator will grant temporary admin to one participant per breakout group, or demonstrate live for everyone to follow along on their own fork):
   Go to **Settings → Branches → Add branch protection rule**.
2. Branch name pattern: `main`.
3. Enable:
   - Require a pull request before merging
   - Require approvals (set to 1)
   - Require status checks to pass before merging (if a GitHub Actions check exists — see Part 6)
4. Save the rule.
5. Try to push directly to `main` from your terminal:
   ```bash
   git switch main
   echo "test" >> playground/notes.md
   git add . && git commit -m "direct push test"
   git push origin main
   ```
6. Observe the rejection message. Undo your local test commit: `git reset --hard HEAD~1`.

### Expected Outcome
A rejected push with a message referencing protected branch rules, proving the setting actually works — not just that it exists in a settings page.

### Hints
- If your push succeeds, double check the rule's branch name pattern matches `main` exactly and that it was saved.
- Working on a personal fork instead of the shared repo? You can still practice this on your own fork's `main`.

### Solution (if stuck)
No git commands beyond the ones above are required — this lab is primarily a GitHub UI exercise. If the push isn't rejected, verify the rule was saved (refresh the Branches settings page) and that you're pushing to the exact protected branch name.

### Discussion Questions
- Would this setting have prevented the accidental force-push scenario discussed in the Governance section? Why or why not?
- What's one situation where branch protection might slow a team down more than it helps?

---

## Lab 7 — Disaster Recovery Using Reflog
**Day 2 · ~15 minutes (the emotional climax — protect this time in the agenda)**

### Objectives
- Deliberately create a "lost" commit via detached HEAD.
- Use `git reflog` to find it.
- Recover it using at least one of: `git branch`, `git cherry-pick`, or `git reset --hard`.

### Instructions
1. Find a recent commit hash on `main`: `git log --oneline -5`. Pick one, e.g. `7e2b41f`.
2. Check it out directly (not a branch):
   ```bash
   git checkout 7e2b41f
   ```
   Git will warn you that you're in "detached HEAD" state — this is expected and fine.
3. Make a new commit here:
   ```bash
   echo "an experiment nobody will ever see again... or will they?" >> playground/scratch.md
   git add .
   git commit -m "quick experiment in detached HEAD"
   ```
4. Note the commit hash printed (e.g. `c9a3f10`).
5. Switch back to `main`:
   ```bash
   git switch main
   ```
   Git will warn you that you're leaving commits behind. This is the "loss" moment.
6. Confirm it's really not visible from `main`: `git log --oneline` — your experiment commit isn't there.
7. Now recover it:
   ```bash
   git reflog
   ```
   Find the line referencing your "quick experiment" commit and its hash.
8. Create a branch pointing at it:
   ```bash
   git branch recovered-work c9a3f10
   git switch recovered-work
   git log --oneline -3
   ```
   Your "lost" commit is right there.

### Expected Outcome
A recovered branch containing the commit that had no branch pointing to it a few minutes ago — direct, hands-on proof that committed work in Git is extremely hard to truly lose.

### Hints
- Reflog entries are ordered newest first — look near the top for your most recent actions.
- If you're not sure which hash is yours, look for your own commit message text in the reflog output.
- This branch (`recovered-work`) is just for practice — delete it after the lab: `git switch main && git branch -D recovered-work`.

### Solution (if stuck)
```bash
git log --oneline -5
git checkout <any-recent-hash>
echo "an experiment nobody will ever see again... or will they?" >> playground/scratch.md
git add . && git commit -m "quick experiment in detached HEAD"
git switch main
git reflog
git branch recovered-work <the-experiment-hash-from-reflog>
git switch recovered-work
git log --oneline -3
```

### Discussion Questions
- How long does Git keep reflog entries by default, and what does that imply about how urgent recovery usually is?
- Is there any Git operation that reflog *can't* help you recover from? (Hint: think about files that were never staged or committed at all — tie this back to Day 1's "Git protects commitments, not intentions.")

---

## Lab Facilitation Notes (quick reference)

| Lab | Day | Time | If running behind |
|---|---|---|---|
| 1 – Branching | 1 | 10 min | Don't cut — it's the foundation for everything after |
| 2 – Merge | 1 | 8 min | Can compress to 5 min if Lab 1 ran long |
| 3 – Conflict Resolution | 1 | 15 min | Never cut — this is Day 1's core deliverable |
| 4 – Rebase | 2 | 10 min | Can compress to 6 min |
| 5 – Pull Requests | 2 | 15 min | Never cut — this is the collaborative workshop |
| 6 – Branch Protection | 2 | 8 min | Cut to a described walkthrough first if time is short |
| 7 – Disaster Recovery | 2 | 15 min | Never cut — protect above all else per the facilitator guide |

---

*End of Part 5. Say "continue" for Part 6: the GitHub Workshop Repository Specification.*
