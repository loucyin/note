# git svn

公司使用 svn 做版本仓库，自己习惯用 git 管理代码。
## git svn clone
```
git svn clone svn://host/isap
```
可以同过 `-rN` 指定开始 clone SVN 版本号，可以通过 `--username` 指定 svn 仓库的账号。

## git svn 更换远程版本库
- 修改 .git/config 中的域名
- 运行 `git svn fetch`
- 改回原来的域名
- 运行 `git svn rebase -l`
- 在将域名改回
- `git svn rebase`生效

## 使用流程
- git svn 可以 clone svn 远程仓库，也可以将代码提交到 svn 远程仓库；
- 先使用 git svn 将 svn 仓库的代码拉下来，通过 --username 指定 SVN 用户名：
```
git svn clone svn://host/isap --username loucy
```
svn 仓库提交数太多，全部拉下来很慢，可以只拉最后一个版本，假设最后一个版本为 3480
```
git svn clone svn://host/isap -r3480
```
- 拉取的代码本地分支为 master
- 从 master 新建一个分支 bugfix，用于管理自己的提交，这样可以更加精细的控制代码的提交
- 当要提交的远程分支时，先切换到 master 分支，执行
```
git svn rebase
```
- 将 bugfix 分支合并到 master
- 提交代码到 svn
```
git svn dcommit
```

**最好不要在 master 分支修改代码，合并冲突是一件很烦人的事情**

## 参考链接
- [GitSvnSwitch](https://git.wiki.kernel.org/index.php/GitSvnSwitch)
