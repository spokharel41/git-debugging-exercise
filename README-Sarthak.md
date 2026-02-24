Checked the logs using the following command:
git log --oneline --decorate --graph --all


TASK 1: 
1. Commit that mixes unrelated changes:
Commit: 2ee27fa
Reason: The commit is not atomic and it is hard to trace what exactly was fixed. This also complicates rollback. 

2. Commit that introduced incorrect content
Commit: 4a0530f
Reason: We can see a line: +This line should not be here - it was added by mistake. which makes it an accidental content and not part of intented feature. Also, this is not a good commit principle. 

3. One commit that contains sensitive information (API key or secret)
Commit: f6cd227
Reason: It has hard coded API key in the commit. 

TASK 2: 
Start rebase just before the quick fix commit. 
Command: git rebase -i 12516f6
- This opens an interactive window with the quick fix commit and the commits after that. 
- Edit the pick 2ee27fa # quick fix to edit 2ee27fa # quick fix
- Save it by pressing ESC and saving it by :wq
- The command will replay commit and stop at the quick fix commit. 
- Then do git reset HEAD^ which will uncommit the quick fix and keep the changes in working directory. It lists README.md and notes.txt. we then add these files step by step. 
- git add README.md and git commit -m "docs: update README with feature section"
- git add notes.txt and git commit -m "docs: update project status in notes"
- Once the splitting is done we'll rebase it again:
git rebase --continue
- Now once we do  git log --oneline --graph we will see the commits are splitted. 

TASK 3: 
- Open the todo.txt file. 
- Remove the last line: This line should not be here - it was added by mistake.
- Commit the fix using: git add todo.txt and git commit -m "fix: remove accidental line from todo list"


TASK 4: 
- Checkout to main branch: git checkout main and then run the merge command: git merge feature/login
- There was an untracked working tree file(config.txt) which would be overwritten by merge. 
- Temporarily moved the file: mv config.txt /tmp/config.txt.backup
- Then ran the merge command again: git merge feature/login
- After merge restored the backed up file: mv /tmp/config.txt.backup config.txt
- Then add the file: git add config.txt and git commit --amend --no-edit
- Got an error for unmerged files. so for those opened each of the conflicted files(notes.txt and README.md) and then replaced it with the incoming change from another branch. 
-  After that the resolved files are staged: git add README.md notes.txt
- The merge is completed using git commit and is verified by git status

TASK 5: 
- Remove secrets.txt from the current working tree as it was added in f6cd227 commit: rm secrets.txt
- stage the deletion process secrets.txt: git add secrets.txt 
- commit the changes: git commit -m "chore: remove leaked secrets from working tree". This will record the file removal. 


TASK 6: 
- Checkout to feature/login branch: git checkout feature/login
- Rebase main branch to here: git rebase main
- Git has applied all commits from feature/login on top of latest main. 
- Since there were no conflicts, git automatically finished the rebase. 
- Chose rebase over merge to apply the commits of feature/login on top of the latest main. 
- Also to avoid extra merge commits. 

TASK 7: 
- Use git reflog to check all the commits that the HEAD has pointed to. 
- Use git reflog to check the disappeared commit. 
- Use git cherry-pick #commithash to get the lost commit on top of current branch. 
- Since there were no lost commits, I did git cherry-pick --skip


Summary: The issue were identified with inspecting history. To avoid these issues in team, first would like to have a descriptive commit message and peer review before merging. Also manage .gitignore to maintain security related to leaked secrets. 
Challenges encountered were interrupted rebase as it was needed to abort previous incomplete rebase before starting a clean rebase. 

