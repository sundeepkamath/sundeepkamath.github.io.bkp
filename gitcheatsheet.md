---
layout: post
title: Git Cheatsheet
---

This is a list of frequently used git commands for reference.  
I'll be updating this section on an ongoing basis.  
For the complete list of git commands I use the following [link](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf).

- Initialize a new git repository  
```
git init
```
- List all new or modified files to be committed  
```
git status
```
- Add files to the staging area  
```
git add [filename]
```
- Add files from staging area permanently to source control  
```
git commit -m "[descriptive comment]"
```
- Display history of all git commits  
```
git log
```
- Display historical git commits as single liner comments  
```
git log --oneline
```
- Check details of remote git repository url  
```
git remote -v
```
- Set remote repository url to origin  
```
git remote add origin [http://remoteurl.git]
```
- If existing remote origin url needs to be replaced with a different one
```
git remote set-url origin [http://newurl.git]
```
- Push local changes to remote git repository  
```
git push -u origin master
```
- Clone remote git repository  
```
git clone [http://remoteurl.git]
```
