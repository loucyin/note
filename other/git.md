# git使用笔记

## 创建办本库

1. 配置全局用户信息

  ```
  git config --global user.name "userName"
  git config --global user.email "email@email.com"
  ```

2. 配置当前版本仓库的用户信息

  ```
  git config user.name "userName"
  git config user.email "email@email.com"
  ```

3. 创建版本库

  ```
    git init
  ```

4. 简单的几个命令

  ```
    git add readme.md
    git commit -m "添加readme"
    git add -A
    git status
    git log
    git log --pretty=oneline
    git reflog
    git diff
    git reset
    git checkout
  ```

## 创建远程仓库

1. 创建SSH key

  ```
   ssh-keygen -t rsa -C "youremail@example.com"
  ```

2. 创建远程版本库(SSH) 版本库-> settings->Deploy keys->Add deploy key<br>
  填写title，将生成的.pub中的内容，复制到key里面。勾选Allow write access，最后Add key。
3. 本地添加远程版本库<br>
  通过SSH的方式添加：

  ```
   git remote add origin git@github.com:loucyin/note.git
  ```

   通过https的方式添加：

  ```
   git remote add origin https://github.com/loucyin/note.git
  ```

4. 推送至github

  ```
    git push origin master
  ```

   如果使用https的方式推送到远程库，需要验证用户名和密码。
5. clone远程版本库

  ```
    git clone git@github.com:loucyin/note.git
  ```

6. git合并远程分支<br>
  从本地push到远程仓库，在远程仓库做了更改；<br>
  接着，又在本地仓库做了更改。<br>
  现在我要把本地仓库push到远程仓库。

  ```
    E:\workspace\note>git push origin master
    Warning: Permanently added the RSA host key for IP address '192.30.252.122'
    he list of known hosts.
    To git@github.com:loucyin/note.git
     ! [rejected]        master -> master (fetch first)
    error: failed to push some refs to 'git@github.com:loucyin/note.git'
    hint: Updates were rejected because the remote contains work that you do
    hint: not have locally. This is usually caused by another repository pushin
    hint: to the same ref. You may want to first integrate the remote changes
    hint: (e.g., 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.
  ```

  哇哦，失败了。咋办呢

  1. 把远程仓库拉下来，做个分支
  2. 然后创建本地分支
  3. add 文件，commit
  4. 合并分支
  5. push到远程库

  ```
    git pull origin
    git checkout -b branch origin/master
    git add --all
    git commit -m "commit"
    git merge master
    git push origin branch
  ```

  远程库就多了一个branch 分支。

  git 删除远程分支,该分支不能为default branch：

  ```
  git push origin --delete branch
  git push origin :branch
  ```

## clone OR pull , 还是 fetch + merge 吧

知乎关于 clone pull 的区别：

> git clone是把整个git项目拷贝下来，包括里面的日志信息，git项目里的分支，你也可以直接切换、使用里面的分支等等 git pull相当于git fetch和git merge。其意思是先从远程下载git项目里的文件，然后将文件与本地的分支进行merge。

用 pull 还是 fetch + merge ?

> 为什么不用 git 的 pull？<br>
> 虽然 git pull 大部分时候是好的，特别是如果你用CVS类型的方式使用Git时，它可能正适合你。然而，如果你想用一个更地道的方式（建立很多主题分支，当你需要时随时改写本地历史，等等）使用Git，那么习惯把 git fetch 和 git merge 分开做会有很大帮助。

## Git 打包 Zip

```
git archive --format zip --output file.zip master
```

## Git checkout
- 从当前版本拉一个分支，没有提交历史
```
git checkout --orphan latest
```

- 合并两个版本库的代码，版本库有不同的提交历史
```
git merge other --allow-unrelated-histories
```

从远程分支拉取一个分支：

```
git checkout --track origin/test
git checkout -b test origin/test
```

## git cherry-pick

- 使用 git 在 master 分支上创建一个 commit
- 现在 branch1 也需要这个更改

```
# 查看刚才提交的 id
git log
# 切换到 branch1 分支
git checkout branch1
# 使用修改 id 为 f4cd
git cherry-pick f4cd
```

## git rebase

修改一个问题，第一次修改完成，提交，但是测试有 bug ，改 bug ，又提交一次，其实两次提交只是修改一个问题。

```
git add --all
git commit -m "修复 bug"
git add --all
git commit -m "修复 bug"
git rebase -i HEAD~2
```

之后会进入修改界面，几个关键字：

```
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
```

- pick 使用这次提交
- reword 使用这次提交，但是可以修改提交信息
- edit 使用提交
- squash 这次提交会合并到上一个提交里面
- fixup 和 squash 差不多，但是会丢弃提交内容

> 假如你在开发过程中对于一个小功能有多次提交，你想要把他们合并成一个提交，或者你想给提交排序，那这个功能就很有用了。但是假如你已经把 commits 推到了远程分支，那么就不可以使用 rebase 了。

## 参考链接：

- [知乎 clone 和 pull 的区别](https://www.zhihu.com/question/39595933)
- [Git 少用 Pull 多用 Fetch 和 Merge](http://www.cnblogs.com/flying_bat/p/3408634.html)
- [git 文件打包命令](https://segmentfault.com/a/1190000002443283)
- [Git查看、删除、重命名远程分支和tag](http://zengrong.net/post/1746.htm)

> **_我最擅长从零开始创造世界，所以从来不怕失败，它最多也就让我一无所有。_**<br>
> ---- Git少用Pull多用Fetch和Merge
