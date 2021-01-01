---
layout: post
title:  "Command line cheatsheet"
categories: bash
---

Useful commands that I tend to forget when I need them (to be extended):

## Bash
`grep -nr "string" .`: search string in current folder (`.`)
- -n: show linenumber
- -r: recursive

`find / -iname "filename"`: search in root (`/`) for file with "filename".
- -iname: case insensitive

`find / -iname "filename" 2>&1 | grep -v "Permission denied"`: following this [stackoverflow post](https://stackoverflow.com/questions/30851708/permission-denied-in-find-why-do-we-need-21), this also removes the "Permission denied" messages from the output.
- 2>&1: routing stderr to stdout
- `grep -v`: invert match; select non-matching lines

## Jekyll
`bundle exec jekyll serve`: run webpage locally

## Docker
`docker ps`: show all running containers:
- -a show all (also stopped) containers

`docker cp <container-id>:/abs-src-path abs-dest-path`: copy file/folder from abs-src-path in running docker container with container-id to abs-dest-path on the host machine.

## Anaconda
`conda create --name myclone --clone myenv`: clone myenv into myclone

`conda info --env`: list all enviroments, and indicate the current one.

## PIP
`pip install --no-cache-dir package`: pip install, ignoring cache, package is package or local path to package.

