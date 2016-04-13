# git使用笔记　　
- 创建办本库
    1.  配置全局用户信息
```
git config --global user.name "userName"
git config --global user.email "email@email.com"
```
    - 配置当前版本仓库的用户信息
```
git config user.name "userName"
git config user.email "email@email.com"
```
    - 创建版本库  
    ```
    git init
    ```
    - 简单的几个命令
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
- 创建远程仓库
    1. 创建SSH key
    ```
    ssh-keygen -t rsa -C "youremail@example.com"
    ```
    - 创建远程版本库(SSH)
    版本库-> settings->Deploy keys->Add deploy key  
    填写title，将生成的.pub中的内容，复制到key里面。勾选Allow write access，最后Add key。
    -  本地添加远程版本库  
    通过SSH的方式添加：
    ```
    git remote add origin git@github.com:loucyin/note.git
    ```
    通过https的方式添加：
    ```
    git remote add origin https://github.com/loucyin/note.git
    ```
    - 推送至github  
    ```
    git push origin master
    ```
    如果使用https的方式推送到远程库，需要验证用户名和密码。
- clone远程版本库
    ```
    git clone git@github.com:loucyin/note.git
    ```
- git合并远程分支  
    从本地push到远程仓库，在远程仓库做了更改；  
    接着，又在本地仓库做了更改。  
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
  * 然后创建本地分支
  - add 文件，commit
  - 合并分支
  - push到远程库  

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
  ```
  参考链接：[Git查看、删除、重命名远程分支和tag](http://zengrong.net/post/1746.htm)
