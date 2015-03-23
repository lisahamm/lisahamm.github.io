---
layout: post
title: "Git Basics"
date: 2015-03-23 10:22:35 -0500
comments: true
categories:
---

### What is Git?

Git is a popular Version Control System that records changes made to files over time. This offers many benefits, such as the capability of reverting changes and comparing changes in files. <!--more-->
If you make changes and suddenly your program stops working, Git makes it easy to revert back to a working state. Git also comes in handy when collaborating with others on a project. Team members are easily able to view who made specific changes at specific time points.

Git is an incredibly useful tool to a developer at any stage in their learning. I would suggest learning the basics of Git as early as possible.

### Getting started with Git

Conceptually, Git allows users to make changes in a working directory, add specific modified files to a staging area, and commit staged files with messages. Beginning to use Git in a project is fairly straightforward. If you do not yet have Git on your computer, you can download it [here](http://git-scm.com/downloads). Once you have Git downloaded, cd into your project's root directory in the terminal. To initialize a new git repository, type:

```
git init
```
It is a good idea to get in the habit of typing `git status` before and after every git command you execute. This will allow you to see the status of your files and double check the files are actually being staged and committed the way you think they are. Running `git status` at this point will return:

```
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        # All files in the project folder will be listed here
```

Next, you will need to get the files you would like to be tracked into the new Git repository you created. This will require two steps. First, the files need to be staged. To do this for individual files, type:

```
git add <filename>
```
Alternatively, if you would like to do this for all of your files at once, type:

```
git add .
```
Once you think the files have been added to the staging area, it is a good idea to double check with `git status`, which should return:

```
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  new file:   # your filename
```

Once the files have been added to the staging area, the second step is to actually commit the proposed changes with the following command:

```
git commit -m "Commit message"
```
This will commit your changes, along with your message, to the HEAD of the git repository.

### Now what?

At this point, if you are using a remote repository, you can go ahead and push your changes to it. Otherwise, you are now in good shape to begin making changes to your files. Make sure to track future changes as you go by adding and committing them. It is important to make small, focused commits when possible. This will make it easier to look back at your project changes later on. It also makes your life much easier if you need to revert changes. Prior to staging changes, you can run `git diff` to view changes made to the files. It is a good idea to do this so you know exactly what you are committing (avoid committing commented-out code, print, puts, and p statements) and thus, will be able to include an accurate message with the commit.

### Work on branches

By default, when a Git repository is initialized, it is created on the "master" branch. It is a good idea to become in the habit of creating alternate branches to use for development. These branches can be merged with the master branch once proper changes have been made to them.

To create a new branch and simultaneously switch to it, type the following:

```
git checkout -b branch_name
```

To switch back to the master branch:

```
git checkout master
```
Branches can be merged with the following command:

```
git merge <branch>
```

### Reverting changes

Luckily, Git understands mistakes are inevitable and provides ways to revert changes. To discard changes in the working directory (those that haven't yet been staged or committed), the following command will revert your file to its state at the last commit:

```
git checkout -- <file>
```
To remove a file from the staging area, without altering the working directory, use the following command:

```
git reset <file>
```
To remove all files from the staging area, without altering the working directory, you can simply use `git reset`.

To remove all uncommitted changes from both the staging area and the working directory, use:

```
git reset --hard
```
### Takeaway
Git is a powerful tool one can take advantage of by knowing just a handful of basic commands. Learning to use Git early on during solo/personal projects will be incredibly useful when it comes time to work with a team of developers. Remember to make small, focused commits with meaningful commit messages--I guarantee your future self will thank you.