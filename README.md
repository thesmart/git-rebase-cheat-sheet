# Git Rebase Tutorial
This is a cheat sheet for learning how to manage branch development using rebase.

Start by creating a feature branch from main:
```sh
git checkout -b smart/my-feature
```

Make a few commits to this `README.md`.
```sh
echo "\n\n$(date)" >> README.md
git add README.md
git commit -m "I made my edit to the README"

echo "\n\n$(date)" >> README.md
git add README.md
git commit -m "This is my second commit to README"

echo "\n\n$(date)" >> README.md
git add README.md
git commit -m "This is my third commit to README"
```

Now it's a part of history:
```sh
git log
```
```
commit 82c1c0fbb3905dc7ff02304c2a0d2e0bc3e13ad5 (HEAD -> smart/my-feature)
Author: John Smart <thesmart@users.noreply.github.com>
Date:   Wed Jun 2 13:58:14 2021 -0700

    This is my third commit to README

commit 5a382de7c0eb3342732b6ea7717c9355d2852bab
Author: John Smart <thesmart@users.noreply.github.com>
Date:   Wed Jun 2 13:58:14 2021 -0700

    This is my second commit to README

commit e76901cd83cad74c19d674838c08d27b42ef77e8
Author: John Smart <thesmart@users.noreply.github.com>
Date:   Wed Jun 2 13:58:14 2021 -0700

    I made my edit to the README

commit 0caa69ddd46628913f171115d3b3cc6b60b19c44 (origin/main, origin/HEAD, main)
Author: John Smart <thesmart@users.noreply.github.com>
Date:   Wed Jun 2 10:55:43 2021 -0700

    Initial commit
```

We can edit history with interactive rebase:
```
git rebase -i HEAD~3
```

Interactive rebase will open a text editor. When closed, it takes the 
whatever was saved and applies it to the history. Closing the unedited file
is effectively a noop.  You can also get out of it by `ctrl+c` the paused
git rebase command.

Here, I squash the last three commits together to make one new commit that
replaces `e76901c`.
```
pick 0caa69d Making progress on the README file.
pick e76901c I made my edit to the README
squash 5a382de This is my second commit to README
squash 82c1c0f This is my third commit to README
```

A new editor opens to let me change the commit message. Here's the log after 
I'm finished:
```
commit a426582ca389b03a80a2dc3c41a625016fe0142c (HEAD -> smart/my-feature)
Author: John Smart <thesmart@users.noreply.github.com>
Date:   Wed Jun 2 13:38:54 2021 -0700

    Making progress on the README file.

commit 0caa69ddd46628913f171115d3b3cc6b60b19c44 (origin/main, origin/HEAD, main)
Author: John Smart <thesmart@users.noreply.github.com>
Date:   Wed Jun 2 10:55:43 2021 -0700

    Initial commit
```

Now let's suppose that our coworkers have been busy pushing features while we've 
been working on our branch. We can get pull their changes:
```sh
git pull --rebase origin main
```

This will pull all of main's commits to our branch and THEN apply our changes on
top. This will totally avoid any merge conflicts as long as you are pulling from
main, provided that everyone is squash merging their PRs.

## Fixing Mistakes Part 1: Before Push

Let's suppose I rebase and I messed something up. You can fix that too!

```sh
git reflog
```
```
a426582 (HEAD -> smart/my-feature) HEAD@{0}: rebase -i (finish): returning to refs/heads/smart/my-feature
a426582 HEAD@{1}: rebase -i (squash): Making progress on the README file.
b7c7998 HEAD@{2}: rebase -i (start): checkout HEAD~2
82c1c0f HEAD@{3}: commit: This is my third edit to README
1de605a HEAD@{4}: commit: This is my second edit to README
b7c7998 HEAD@{5}: commit: I made my edit to the README
0caa69d (origin/main, origin/HEAD, main) HEAD@{6}: checkout: moving from main to smart/my-feature
0caa69d (origin/main, origin/HEAD, main) HEAD@{7}: clone: from git@github.com:thesmart/git-rebase-cheat-sheet.git
```

You can go back in time like nothing happened:
```sh
# NOTE! This will destroy any local uncommitted changes!
git reset --hard 82c1c0f
```
```
82c1c0f (HEAD -> smart/my-feature) HEAD@{0}: reset: moving to 82c1c0f
a426582 HEAD@{1}: rebase -i (finish): returning to refs/heads/smart/my-feature
a426582 HEAD@{2}: rebase -i (squash): Making progress on the README file.
b7c7998 HEAD@{3}: rebase -i (start): checkout HEAD~2
82c1c0f HEAD@{4}: commit: This is my third edit to README
1de605a HEAD@{5}: commit: This is my second edit to README
b7c7998 HEAD@{6}: commit: I made my edit to the README
0caa69d (origin/main, origin/HEAD, main) HEAD@{7}: checkout: moving from main to smart/my-feature
0caa69d (origin/main, origin/HEAD, main) HEAD@{8}: clone: from git@github.com:thesmart/git-rebase-cheat-sheet.git
```

Note how `reset` just adds to the reflog such that you can even reset the reset.

## Pull Requests

When you're done tidying up your commit history, its time to open a pull request.

```sh
git push origin smart/my-feature
```
```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 12 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 862 bytes | 862.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for 'smart/my-feature' on GitHub by visiting:
remote:      https://github.com/thesmart/git-rebase-cheat-sheet/pull/new/smart/my-feature
remote:
To github.com:thesmart/git-rebase-cheat-sheet.git
 * [new branch]      smart/my-feature -> smart/my-feature
```

Github remote provided a nice URL you can use. Here is an example of opening a pull request:
![Comparing_main___smart_my-feature_·_thesmart_git-rebase-cheat-sheet](https://user-images.githubusercontent.com/280877/120562170-bb619500-c3ba-11eb-9eea-fdb97b8cbba9.jpg)

The goal of feature branch squashing is to communicate well:

 1. Write a one-liner title in the active present tense. (e.g. "Adds foobar" instead of "Added foobar")
 1. Write a thorough description:
    - provide context about the purpose of the change
    - describe the tangible outcome of the change
    - include links to specs, screenshots, animated gif demos, reproductions steps, bug ids, etc.
 1. Assign yourself as the assignee.
 1. Request review from others, and consider sending them a courtesy Slack message.

 ## Fixing Mistakes Part 2: After Push

Suppose we made a mistake that we've already pushed to our feature branch. OH NOES!

No worries, you can force GitHub to accept your local commit history:
```
git push --force-with-lease origin main
```

This will force GitHub's history to change, but only if there are no commits 
from other authors that would be lost. If you **really** want to destroy remote
changes, you can use the unsafe `--force` option instead.

**NEVER do this on `main`!**

If you need to fix something on main, open a branch and use `git revert COMMIT_ID` 
to commit an inverse change. Then make your change, get a review, and
squash merge.

Occasionally, a pull from main may create merge conflicts. It may be easier to fix these 
by using interactive pull, allowing you to fix merge conflicts one commit at a time:
```sh
# stash your local changes
git stash
# start the interactive pull
git pull --rebase=interactive origin main
```

 ## Merging Pull Requests
You should never merge a pull request that hasn't had a review. At least one other
developer should look at and sign-off on your code.

 You can find pull requests awaiting your review by setting this filter:
 ![Pull_requests_·_thesmart_git-rebase-cheat-sheet](https://user-images.githubusercontent.com/280877/120563117-adad0f00-c3bc-11eb-90fa-d700e3d007a1.jpg)

When you have had your code reviewed and feel good about it, merge your PR using "Squash and Merge".
 ![Creates_a_tutorial_for_interactive_rebase_by_thesmart_·_Pull_Request__1_·_thesmart_git-rebase-cheat-sheet](https://user-images.githubusercontent.com/280877/120563392-4b084300-c3bd-11eb-886d-d38d88286f04.jpg)

This will turn your PR into a single merge to `main`, just copy over the PR title and description.

**NOTE:** Make sure to incude the PR id in the title: e.g. `Adds foobar (#12345)`
![Creates_a_tutorial_for_interactive_rebase_by_thesmart_·_Pull_Request__1_·_thesmart_git-rebase-cheat-sheet](https://user-images.githubusercontent.com/280877/120563580-c964e500-c3bd-11eb-93d1-00cf486c94e5.jpg)

**You should delete your feature branch now.** If you need to make more changes, open a new branch.
```
git branch -D smart/my-feature
```
![Creates_a_tutorial_for_interactive_rebase_by_thesmart_·_Pull_Request__1_·_thesmart_git-rebase-cheat-sheet](https://user-images.githubusercontent.com/280877/120563795-4f812b80-c3be-11eb-8238-38fc6184d187.jpg)
