---
title: merge multi git commit into one 
category: [git]
tag: [git]
---


# objective

*new carreer, new technical stacks.*

  Recently I started a new job which include many operations on cloud machine via jenkins tasks and ansible playbooks. During I finished the first feature on my branch, I push a lot git commits(which triggered the jenkins and ansible)  to test the syntax and flow are working. After the first version was stablized , I wanted to merge all commits into one.


## TL;DR

from this [article](https://dev.to/maxwell_dev/the-git-rebase-introduction-i-wish-id-had),
We can use :
 
 - git rebase
 
 ```bash
 git rebase -i [the commit hash u want to rebase to it]
 ```
 the command will give u an editor which u can choose actions and commits to operate

 - git push --force-with-lease


to achieve this.

 
