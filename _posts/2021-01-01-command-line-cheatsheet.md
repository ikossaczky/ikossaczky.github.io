---
layout: post
title:  "Command line cheatsheet"
categories: ["bash", "jekyll"]
redirect_from: /bash/2021/01/01/command-line-cheatsheet.html
---

Useful commands that I tend to forget exactly when I need them (to be extended):

## Bash
`bc <<< '2^3+2.059'`: evaluates the arithmetic expression with `bc` program. `<<<` is here-string.

`cp -as source dest`: copies source into dest, replacing all files by symbolic links to the respective counterparts in source. Source has to be absolute path
- -a: recursive (archive)
- -s: replace files by symlinks

`df -h`: reports filesystem disk usage as reported by filesystem's primary superblock (difference df vs. du see e.g. [link](http://linuxshellaccount.blogspot.com/2008/12/why-du-and-df-display-different-values.html))
- -h: human readable

`diff -bE -C 0 file1 file2`: diff in context mode between file1 and file2
- -b: ignore changes in the amount of white space
- -E: ignore changes due to tab expansion
- -C 0: context mode with 0 context lines (positive inte can be used instead of 0 to show context lines)

`du -hs dir`: prints size of directory dir (estimates file space usage/)
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

`pidof process-name`: finds process IDs of all processes with name process-name

`pushd folder`, `popd`: `pushd folder` changes working directory to folder. By calling `popd`, the working directory is reseted back. 

`tree dir`: print tree of directory dir
- `-L 5`: only till tree depth 5
- `-P pattern`: only files matching wildcard pattern, but all dirs
- `--prune --matchdirs`: also apply matchingwith `-P` to dirs.

`!23`: run 23th command from `history`

`!23:p`: print 23th command from `history`, and save it to history. The command can be edited after pressing up-arrow.

## Sed
General form (outputs always into stdout):
- `sed sed-command`: applies sed command sed-command on lines of standard input
- `sed sed-command file`: applies sed command sed-command on lines of file
- `sed -f sed-script file`: applies commands from file sed-script sed-command on lines of file
- `sed -f sed-script`: applies commands from file sed-script sed-command on lines of standard input
- `sed -f - file`: applies commands from standard input string on lines of file

Most sed commands can be preceded by adress so that the format of the command is *adress*command. Adress examples:
- `n`: where n is positive integer (line number)
- `$`: last line
- `/regex/`: lines matching regex 

Sed commands (always begin with a single letter):

`p`: print the line. Needs to be executed wit `-n` as otherwise sed prints every line anyway

`s/regex/replacement/`: replaces first occurence of regex in each line with replacement. replacement can use backreferences (`\1`-`\9`) and `&` to match the text matched by regex

`s/regex/replacement/g`: the same as above, but replaces all occurences

`y/charset1/charset2`: transliterate charset1 into charset2

## Btrfs
`cp --reflink[=option] src dst`: if no option or if option is `always`, creates reflink copy (copy-on-write), if option is `never`, standard copy is done, and if option is `auto` reflink copy is done if possible and standard copy as a fallback option 

`btrfs filesystem du -s`: better du alternative for btrfs filesystem (see [link](https://unix.stackexchange.com/questions/436585/get-size-of-btrfs-directory-which-may-contain-subvolumes))
- -s: sum (otherwise also sizes of all subdirectories will be listed)
- outputs "Total" size (as the size would be without CoW/snapshots functionality) "Exclusive" size (space which is not shared) and "Set shared" size (shared space). To my understanding, sum of exclusive and set shared size is most near to the actual disk usage.

`btrfs subvolume show path`: show informations about subvolume mounted a path

`btrfs subvolume list path`: list all subvolumes of the filesystem the path belongs to

`sudo btrfs subvolume snapshot -r subvolume-src snapshot-dst`: from the subvolume mounted at subvolume-src ceate a snapshot at snpshot-dst
- `-r`: make the snapshot readonly, otherwise, it would be writable. Needed to send the snapshot to external device

`btrfs property get -ts snapshot-path`: outputs `ro=true` if snapshot is readonly and `ro=false` writable
- `-ts`: type subvolume (snapshot is also subvolume). see man.

`sudo btrfs property set -ts snapshot-path ro true`: set snapshot at snapshot-path to be readonly (or writable if `true` is replaced by `false`)

`sudo btrfs send snapshot-path | sudo btrfs receive destination-folder`: copies subvolume/snapshot at snapshot-path into the destination-folder on different drive. The subvolume/snapshot needs to be read only.

`sudo btrfs send -p parent-snapshot-path snapshot-path | sudo btrfs receive destination-folder`: copies subvolume/snapshot at snapshot-path into the destination-folder on different drive in form of increment with respect to snapshot at parrent-snapshot-path (faster & less memory consuming). The parent snapshot must exist at both parent-snapshot-path and in the destinatio-folder The subvolume/snapshot needs to be read only.

## Fedora related commands

`sudo dnf check-updates`: check for updates. sudo not needed.

`sudo dnf history list`: show dnf transaction history. sudo not needed.

`sudo dnf info package`: info on package (also indicates installed and available). sudo not needed

`sudo dnf install package`: install package

`sudo dnf list`: list all packages from repos. sudo not needed.

`sudo dnf list installed`: list all installed packages (from repos). sudo not needed.

`sudo dnf remove package`: uninstall package

`sudo dnf search package`: search for package (does not matter if installed). sudo not needed

`rpm -qf file`: print which package installed a file

## Jekyll
`bundle exec jekyll serve`: run webpage locally

## Docker
`ctrl+p, ctrl+q`: detach from running container

`docker attach id-or-name`: attach running docker container with id or name id-or-name

`docker build -f Dockerfile -t name context-dir`: build docker image from dockerfile Dockerfile, with name "name", and with context directory "context-dir"
- -f dockerfile, use - for stdin
- -t optionaly use name:tag to specify tag

`docker cp <container-id>:/abs-src-path abs-dest-path`: copy file/folder from abs-src-path in running docker container with container-id to abs-dest-path on the host machine.

`docker image ls` list images
- -a show also intermediate (default hides intermediate)

`docker images -a`: probably equivalent to `docker image ls -a`

`docker inspect object`: show information on object (container or image), e.g. IPAddress of the container

`docker kill container [container...]`: kill one or more containers

`docker login registry:port`: login into docker registry

`docker ps`: show all running containers
- -a show all (also stopped) containers

`docker rm container`: remove container

`docker rmi image`: remove image

`docker run -ti --gpus all --privileged -v /home:/home -p 127.0.0.1:7000:7000 image`: start a new container from image
- -ti: i for interactive, t for allocate pseudotti. basically allows interactive session with container
- --privileged: some privileged mode needed for accesing nvidia drivers (see [link](https://rickycorte.medium.com/installing-tensorflow-on-fedora-34-6d2f97651e60))
- --gpus all: to enable accessing all gpus
- -v /home:/home : mount container /home to host /home
- -p 127.0.0.1:7000:7003 : map container port 7003 to localhost (127.0.0.1) port 7000

`docker stop container`: stop docker container

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

`git for-each-ref --format='%(committerdate:short) %(refname:short)'`: prints all branches, preceded by they last commit date. `:short` in format string can be ommited to get more verbose date or name.

`git push origin --delete branch-name`: delete branch branch-name at remote origin

`git rebase -i HEAD~5`: interactive rebase of last 5 commit in current branch

`git remote set-url origin git@github.com:USERNAME/REPOSITORY.git`: set github remote origin url to work with ssh keys

`git reset --hard target-branch`: set the HEAD of current branch to head of target-branch

`git reset --soft HEAD~1`: undo last commit. `--soft` preserves the changes from the undone commit (so that they can be commited again), to drop the changes use `--hard`

`git revert -n HEAD~4..`: revert last 4 commits, without creating commit messages. Without `-n` flag, you would need to interactively specify commit messages for each revert. To get out of the revert-process without commit just with staged reversions use `git revert --quit`.

## vim
`:noh`: stop highlighting last search

## ssh
`ssh-keygen -t rsa -C "EMAIL"`: create ssh key pair

