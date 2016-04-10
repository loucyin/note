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
    ```
- 创建远程仓库
    1. 创建SSH key
