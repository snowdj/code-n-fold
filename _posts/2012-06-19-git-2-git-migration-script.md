---
layout: default
title: Git 2 git migration script
date: 2012-06-19
category: Programming
tags:
- git
- git migration
- git2git
- ruby
type: post
excerpt: Migrate a git repository via your local machine - all branches and tags included!

---

Here's a small ruby script I wrote to help me facilitate an upcoming git to git migration.
It takes two params, first the name of the repository to be migrated, second the name of the new remote repository.
The script is simple and straightforward, it clones the remote repository to the current directory, fetches all branches, creates a new mirror, pushes all branches and pushes all tags - done.
Feel free to use and further improve it...
Note: The new remote repository must already exist (bare).

{% gist 2956098 %}
