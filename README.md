# TP Git
## Description

This project is a practice repository we made for our "Software Engineering" course. It helped us get familiar with Git: creating a repository, managing simple text files (fruits.txt, vegetables.txt, sauces.txt, spices.txt, herbs.txt), making and merging branches, fixing conflicts (including one caused by file encoding), connecting to GitHub, and working together as a team.

GitHub repository: [github.com/momo226-code/tpgit-](https://github.com/momo226-code/tpgit-)

## Authors

- **Mohamed Idriss NANA**
- **Taha Thiam**

## Objectives

- Learn how to use a version control system (Git).
- Master versioning of a software project.
- Share a project and work as a team via GitHub.

---

## 1. Installing and Configuring Git

Git was first installed from [git-scm.com](https://git-scm.com/downloads), then verified with:

```powershell
git --version
```

Git identity (name + email) was then configured, since Git attaches it to every commit:

```powershell
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## 2. Initializing the Local Repository

A `tpgit` folder was created and turned into a Git repository with `git init`. A first file, `fruits.txt`, was created and then tracked using the classic cycle **status → add → commit → status → log**.

![First commit on fruits.txt](screenshots/01-premier-commit-apple.png)
*First commit: adding "Apple" to `fruits.txt`, with status checked before and after.*

![Adding a second fruit](screenshots/02-ajout-orange.png)
*Adding "Orange": a new add → commit cycle.*

Three additional commits followed the same pattern (Banana, Lemon, Ananas), each visible in the detailed history:

![Detailed commit history](screenshots/03-git-log-detaille.png)
*`git log`: each commit shows its hash, author, date, and message.*

## 3. Branches

### `vegetables` branch

A `vegetables` branch was created and switched to using `git branch` / `git checkout`, then a `vegetables.txt` file was added and built up through 3 separate commits (Tomato, Cucumber, Carrot).

![Creating the vegetables branch](screenshots/04-creation-branche-vegetables.png)
*Creating and switching to the `vegetables` branch, confirmed with `git branch`.*

![History of the vegetables branch](screenshots/05-log-graph-vegetables.png)
*`git log --graph --oneline --decorate --all`: the `vegetables` branch diverged from `master` after the "Ananas" commit.*

### `sauces` branch

The same approach was used for the `sauces` branch, with a `sauces.txt` file built up through the "SauceArrachide" and "Djoumblé" commits.

![Sauces branch](screenshots/06-branche-sauces.png)
*Creating the `sauces` branch and its first two commits, with the full graph showing the divergence of the `sauces` and `vegetables` branches from `master`.*

### `spices` and `herbs` branches (paired work)

Each member of the pair worked on their own branch (`spices` for Mohamed Idriss, `herbs` for Taha), with their own files and commits, before merging into `main`.

![History of the spices and herbs branches](screenshots/08-log-detaille-herbs-spices.png)
*Detailed `git log` showing each contributor's commits on their respective branches.*

![gitk visualization of the branches](screenshots/10-gitk-branches-herbs-spices.png)
*Graphical visualization (gitk) of the parallel progress on the `spices` and `herbs` branches before they were merged into `main`.*

## 4. Merges

Once work was finished on each branch, merges were performed into `master`/`main`, following the principle: **check out the destination branch, then `merge` the source branch into it**.

```powershell
git checkout main
git pull
git merge <branch>
git push origin main
```

![Successful merge and final history](screenshots/09-historique-complet-final.png)
*Final graph showing the `vegetables` and `sauces` branches merged into `main` via a merge commit.*

## 5. Conflict Resolution

Two kinds of conflicts came up during the lab and were resolved.

### a) A classic content conflict (`vegetables.txt`)

After deleting "Carotte" locally while a different change existed on the remote, a conflict appeared when running `git pull`:

![Conflict on vegetables.txt](screenshots/14-conflit-vegetables.png)
*`CONFLICT (content): Merge conflict in vegetables.txt` after a `git pull`.*

### b) An encoding-related conflict (`sauces.txt`)

A strange problem happened with sauces.txt. Git said cannot merge binary files, even though the file was just plain text. The real reason was the file's encoding and the way the text was saved. PowerShell had saved it using UTF-16 (with a hidden marker called a BOM) instead of the more common UTF-8. Git looked at this and thought the file was a binary file so it couldn't merge it line by line like it normally does with text files.

![Conflict and divergence on sauces.txt](screenshots/17-conflit-sauces-diverge.png)
*A `git pull` attempt triggering a binary-file conflict, followed by `git merge --abort` (cleanly cancelling the attempt, without fixing the underlying issue) and confirmation of the branch divergence (`git status`).*

**Solution applied**: the file was manually rewritten from scratch in UTF-8, then the conflict was explicitly resolved:

```powershell
Remove-Item sauces.txt
"SauceArrachide" | Set-Content sauces.txt -Encoding utf8
"Djoumblé"        | Add-Content sauces.txt -Encoding utf8
"SauceTomate"     | Add-Content sauces.txt -Encoding utf8

git add sauces.txt
git commit -m "Resolve conflict in sauces.txt and fix encoding to UTF-8"
git pull
```

![Final resolution of the encoding conflict](screenshots/18-resolution-finale-encodage.png)
*Rewriting the file in UTF-8, `git add` + `git commit` to close the conflict, then `git pull` confirming everything is in sync (`Already up to date`).*

## 6. Managing Files (Adding / Removing Items)

Several standard operations were practiced on `fruits.txt`: adding new items (Grenadille, Fraise, Papaye) and removing a specific line without an editor, directly from PowerShell.

![Removing an item from a list](screenshots/11-suppression-fruit.png)
*Removing "Banana" and then "Ananas" using `Where-Object { $_ -ne "..." }`, without opening a text editor.*

![Adding new fruits](screenshots/12-ajout-grenadille.png)
*Adding "Grenadille" to the list, followed by a dedicated commit.*

## 7. Remote Repository (GitHub)

The local repository was linked to a remote repository created on GitHub, then synced via `push`/`pull`:

```powershell
git remote add origin https://github.com/momo226-code/tpgit-.git
git push -u origin main
```

![Push to GitHub](screenshots/13-push-github.png)
*Sending local commits to the remote `tpgit-` repository on GitHub.*


## Issues Encountered

| Problem | Cause | Solution |
|---|---|---|
| `fatal: unable to auto-detect email address` | Git identity not configured | `git config --global user.name/user.email` |
| `cannot do a partial commit during a merge` | Trying to commit a single file while a merge was in progress | `git add .` (stage everything) before committing |
| `Cannot merge binary files: sauces.txt` | File written in UTF-16 (via `echo >>` in PowerShell), interpreted as binary by Git | Rewriting the file in UTF-8 with `Set-Content -Encoding utf8` |
| Conflict reappearing after `git merge --abort` | `--abort` cancels the merge attempt but never resolves the underlying disagreement | Redo the `pull`/`merge` and actually resolve the conflict (edit + `add` + `commit`) |

## Conclusion

This lab let us practice the full Git process, step by step: tracking a simple text file, working with a partner, creating branches, merging them, and fixing conflicts

## Resources

- [Official Git Documentation](https://git-scm.com/book/en/v2)
- [Learn Git Branching](https://learngitbranching.js.org/)
- [Try GitHub](https://try.github.io/)
