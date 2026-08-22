# Day 22 – Git Fundamentals Notes

1) What is the difference between `git add` and `git commit`?
`git add` moves changes into the staging area, a holding zone for what you're about to commit. `git commit` takes whatever is currently staged and permanently saves it as a snapshot in the repository's history. Nothing becomes part of history until it's committed, add alone doesn't save anything permanently.

2) What does the staging area do? Why doesn't Git just commit directly?
The staging area lets you choose exactly what goes into a commit, even if you've changed five files, you can stage and commit just two of them, or even part of a single file. Without it, every save would force you to commit everything you've touched at once, which makes it much harder to write clean, focused commits that describe one logical change each.

3) What information does `git log` show you?
Each commit's unique hash, author name and email, date, and commit message, in reverse chronological order (newest first). `git log --oneline` compresses this to just the short hash and message per line, useful for a quick scan of history.

4) What is the `.git/` folder and what happens if you delete it?
`.git/` is the actual database, it stores every commit, branch, and piece of history for the repository. It's not just config, it IS the repository. Deleting it permanently erases all version history; your files would still exist on disk, but they'd revert to being plain, untracked files with zero Git history, memory, or ability to recover previous versions.

5) What is the difference between working directory, staging area, and repository?
- **Working directory**: the actual files you see and edit on disk right now.
- **Staging area**: a holding zone where you place changes you intend to commit next (via `git add`).
- **Repository**: the permanent, committed history stored inside `.git/`.
A change moves: working directory → (git add) → staging area → (git commit) → repository.