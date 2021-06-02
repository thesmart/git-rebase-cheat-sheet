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
