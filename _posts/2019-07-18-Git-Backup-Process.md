---
title:  "Git Backup Process"
excerpt_separator: "<!--more-->"
categories:
  - Guides
tags:
  - git
  - guide
---

This is a simple method to create backups of your repository, that can be easily updated while stored remotely.

<!--more-->

In the directory you want to store the repository backup run: 

```shell
$ git clone --mirror $url
```

If the repository contains LFS objects run:

```shell
$ cd backup-repo.git

$ git lfs fetch --all
```

Periodically run a remote update to keep the backup relevant:
```shell
$ git remote update --prune
```
> Running a remote update with `--prune ` will remove any branches that have also been removed from the original repository.

When you need to restore from the backup run:
```shell
$ git push --mirror https://hostname/exampleuser/new-repository.git

$ git lfs push --all https://hostname/exampleuser/new-repository.git
```
> This will push the backup of your repo to a new repository for use.

> A note on LFS, it requires a HTTP server to download (serve) LFS objects, so using the backups locally will require extra steps not covered in this guide.