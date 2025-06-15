---
title:  "Fixing GIT LFS Missing Objects"
excerpt_separator: "<!--more-->"
categories:
  - Guides
tags:
  - git
  - guides
---

Simple fix for Git LFS `[404] Object does not exists on the server` errors.

<!--more-->

> [e70f2f6f92ac50774e87160ee70c163607f69f49518fde060731b28a38a88ca8] Object does not exist on the server: [404] Object does not exist on the server

The long string at the start of the this error message is the OID of the file that it can't find. In order to purge the object from the Git history we need to find out what it's file name is, we can do that by running:

```shell
$ git log --all -p -S <OID>
```
Next, download [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)

BGG allows for much quicker, and easier, git history rewriting. As an alternative to `git filter-branch`.

> Before beginning the history rewrite, ensure all work has been pushed to the remote, as we'll need a fresh clone once done.

## Rewrite History

First, create a mirror of the repo:

```shell
$ git clone --mirror git://example.com/your-repo.git
```
> Make a copy of the cloned repo at this point, in the event of a catastrophic error during re-write.

Then use BFG to delete the references to the missing files:
```shell
$ java -jar bfg.jar --delete-files <FILE_NAME> your-repo.git
```
This will scour the Git history, delete the specified file, and then write it out of commit history.
Make sure the files have been culled from history, and then run:
```shell
$ cd your-repo.git
$ git reflog expire --expire=now --all && git gc --prune=now --aggressive
```
Finally, push the changes back to the remote:
```shell
$ git push
```
This updates all refs on the remote, everyone will now need to re-download fresh clones and should have no more issues with missing LFS objects.
