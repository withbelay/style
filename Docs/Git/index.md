# Git

[Return to Table of Contents](/README.md)

## Table Of Contents

* [**General**]((#general))
* [**Commits**]((#commits))
* [**Branches**]((#branches))
* [**Pull Requests**]((#pull-requests))

## **General**

### **Github tags**

We have tags on github for Pull Requests and Issues. These should be used whenever possible.  
If there is a need for a new tag, discuss during a dev meeting or with the CTO.

### **Verified Commits**

Using Verified Commits is preferred but not required.  
See the [**Github Docs**](https://docs.github.com/en/github/authenticating-to-github/managing-commit-signature-verification) for more info

### **Git Config**

Run the following command to set your real name as your git name:  
`git config --global user.name "<Name>"`

Setting a preferred editor will be useful when dealing with squashing commits or merge conflicts, this can be set with
`git config --global core.editor "<editor>"`

### **Actually Using The Git CLI**

This is **not** required, github desktop can do pretty much everything needed, the only exceptions being squashing merges and deleting local branches

Current Branch and Edited files: `git status`  
Adding files to commit: `git add <files>` or `git add *`  
Committing: `git commit "<commit name>"` use flag `-m` if signing commits  
Checking out a branch: `git checkout <BranchName>`  
Creating a branch: `git checkout -b <BranchName>`  
Push to origin: `git push`  
Pull from origin `git pull`  
Merge `dev` into current branch `git merge dev` (make sure to pull from `dev` first)  
Delete all branches locally: `git branch --list | grep -v "\*" | grep -v "dev" | xargs -n 1 git branch -D`

## **Commits**

Git commits should follow the following rules in order to keep the git log clean and readable.

### 1. Body ###

Git commits with extensive bodies should be avoided. If there is a need to be that descriptive in a commit it should be a PR, if its part of a PR it can be added as a note to the main description of the PR or as a discussion. Commit messages aren't looked at frequently in our workflow so important information should never be there.

If creating commit messages with bodies seperate the subject line from the body with a blank line.

### 2. 50 Char Limit ###

Git commit messages should be approximately under 50 characters. This should be long enough to provide an accurate 1-liner of what a commit does. Note: Github truncates messages over 72 characters so that is the hard limit, though exceeding 50 should be avoided.

Using common abbreviations such as `+` instead of `and` can both help reduce commit message size, as well as making commit messages faster to read

Sample config setting to add rulers in VSCode for git-commit if helpful:
```
"[git-commit]": {
  "editor.rulers": [50, 72]
}
```

### 3. Sentence Capitlization ###

Capitlize the first word and proper nouns.

### 4. Avoid Punctuation ###

Punctiation at the end of a commit messages is a waste of characters. If commits need to be split into multiple sentences and `+` won't suffice, they should have a more descriptive note/discussion comment on the relevant PR.

### 5. Imperative Mood ###

Using the imperative mood in commit messages is clearer and follows Git's own conventions.

The imperative mood is essentially just a command. Something like: "Move logic file to hooks folder"
A good way to check if a commit message is imperative is to see if it fills in the end of this sentence:

> If applied, this commit will <ins><i>your commit message</i></ins>

## **Branches**

`dev` is the main branch for development. All PRs should open here.

branches should be in the format `<Type>/<Name>` where `<Type>` is something like `feature`, `chore`, `fix`, etc. And `<Name>` is the name of the branch. e.g. `feature/better-text-highlight-parsing`

## **Pull Requests**

Before opening a Pull Request make sure the branch is up to date with `dev`, when working on branches for a long period of time it is useful to regularly merge `dev` into the current branch or rebase the branch of `dev`. This is done to prevent merge issues and to prevent overwriting code that was recently merged into `dev`.

When opening a Pull Request and any relevant github labels, and write a clear description as to what the goal of the Pull Request is (linking to a specific trello card can be useful). For more complicated Pull Requests checklists can be extremely useful.

There is no need to request approval for Pull Request, any PR that doesn't have the `WIP` label will be looked at within a day or two. If a specific person should review the code, then a review should be requested from them.

### **Reviewing Pull Requests**

If a branch has lint issues or merge issues, it should be marked as `WIP`, and the author should be asked to resolve issues

A Code Review should both test the code by running it and testing relevant features, as well as look at the actual code to ensure that it follows best practices and company guidelines. If any problems are found the reviewer should request changes. If no issues are found the reviewer should mark the PR as approved and merge.

Note: The reviewer doesn't need to merge if there is room for discussion about the inclusion of something in the Pull Request. In this case a question should be posed to the author of the Pull Request and the `Awaiting Response` label should be added. See [Frontend PR #301](https://github.com/investingwolf/wolf-frontend/pull/301) for an example.

### **Squashing and Merging Code**

It is strongly preferred that code is squashed prior to review, the steps for this are listed below.

It is recommended to squash code prior to review / merge since it will prevent unnecessary clutter on the `dev` branch. The reasons to use the manual squash and merge technique outlined below is due to Github's "squash and merge" feature not actually squashing code. The way Github's "squash and merge" works is more akin to a rebase of `dev` than anything. This will result in extremely inaccurate git history that is fragmented between git and Github. This can cause issues when trying to find out when or by who code was written. **It is strongly advised not to use the built in Github squash**, not squashing code is better.

### **Steps for Squashing Code** 

1. `git rebase --interactive HEAD~N` where `N` is the number of commits  
a text editor will then open, change all unwanted commits to `squash` instead of `pick`. Save the editor and close it, git should resume. If there are any merge issues git will ask to resolve them, afterwards use `git rebase --continue` to force git to continue.

2. Once git finishes the rebase the user will be allowed to edit the commit message for all of their commits, most of the time this should just ential commenting out unwanted commit messages.

3. force push the branch to origin using `git push -f`
