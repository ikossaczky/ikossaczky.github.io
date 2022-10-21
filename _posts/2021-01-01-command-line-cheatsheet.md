---
layout: post
title:  "Command line cheatsheet"
categories: ["bash", "jekyll"]
redirect_from: /bash/2021/01/01/command-line-cheatsheet.html
---

Useful commands that I tend to forget exactly when I need them (to be extended):

- [Bash](#bash)
- [sed](#sed)
- [awk](#awk)
- [Btrfs](#btrfs)
- [Fedora related commands](#fedora-related-commands)
- [Jekyll](#jekyll)
- [Docker](#docker)
- [Anaconda](#anaconda)
- [PIP](#pip)
- [Git and Github](#git-and-github)
- [vim](#vim)
- [ssh](#ssh)

## Bash
`basename path`: extracts basename from the path

`bc <<< '2^3+2.059'`: evaluates the arithmetic expression with `bc` program. `<<<` is here-string.

`cp -as source dest`: copies source into dest, replacing all files by symbolic links to the respective counterparts in source. Source has to be absolute path
- -a: recursive (archive)
- -s: replace files by symlinks

`cut -d' ' -f3`: split standard input rows by space delimeter and extract third field from every row

`df -h`: reports filesystem disk usage as reported by filesystem's primary superblock (difference df vs. du see e.g. [link](http://linuxshellaccount.blogspot.com/2008/12/why-du-and-df-display-different-values.html))
- -h: human readable

`diff -bE -C 0 file1 file2`: diff in context mode between file1 and file2
- -b: ignore changes in the amount of white space
- -E: ignore changes due to tab expansion
- -C 0: context mode with 0 context lines (positive inte can be used instead of 0 to show context lines)

`du -hs dir`: prints size of directory dir (estimates file space usage/)
- -h: human readable
- -s: sum (otherwise also sizes of all subdirectories will be listed)

`export VAR[=VALUE]`: exports variable VAR into the enviroment so that it can be used in the child processes
- `export -p`: list exported variables

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

more `grep` options:
- omit `file`: search in standard input
- `-A2`: print also 2 context lines after the match
- `-B1`: print also 1 context line before the match
- `-G`/`-E`/`-P`: basic/extended/perl (BRE/ERE/PCRE) regexp syntax
- `-F`: do not search for regex,p but for string literal
- `-v`: invert match; select non-matching lines

`head -n 5 [FILE]`: prints first 5 lines from file, if no file specified first 5 lines of stdin
- -n x: print first x lines. If prefixed with - (e.g. ` head -n -5`),prints all lines except of the last x.

`jobs -l`: lists background jobs in the terminal session. `-l` for listing also PIDs.

`jq '.param[2] = 5' file.json`: processes file.json, changes third element (element 2) of param to be 5 (example); the processed file is returned on stdout. If `file.json` is ommited, processes standard input (in json format).

`ls -d $PWD/*`: list full paths of files/directories in the current working directory
- `-d`: list files/directories matching the (possibly wildcard) argument
- `$PWD`: variable holding path to current working directory

`pidof process-name`: finds process IDs of all processes with name process-name

`pushd folder`, `popd`: `pushd folder` changes working directory to folder. By calling `popd`, the working directory is reseted back. 

`rsync -am --include='*/' --include='*string1*' --exclude='*' src dst`: sync `src` into `dst`, but exclude all files, which do not include string1 and prune empty folders in the destination `dst`.
- `-a`: archive mode; recurse into directories, try to keep permissions, keep symlinks as symlinks, etc.
- `-m`: prune empty folders
- `--include='*/' --include='*string1*'`: do NOT exclude any directories and any files/folders with string1 in name. This has higher priority than all following excludes, but lower than all preceeding excludes.
- `--exclude='*'`: exclude everything (what was not explicitly included before). This has higher priority than all following includes, but lower than all preceeding includes. Note that `--include='*/'` was necessary, otherwise rsync would not even descend into directories to find files matching `*string1*`.

more `rsync` options:
- `--ignore_existing`: skip updating files that exist in `dst`

`tail -n 5 [FILE]`: prints last 5 lines from file, if no file specified last 5 lines of stdin
- -n x: print last x lines. If prefixed with + (e.g. ` tail -n +5`), prints all lines starting with the line x.

`tree dir`: print tree of directory dir
- `-L 5`: only till tree depth 5
- `-P pattern`: only files matching wildcard pattern, but all dirs
- `--prune --matchdirs`: also apply matchingwith `-P` to dirs.

`!23`: run 23th command from `history`

`!23:p`: print 23th command from `history`, and save it to history. The command can be edited after pressing up-arrow.

`${var/pattern/string}`: return content of variable var with the first occurence of (wildcard) pattern replaced by string

`${var//pattern/string}`: return content of variable var with the all occurences of (wildcard) pattern replaced by string

`${var/#pattern/string}`: return content of variable var with the (wildcard) pattern at the beggining of $var replaced by string

`${var/%pattern/string}`: return content of variable var with the (wildcard) pattern at the end of $var replaced by string

`${var#pattern}`: return content of variable var with the leading part matching (wildcard) pattern removed (shortest match)

`${var##pattern}`: return content of variable var with the leading part matching (wildcard) pattern removed (longest match)

`${var%pattern}`: return content of variable var with the trailing part matching (wildcard) pattern removed (shortest match)

`${var%%pattern}`: return content of variable var with the trailing part matching (wildcard) pattern removed (longest match)


## sed
Sed supports basic regex by default. To use extended regex, use the `-E` flag.

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
Without adress, commands are aplied on every line 

Sed commands (always begin with a single letter):

`i text`: insert text before the current line

`a text`: append text after the current line

`p`: print the line. Needs to be executed wit `-n` as otherwise sed prints every line anyway

`s/regex/replacement/`: replaces first occurence of regex in each line with replacement. replacement can use backreferences (`\1`-`\9`) and `&` to match the text matched by regex

`s/regex/replacement/g`: the same as above, but replaces all occurences

`y/charset1/charset2`: transliterate charset1 into charset2

## awk
Awk supports extended regex.
General form of calling commands:
- `awk 'awk-commands-string'`: applies awk commands from awk-commands-string on STDIN.
- `awk 'awk-commands-string' file`: applies awk commands from awk-commands-string on file content.
- `awk -f awk-script`: applies awk commands from file awk-script on STDIN.
- `awk -f awk-script file`: applies awk commands from file awk-script on file content.

Awk program stucture:
```
PATTERN1 {COMMANDS1}
PATTERN2 {COMMANDS2}
...
```
Awk applies COMMANDSx sequantially to all records (by default lines) matching PATTERNx. Commands in COMMANDSx should be separated by new line or by `;`

**awk patterns**:

`BEGIN`: matches before the first record. Suitable for initialization

`END`: matches after the last record. Suitable for final results print

`$3 ~ /regex/` matches records whith third field containes the regex

`$0 ~ /regex/` or `/regex/` matches records which contain the regex

`$1 > 5 && $2=="aaa"`: matches records which have first field number larger than 5, and second field is string "aaa" 

**awk commands:**

`x=5`, `x=x+$2`: set variable x to 5, set variable x to sum of itself and the value in the second field

`print $3, "aaa", $2`: print the third field of the record, then output field separator, then "aaa", again output field separator, and finally second field of the record

`print $3 "aaa" $2`: print the third field of the record, "aaa", and second field of the record concatenated into a single string

**awk special variables**:
- `$0`: current line content
- `$3`: content of the third field in current line
- `NF`: number of fields in the current line
- `NR`: record number; increases each time a line is read
- `FS`: input field separator; default is space
- `OFS`: output field separator: default is space
- `RS`: input record separator: default is newline
- `ORS`: output re cord separator: default is newline
- 
For more on awk see [linuxcommand.org](http://linuxcommand.org/lc3_adv_awk.php)

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

`sudo dnf upgrade`: search and install updates for all packages.
- `sudo dnf update`: deprecated alias

`sudo dnf --installroot=path-to-alternative-root --releasever=/ install package`: install package to alternative root (see [link](https://linuxhint.com/install-package-to-a-specific-directory-using-yum/))

`rpm -qf file`: print which package installed a file

`rpm -ql package`: print all files installed by a package

`rpm -q package --last`: show package update history for a package


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
`pip install --force-reinstall package==1.0.2`: force reinstall a pip package with specific version 1.0.2

`pip install --no-cache-dir package`: pip install, ignoring cache, package is package or local path to package.

`pip install -r requirements.txt`: install all packages from requirements.txt

## Git and Github:
`git branch -d branch-name`: delete branch branch-name locally

`git branch -a --contains commit-hash`: print branches which contain commit `commit-hash`
- `-a`: all (also remote branches), otherwise only local branches will be printed

`git branch -m old_branch_name new_branch_name`: rename branch locally

`git checkout -f target-branch`: force checkout into target-branch (discarding local changes)

`git checkout --theirs file`: accept their version to resolve current merge conflict in a file.
- use `.` instead of `file` to apply to all files
- use `--ours` instead of `--theirs` to accept our version

`git checkout target-branch -- file-or-folder`: copies file-or-folder from target-branch into current branch

`git clean -n`: show local untracked files that will be removed with `git clean -f`

`git clean -f`: remove local untracked files

`git commit --amend -m "message"`: append staged changes to the last commit and change commit message to "message" (without -m you will be prompted to edit the original message)

`git config --global user.email "EMAIL"`: set global user e-mail

`git config --global user.name "NAME"`: set global user name

`git config --list`: show git config (global, and if inside repo, local)

`git diff --ignore-space-at-eol PATH`: changes in PATH. `--ignore-space-at-eol` takes care of not messing with LF/CRLF when working on both linux and windows

`git diff --staged`: diff between staged files and HEAD 

`git diff HEAD~3..HEAD`: diff between current commit and 3 commits before. `git diff HEAD~3..HEAD~1` prints difference between 3 commits before and 1 commit before 

`git for-each-ref --format='%(committerdate:short) %(refname:short)'`: prints all branches, preceded by they last commit date. `:short` in format string can be ommited to get more verbose date or name.

`git log foo bar ^baz`: git log for all commits reachable from (by following parents of) foo and bar but not reachable from baz

`git log --reflog`: git log of all commits reachable from all references (that means most commits excluding those rebased, amended, etc.)

`git push origin --delete branch-name`: delete branch branch-name at remote origin

`git push origin --force-with-lease`: force push, but only if no new commits were added to remote. Compares the reference on remote with the last fetched reference from remote
- using `--force` instead of `--force-with-lease` overwerites the remote origin unconditionaly.

`git rebase other_branch`: rebase our current branch on top of other_branch. Commits from other_branch are taken without changes, commits from current branch not present in other_branch are placed on top, which changes their commit id (SHA). Changes the current branch.

`git rebase --onto other_branch HEAD~5`: rebase last 5 commits from current branch on top of other_branch. Changes the current branch. 

`git rebase -i HEAD~5`: interactive rebase of last 5 commit in current branch

`git remote set-url origin git@github.com:USERNAME/REPOSITORY.git`: set github remote origin url to work with ssh keys

`git reset --hard target-branch`: set the HEAD of current branch to head of target-branch

`git reset --soft HEAD~1`: undo last commit. `--soft` preserves the changes from the undone commit (so that they can be commited again), to drop the changes use `--hard`

`git rev-parse HEAD_or_branch_or_tag`: returns commit of HEAD or branch or tag (or also expressions like HEAD~1)

`git rev-parse --abbrev-ref HEAD`: get branch of HEAD

`git revert -n HEAD~4..`: revert last 4 commits, without creating commit messages. Without `-n` flag, you would need to interactively specify commit messages for each revert. To get out of the revert-process without commit just with staged reversions use `git revert --quit`.

`git show commit-hash`: print informations about commit (outhor, date, diff...)

`git show branch_or_commit:path_to_file > local_path_to_file`: copies the file in path_to_file from branch_or_commit into local_path_to_file in the current branch

`git stash -- file`: stash only a single file "file"

`git tag [--[no-]merge commit] [-n]`: show (all) tags
- `--merge commit`: only list those reachable from commit
- `--no-merge commit`: only list those not reachable from commit
- `-n`: show tag message

## vim
vim supports basic regex.

`:noh`: stop highlighting last search

`/regex`: searches for regex. After pressing enter use `n` to serch for next expression matching regex

`:%s/regex/text/g`: global search for regex and replacement with text (supports backrefs)
- `%` line adresses to apply search-and-replace: all. If ommited, replacement will be performed only on the current line. Alternatively e.g. range `5,10` can be specified.
- `g`: replace all occurences in the lines. If ommited, only first occurence will be replaced

`yy`: copy current line

`dd`: delete current line

`dw`:  delete word under the cursor from the cursor position to the start of the next word

`diw`:  delete word under the cursor

`daw`:  delete word under the cursor and the space after or before it

`p`: past rectly copied or deleted line or word

## ssh
`ssh-keygen -t rsa -C "EMAIL"`: create ssh key pair

