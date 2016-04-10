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
