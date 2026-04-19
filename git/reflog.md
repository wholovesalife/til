# git reflog saves you from almost anything

if you ever run a `git reset --hard` and lose commits, `git reflog` tracks every `HEAD` move for 90 days. grab the sha, `git checkout <sha>` or `git cherry-pick <sha>`, and your work is back.

works for branch deletions too — deleted branch is reachable via reflog until gc.
