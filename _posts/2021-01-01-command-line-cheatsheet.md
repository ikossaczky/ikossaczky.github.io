---
layout: post
title:  "Command line cheatsheet"
categories: ["bash", "jekyll"]
redirect_from: /bash/2021/01/01/command-line-cheatsheet.html
---

Useful commands that I tend to forget exactly when I need them (to be extended):

## Bash
`cp -as source dest`: copies source into dest, replacing all files by symbolic links to the respective counterparts in source. Source has to be absolute path
- -a: recursive (archive)
- -s: replace files by symlinks

`du -hs dir`: prints size of directory dir
- -h: human readable
- -s: sum (otherwise also sizes of all subdirectories will be listed)

`find / -iname "filename"`: search in root (`/`) for file with "filename".
- -iname: case insensitive

`find . -regex "regex"`: search in current directory (`.`) for all **pathes** matching regex (the full path should match regex, not just some of its substrings as in the case of grep).
- -iname: case insensitive

`find / -iname "filename" 2>&1 | grep -v "Permission denied"`: following this [stackoverflow post](https://stackoverflow.com/questions/30851708/permission-denied-in-find-why-do-we-need-21), this also removes the "Permission denied" messages from the output.
- 2>&1: routing stderr to stdout
- `grep -v`: invert match; select non-matching lines

`find dir -type f | wc -l`: number of files in directory dir and recursively in all subdirectories
- `-type f`: find all files
- `wc -l`: count all lines

`grep -nr "string" .`: search string in current folder (`.`)
- -n: show line number
- -r: recursive

`grep -n -P "regex" file`: search for expressions matching regex in file
- -P: use pcre (perl) regex standard

## Jekyll
`bundle exec jekyll serve`: run webpage locally

## Docker
`docker ps`: show all running containers
- -a show all (also stopped) containers

`docker cp <container-id>:/abs-src-path abs-dest-path`: copy file/folder from abs-src-path in running docker container with container-id to abs-dest-path on the host machine.

## Anaconda
`conda create -n myenv python=3.7 anaconda`: creates fresh anaconda enviroment "myenv" with python version 3.7 and all basic libraries

`conda create --name myclone --clone myenv`: clone "myenv" into "myclone"

`conda info --env`: list all enviroments, and indicate the current one.

## PIP
`pip install --no-cache-dir package`: pip install, ignoring cache, package is package or local path to package.

## Git & Github:
`git branch -d branch-name`: delete branch branch-name locally

`git checkout -f target-branch`: force checkout into target-branch (discarding local changes)

`git clean -n`: show local untracked files that will be removed with `git clean -f`

`git clean -f`: remove local untracked files

`git commit --amend -m "message"`: append staged changes to the last commit and change commit message to "message" (without -m you will be prompted to edit the original message)

`git config --global user.email "EMAIL"`: set global user e-mail

`git config --global user.name "NAME"`: set global user name

`git config --list`: show git config (global, and if inside repo, local)

`git diff --ignore-space-at-eol PATH`: changes in PATH. `--ignore-space-at-eol` takes care of not messing with LF/CRLF when working on both linux and windows

`git push origin --delete branch-name`: delete branch branch-name at remote origin

`git rebase -i HEAD~5`: interactive rebase of last 5 commit in current branch

`git remote set-url origin git@github.com:USERNAME/REPOSITORY.git`: set github remote origin url to work with ssh keys

`git reset --hard target-branch`: set the HEAD of current branch to head of target-branch

`git reset --soft HEAD~1`: undo last commit. `--soft` preserves the changes from the undone commit (so that they can be commited again), to drop the changes use `--hard`

`git revert -n HEAD~4..`: revert last 4 commits, without creating commit messages. Without `-n` flag, you would need to interactively specify commit messages for each revert. To get out of the revert-process without commit just with staged reversions use `git revert --quit`.

## ssh
`ssh-keygen -t rsa -C "EMAIL"`: create ssh key pair

