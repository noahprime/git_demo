# Git

Git is an open source software that allows you to track changes to files. This type of software is called a source control manager of SCM.

In Git, you create **repositories**, which are essentially folders which contain all of your project's files.

Git allows you keep a log of changes, including who, what, and when these changes were made, and allows you to revert to previous versions.

Additionally, you can create multiple versions of your repository that can be worked on and updated in parrallel called **branches**. This allows for a progress to be made on a project, while retaining a stable main branch for users to rely on, and allows for multiple development paths to happen at the same time. Branches can then be **merged** when ready.

# Github

Github is a place where Git repositories can be stored, managed and accessed by you and collaborators. Essentially, an online home for your repositories. 

You can see all of the changes that are being made, the branches that exist, and get all the information about the history of a given project. You can also **clone** repositories to have access to them on your local machine to either use or help in development as well.

# Usage

## Initialize and Basic Commands

First, we'll turn our current directory into a git repository:

```
git init
```

This creates a hidden directory `.git`, which contains all of the needed Git files.

A common command which allows you to see the current state of your git environment is 

```
git status
```

We see that we're on a branch called "master". This is the default main branch of the repository, and the only one so far that exists.

We also see that there is an untracked file, this file, called `README.md`. This means that Git is not going to track any changes made to this file. 

We can add this file to our repository with two commands. First,

```
git add README.md

# Note, to unstage the file
git restore --staged README.md
```

stages the file. This is kind of like a holding period where the file is being prepared to be commited. And then,

```
git commit -m "The first commit, adding the README file."
```

This commits our changes which does multiple things. 
1. In this case, README.md is now a tracked file in our repository.
2. We write a log of this changes in the history of this repository.

After this, we'll see that `git status` tells us that our working directory is clean, meaning there are no changes compared to the most recent commit.

Now let's add some new files and see what happens when we start editting them.

```
touch secret_file.txt
touch code.R
```

## Git Ignore

First, we created `secret_file.txt`, which of course is a secret. Thus, we don't want this file to be in our repository for other people to see. We can ask Git to ignore this file, and others using a `.gitignore` file.

We can again create this using
```
touch .gitignore
```

and then add the following line:
```
# Ignore all text files
*.txt

# Or we can ignore the specific file
secret_file.txt
```

If we check `git status` again, we see that file is no longer being monitored.

Now let's add our other two new files to the repository, which we can do both at once using
```
git add .
git commit -m "Adding our R script and .gitignore file"
```

And let's edit the `code.R` file like we would. For example add the lines:
```
# This is an R script for approximating pi
x <- 22
y <- 7
pi_approx <- x/y
```

Now `code.R` shows up on the list of files with changes that have not been staged for commit. So, we can commit them. Here another alternative way to do this is:
```
git commit -a -m "Adding code to approximate pi"
```

## Git Diff

Edit again to look like:
```
# This is an R script for approximating pi
x <- 333
y <- 106
pi_approx <- x/y
```

Now, our file is different from our last commit, so we can see exactly what has changed using
```
git diff
```
We see the two lines that have been changed, both their old versions, and what they are now. This is surely not the best way to see differences, as desktop applications display this information better. But we see that Git knows what changes have been made to the file thanks to our commit history.

Then we can see that we're satisfied with the changes and commit them:
```
git commit -a -m "Using a better approximation of pi."
```

## Restoring Files

Let's now accidently delete our `code.R` file and see how we can fix it with Git.
```
rm code.R
git status
```

We see that `code.R` has been deleted, but we see an option to restore the file.
```
# Old Git version, deception
git checkout code.R

# Newer Git versions, likely installed locally
git restore code.R
```

This brings back the file `code.R` from the last commit. This can also be used if you've made changes to the file that you don't want to keep, or any reason you might have to restore the file back to it's last commit.

## Logs and Reverting

With the commits that we've been making, we've been writing a history book of our repository. We can view this by using
```
git log
git log --oneline
```
which shows us all of our past commits with details about them, including an identifier code.

We can use that identifier code to access previous commits. The commands
```
git checkout
git reset
git revert
```
all are related to this idea and the differences and use cases of each can be read about [here](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting#:~:text=Checking%20out%20a%20file%20is,you%20won't%20switch%20branches.).

## Branching

Let's create a new branch to fix my code.

To create a new branch we can use:
```
git branch Fix-Code-For-Noah
```

we can look at our branches using 
```
git branch
```

which shows us that there are two branches, master and Fix-Code-For-Noah, and an astrisk that tells us we're currently in master.

To swithc into our new branch we can
```
# Old and new Git versions
git checkout Fix-Code-For-Noah

# New Git versions
git switch Fix-Code-For-Noah
```

Now we're on our new branch, which again we can check using `git branch`.

Now we can make changes on this branch, for instance updating `code.R` so that is actually does something with the pi approximation by adding the lines:
```
diameter <- 5
circumference <- 5 * pi_approx
```
Let's now commit these changes, which will write a new entry in the history of this branch only.
```
git commit -a -m "Calculated the circumference of a cirlce with diameter of 5."
```
When we now switch back to `master` with `git checkout master`, we won't see those changes that we made in the `Fix-Code-For-Noah` branch.

When we want to, we can bring the changes made on `Fix-Code-For-Noah` into `master` by merging the branch in:
```
git checkout master
git merge -m "Merging code that does something" Fix-Code-For-Noah
```
We now have the option to delete said branch if we're done with it
```
git branch -d Fix-Code-For-Noah
git branch
```
and see that it no longer shows up in our list of branches.

## Conflicts
In the above example, no changes were made on Master while we were working on our other branch, so the merge went smoothly. But what happens when we make changes in parrallel, introducing conflicts between the two branches?

Let's create another new branch. And check it out.
```
git branch Scary-Conflict
git checkout Scary-Conflict
```

Now let's make some changes to the `code.R` script to use a different circle diameter:
```
diameter <- 10
```
and let's commit these changes
```
git commit -a -m "New circle diameter"
```
Follow this up by switching back to the master branch (`git checkout master`), but instead of immediatley merging, let's create some changes to `code.R` here as well. Say here we want to use a diameter of 2.
```
diameter <- 2
```
Let's commit these changes too
```
git commit -a -m "Using an even better approximation of pi"
```
Now when we try
```
git merge Scary-Conflict
```
we get an error because of conflicts, telling us that the merge failed. This puts us into a new branch `master | merge`. On this branch when we open the conflicting file, we get a view of the current file on `master` and the one on `Scary-Conflict`. 

It's up to us now which version to keep. We can remove the conflict that we don't want, and save the file. After which we can commit this change, which will update the conflict and get back to `master`.

Conflicts can be difficult to resolve, and neccessarily are a manual process since it is up to you to decide what should be kept.

# Github Collaboration

## Linking New Repository

So far, everything has been done locally. To work with others, we'll host out repo online using Github, though other options exist.

On github, create a new repository. Then we can connect our local repository that we've been working on and connect it to the remote.
```
# Connect remote
git remote add origin <url of remote repository>
# Set target branch
git branch -M master
# Push contents from local to remote.
git push -u origin master
```
Now when we visit the url, we can see all of the contents and history of our repository.

In general, `git push` will send the commits from your local repository to the remote. We can see this by making changes in one of our files, committing, and then using `git push`.

The opposite of this is `git pull`, which will send commits from the remote back down to your local repository, changes that may have come from another user.

Optionally, you can use `git pull` in two steps:
```
git fetch
git merge
```
The `fetch` command will get all the latest commits from remote, but not update your current branch, and then `merge` will update your current branch if you're ready to do so.

You should be committing, pushing and pulling often, so that teams have an up-to-date view of the repository at all times, and to keep changes, and thus potential conflicts, small.

## Cloning

Someone can also now **clone** your repository, or you could clone another. You do this very simply by taking the url of the repo and running
```
git clone <url>
```
and you can optionally name the folder that it will clone into something other than the repository name which it uses as default.

## Other Github features

### Viewing Commits
You can view past commits, and change logs on Github, which provide better views that in the terminal. You can also see branches visually, how many there are, where the branched off, etc.

### Issues
Issues allow you to publicly note work to be done on your project. You can title the issue, write about what exactly needs to be done, and do things like link the issue to a branch, label it as a bug, feature request, etc., and more.

### Pull Requests
Pull requests are there to essentially ask if a merge can happen. This allows your collaborators, or managers of the repository to take a look at the branch you want to merge, what changes there are, and have a discussion about these changes before commiting the merge. These can also be linked to issues.

### Wiki
Github wiki provides a place to describe your project in detail. You can have user guides, explanations of methods, etc. These are often written in markdown, and give new and existing users a resource to learn about your project and how to use it.

### Actions
Github actions can be set up to run actions when commits are pushed to remote. These can include unit tests for example, and let you know when something in the code is failing.
