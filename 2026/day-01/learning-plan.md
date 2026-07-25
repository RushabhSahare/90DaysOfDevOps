- **current level**  
  I'm a working professional. Currently my role is Sr. Jira admin. 

- **3 clear goals**  
  My 3 clear goals are to Learn, implement and document everything which I have learned and covered.

- **3 core DevOps skills**
  Linux, CI/CD, IaC, Containerization
  
- Allocate a **weekly time budget**  
  I can definitely give my 2–2.5 hours per day on weekdays.

- Keep the document **under 1 page**

- Be honest and realistic; consistency matters more than perfection

These are my plans for the next 90 days!
--------------------------------------------------------------------------
WHAT I FACED:

## Day 01 - Git & GitHub Learnings

### Setup
- VS Code needs Git installed separately. Check with `git --version`.
- If missing, run `xcode-select --install` and complete the Command Line Tools install via the pop-up.

### Git Identity
- Git commits use whatever name/email is set in config, NOT your Mac username.
- Since I'm using a shared MacBook, the config still had the previous user's details.
- Fixed with:
git config --global user.name "Rushabh"
git config --global user.email "rushabhsahare81@hotmail.com"

- Verify anytime with:
git config --global user.name
git config --global user.email

### Authentication
- GitHub no longer accepts account passwords for Git operations.
- Use a Personal Access Token (PAT) instead, generated from GitHub Settings > Developer Settings > Tokens.
- Save credentials so I'm not asked every time:
git config --global credential.helper osxkeychain

### The Commit Workflow (the important one)
1. **Edit the file**
2. **Save it** (Cmd+S) - editor changes only exist on disk after saving
3. `git status` - confirm the file shows as "modified"
4. `git add <filename>` - stage the change
5. `git commit -m "message"` - commit locally
6. `git push` - send it to GitHub

### Key mistake I made
- Edited a file in VS Code but forgot to save (Cmd+S) before running `git add`/`commit`/`push`.
- Git only sees what's saved on disk, so my changes never got picked up.
- `git status` showing no changes is a red flag that the file wasn't saved.

### My approach going forward
- Always Cmd+S before any Git command.
- Run `git status` as a habit before `git add`, it tells me exactly what Git sees.
- Don't skip straight to `git push`, it only sends existing commits, it doesn't stage or save anything new.