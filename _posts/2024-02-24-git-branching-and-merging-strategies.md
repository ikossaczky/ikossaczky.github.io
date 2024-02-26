---
layout: post
title: "Git branching and merging strategies"
date: 2024-02-24 20:00:00 +0100
categories: git
---

Knowing git commands like push, pull, merge, add, commit and fetch is essential for collaboration on a git repository. However, when setting up a repository it is helpful to think about rules on how to use this command and don't let the repository sink into chaos. Fortunately, this is what git branching workflows are about.

## Git branching strategies

Some of the more widely used workflows are **Git flow**, **Github flow**, **Gitlab flow**, and **Trunk based development**. You can find of these under this [link](https://www.abtasty.com/blog/git-branching-strategies/), and more specifically, Git flow is also described [here](https://nvie.com/posts/a-successful-git-branching-model/) and Github flow [here](https://docs.github.com/en/get-started/using-github/github-flow). Given these pages, I'll not go into details, just sketch the main principles of Git flow and Github flow:

- **Github flow**: there is just one persistent branch: main. The developers create new feature branches from main, and develop only on these. When the feature is ready, the feature branch is merged back into main and deleted afterwards.

- **Git flow**, the older and more complex: there are 2 persistent branches: main and develop. Developers create new feature branches from develop, and when ready, also merge these back to develop. The branch main, which is meant to be always in a stable production-ready state, is being updated via merge from develop, possibly over dedicated release branches. Moreover, hotfix branches can be opened from- and merged into main, to "hotfix" urgent issues.

Now, you can choose any of these strategies, optionally adapt it or create your own strategy. However beside the explicit advantages and limitations, the strategy may also influence how you will perform merges.

## Merge types
Let us at first introduce the possible merge types and how are these supported in githubs PRs ([link](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/merging-a-pull-request#merging-a-pull-request)).

### Standard merge
In standard merge of branch B into branch A, a merge commit is created. This commit has not one but two parents: heads of branches A and B. After the merge the head of the branch A is set to the merge commit. This is what Githubs PR "Create a merge commit" button does, with the additional option `--no-ff` to disable fast-forward ([link](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges#merge-your-commits)): see below.

### Fast-forward merge
Consider this situation: you create a new branch B from HEAD of branch A, and after a few commit you want to merge it into branch A. However, there are no new commits in branch A, i.e., the branch is in the same state as it was when B was branched of.  In that case, git can simply set the head of branch A to the head of branch B - no merge commit is needed. This is called fas-forward merge. On one hand, it may be convenient, as it does not add a complex two-parent commit to the git history, but as we will see later it has also some drawbacks: therefore you can force git to do this by using the `--ff-only` option.

### Rebase merge
Rebase merge is actually a combination of rebase and fas-forward merge. In situation when you want to rebase merge branch B into branch A, you at first rebase branch B onto branch A. This means, all commits present in B but not present in A are stacked onto the HEAD of branch A. This of course changes the hashes of these commits, as the ancestors are changed, and possibly rebase conflicts may need resolution. This new B-stacked-on-A branch is then declared to be branch A, and a fast-forward into branch A is performed. Github supports this type of merging via "Rebase and Merge" button in the PR

### Squash merge
Similar to rebase merge with a single adaptation: all commits of B are squashed into a single commit before rebasing and fast-forwarding this single commit onto A. Supported in Githubs PRs by the "Squash and merge" button, and in command line by option `--squash`: however in the command line case, the squash commit gets only staged and needs to be commited afterwards.

