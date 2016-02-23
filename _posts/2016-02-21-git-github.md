---
layout: post
title:  "Git and GitHub"
date:   2016-02-21
author: "Huang Qiang"
tags: [git, github]
---

## Configuration

git把config info存在三个位置

- System: to every user of this computer, user can overwrite, but this is default.
  path: /etc/gitconfig
- User: 最常用
  path: ~/.gitconfig
- Project: path: my_project/.git/config

不要直接编辑这些config，git提供了命令。例如：

```bash
$ git config --system
$ git config --global #user
$ git config #project

$ git config --global user.name "your_name"
$ git config --global user.email "your_email"

$ git config --list #check your config
$ git config user.name #return your name
```

还可以设定你需要的editor，例如([详细][1])：

```bash
$ git config --global core.editor "subl -n -w"
```

设置颜色

```bash
$ git config --global color.ui true
```

下载[auto-completion][2]，重命名之后在bash_profile里加上

```bash
# Enable tab completion
if [ -f ~/.git-completion.bash ]; then
  source ~/.git-completion.bash
fi
```

```bash
$ git help <command> #查找特定命令帮助
```

f b 按键可以前进或者后退浏览manual

---

Commit message best practices

- short single-line summary (less than 50 characters)
- optionally followed by a blank line and a more complete description
- keep each line to less than 72 characters
- write commit messages in present tense, not past tense
  - "fix bug" or "fixes bug", not "fixed bug"
- can add "ticket tracking numbers" from bugs or support requests
- can develop shorthand for your organization
  - "[css,js]"
  - "bugfix: "
  - "#38405 - " 
- Be clear and descriptive

Use git log

```bash
$ git log -n <number> #show number of recent commit
$ git log --since=YYYY-MM-DD #show commits since that date
$ git log --until=YYYY-MM-DD #show commits until that date
$ git log --author="<part of name>"
$ git log --grep="re" 
```

## Git Concepts and Architecture

Git uses three-tree architecture

- repo
- staging index
- working

这个结构让我们可以修改repo中的10个文件，可以把5个文件加到staging，作为一个commit。

在pull的时候通常从repo pull到working。

- Git generates a checksum for each change set
  - checksum algorithms convert data into a simple number
  - same data always equals same checksum
- data integrity is fundamental
  - changing data would change checksum
- Git uses SHA-1 has algorithm to create checksums
  - 40-character hexadecimal string(0-9, a-f)

Git takes the entire set of changes, runs them through in algorithm, and comes out with a 40 digit number. It attaches a bit of meta information to each snapshots, it has that commit number at the top, it has the parent commit the commit comes before it, the author of the commit, then the commit message. 

HEAD

Git maintains a reference variable called HEAD. Its purpose is to reference, or point to, a specific commit in the repo. As we make new commits, the pointer is going to change or move to point to a new commit. HEAD always points to the tip of the current branch in our repo. It has to do with our repo, not our staging index or working directory. It's about the repo the commits we've made by checking them in.

Another way to think of it is the last state of our repo or what was last checked out, the HEAD points to the parent of the next commit or it's where commit writing is going to take place.

HEAD always points to the tip of the currently checked out branch from the repo. We can look at HEAD in .git.

```bash
$ cat HEAD #ref: refs/heads/master
```

refs/head/master 含有这个branch的last checksum。

```bash
$ git log HEAD #返回last commit
```

git diff

当add file to staging index后，git diff只返回working directory的diff。要查看staged index

```bash
$ git diff --staged <file>
```

删除，重命名，都用git rm/mv 可以直接进入staging

```bash
$ git diff --color-words <file> #show diff in one-line
```


[1]: https://help.github.com/articles/associating-text-editors-with-git/
[2]: https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash



