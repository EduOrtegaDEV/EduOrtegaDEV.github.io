---
title: "Using multiple git acounts"
date: 2026-01-05
categories:
  - git
tags:
  - git
  - profiles
---

Hello future Edu!

Working with multiple Git accounts on the same machine can quickly become confusing. One moment you’re committing to a personal project, the next you’re pushing code to a company repository, and suddenly Git is mixing identities. The result: commits tagged with the wrong email, SSH keys that don’t match, sending changes to the wrong git repo and that might cause problems.

This post is a reminder of how to set things up cleanly so future Edu doesn’t have to untangle the mess.

## The problem
By default, Git uses a single global configuration for your username and email. That works fine if you only ever use one account (Which is basically imposible). But when you need to separate personal and professional work, Git doesn’t automatically know which identity to apply, in fact it 'thinks' you are one person, regarless of the project you are working on.

## Configurations with includeIf
A practical way to manage multiple accounts is to use Git’s includeIf directive. This allows you to load different configuration files depending on the directory you’re working in.

``` ini
# ~/.gitconfig
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work
[includeIf "gitdir:~/personal/"]
    path = ~/.gitconfig-personal
```

and each file defines it's own identity

``` ini
# ~/.gitconfig-work
[user]
    name = Edu Work
    email = edu@company.com

# ~/.gitconfig-personal
[user]
    name = Edu Personal
    email = edu@personal.com
```

## What about ssh keys?
If we are using SSH keys for authentication, the best option is to generate separate keys for each account. Then we can configure which key to use for each profile in the ``~/.ssh/config`` file:

``` ini
Host work.github.com
    HostName github.com
    IdentityFile ~/.ssh/id_rsa_work
    User git
```
When cloning reference the custom host:

``` ini
git clone git@work.github.com:company/repo.git
```

## Bonus tip: Verified Commits
Want to look extra fancy? Add GPG signing keys per profile. That way, GitHub adds a shiny green “Verified” badge on your commits.

## Conclusion
Managing multiple Git accounts isn’t rocket science. Painful at first, but once you set up includeIf configs and SSH keys, everything falls into place.

And as always, happy coding!.
