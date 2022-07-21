# Connect Github Account via SSH
In this [link](https://www.geeksforgeeks.org/using-github-with-ssh-secure-shell/),  there is a good tutorial about how to set up SSH for github account. In order to clone repositories with SSH, we have to set it up.

# Cloning Repo via SSH
With following command you can clone your repository via SSH:
```bash
git clone git@github.com:<USERNAME>/<REPONAME>.git
```

# Adding Changes & Update
We can see status local repository via typing following:
```bash
git status
```

After making changes in local github repository, we can add this file to what will be committed via following command:
```bash
git add <FILENAME>
```

![[Pasted image 20220622131207.png]]

In order to select all files, we can use following command:
```bash
git add .
```

# Commit
After we made changes and added them to list of what'll be committed. We can commit them like so
```bash
git commit -m "caption for commit" -m "description for commit"
```

![[Pasted image 20220622132833.png]]

However, this won't make your change can be seen in Github repository. It'll just commit them locally. We are going to use **PUSH** for pushing these commits into github.

# Push
Before using push we need to be sure that our SSH set up for github is ready to go. In order to push commits to github we'll use following command:
```bash
git push origin master 
```

**origin** = path of github repo     *e.g.   /BooRuleDie/obsidian*
**master** = branch that you'll upload your commits to

# Creating Local Git Repository
In order to create a new git repository in your local machine we can use following command:
```bash
git init
```

![[Pasted image 20220622133818.png]]

Adding and committing will be done same way. However if we try to push repository, we'll see an error. And reason is following, since we didn't clone our repo from github via SSH, git doesn't know where to put this commit. So we need to specify a **origin** :
```bash
git remote add origin git@github.com:<USERNAME>/<REPONAME>.git
```

After that we should be able to see our origin:
![[Pasted image 20220622143126.png]]

Now `git push origin master` will work fine.

# Git Branching
In order to see all branches you have in current repo:
```bash
git branch
```

To create a new branch and switch between branches:
```bash
git checkout -b <branch-name> # creates branch
git checkout <branchname> # switch to specified branch
```

# Merge
Before merging any branch with master, we can check the changes have been made via **diff** command:
```bash
git diff <branch-name>
```

![[Pasted image 20220622144643.png]]

After that we can merge branches.
```bash
git merge <branch-name>
```

There is also another way to merge branches which is a way that you see more often.
```bash
git checkout <branch-name>
git push origin <branch-name>
# After that point, go to github and make PR manually
```

After pushing changes to new branch. We can make a **PR**(Pull Request) for master branch. And it'll merge two branches into master. However you'll not see changes in your local repo. In order to see changed master branch, you need to enter following:
```bash
git pull origin master
```

How you delete branches in your local repo:
```bash
git branch -d <branch-name>
```

# Log
You can see all commits that have been made in a repo by entering following command:
```bash
git log
```

![[Pasted image 20220622150649.png]]

And we can go back to a specific commit like so:
```bash
 git reset --hard <commit-value>
```