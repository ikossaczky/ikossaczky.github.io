---
layout: post
title: "Problem with storing complex commands in bash variables"
date: 2021-09-05 22:00:00 +0100
categories: ["bash"]
---
 
The following code snippet works as expected:
```bash
[igor@asus sandbox]$ var="echo aaa"
[igor@asus sandbox]$ $var
aaa
```
However, this one not anymore, at least I expected different output at first:
```bash
[igor@asus sandbox]$ var="echo aaa; echo bbb"
[igor@asus sandbox]$ $var
aaa; echo bbb
```
What? The variable expansion is done before commands are executed and the output one would expect is
```
aaa
bbb
```
Moreover the first echo works, and seemingly interprets ";" as argument instead of command separator. 

This behaviour manifests itself in other situations like piping with \| , redirection with >, as well as with command substitution instead of variable expansion. In internet most answers can be reduced to "[use eval](https://unix.stackexchange.com/questions/91390/how-to-store-pipe-in-a-variable)" (indeed, `eval "$var"` works as expected), "[use functions](https://stackoverflow.com/questions/66681848/bash-storing-a-command-in-a-variable-cmd-and-then-running-it-with-cmd-fails)", or "complex commands saved in variable [do not work](http://mywiki.wooledge.org/BashFAQ/050)" (ok, I see, that but why?)

The answer can be found in bash manual, though not in the section about parameter expansion, but rather in the section on [Shell Operation](https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Shell-Operation):
> ...Basically, the shell does the following: <br>
> ...<br>
> 3 . Parses the tokens into simple and compound commands (see Shell Commands). <br>
> 4 . Performs the various shell expansions (see Shell Expansions), breaking the expanded tokens into lists of filenames (see Filename Expansion) and commands and arguments. <br>
> ...

This means, the shell at first decides what are simple and what are compound commands, in other words, "detects" the compound commands. The expansions (e.g. variable expansion) happens only in the next step, and when a new semicollon or pipe is appears during this process, well, the compound commands are already settled at this timepoint (and are not going to be revised), therefore it is taken as literal (in this case argument of echo).

