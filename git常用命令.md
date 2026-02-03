

## 常用命令

```bash
#创建一个目录
$ mkdir learngit
#进入到该文件夹
$ cd learngit
#查看当前路径
$ pwd

# 查看分支
git branch
# 查看所有分支
git branch -a
# 查看分支详情
git branch -v
# 查看远程分支
git branch -r

# 创建分支：从当前分支创建,相当于备份一个分支
git branch <name>
# 检查目标分支
git branch | grep master
# 切换分支：
git checkout <name>
# 创建并切换分支：
git checkout -b <name>

# 合并某分支到当前分支：
git merge <name>
# 查看分支的合并情况：
git log --graph --pretty=oneline --abbrev-commit

# 删除本地分支
git branch -d branch-name
# 强制删除
git branch -D branch-name
# 删除远程分支
git push origin --delete branch-name

# 查看修改的文件
git status
# 查看对文件的修改
git diff readme.txt   
# 显示文本的内容
cat readme.txt   

```

### git log命令

>git log命令显示从最近到最远的提交日志 ，如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline参数
>
>上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
>回退到上一个版本:git reset --hard HEAD^
>
>git reset --hard 3628164(这是版本号)  ，
>版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了


### 工作区和暂存区
>提交后，用`git diff HEAD -- readme.txt`命令可以查看工作区和版本库里面最新版本的区别

>命令`git checkout -- readme.txt`意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
>一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
>一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

>`git checkout -- file`命令中的`--`很重要，没有--，就变成了“切换到另一个分支”的命令

>命令`git reset HEAD file`可以把暂存区的修改撤销掉（unstage），重新放回工作区,例如：`git reset HEAD readme.txt`
>git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本

```bash
# 通常直接在文件管理器中把没用的文件删了，或者用rm命令删了
rm test.txt
git status

# 现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit
git rm test.txt
git commit -m "remove test.txt"

#另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
git checkout -- test.txt
```


## 基础命令
```bash
# git init命令把这个目录变成Git可以管理的仓库
# 如果你没有看到.git目录，那是因为这个目录默认是隐藏的，用ls -ah命令就可以看见
git init
# 把文件添加到仓库
git add README.md
# 把文件提交到仓库,-m后面输入的是本次提交的说明
git commit -m "first commit"
# 强制重命名当前分支为 master名字
git branch -M master
# 添加远程仓库地址
git remote add origin https://github.com/zhouxinfei/git-skills.git
# 推送到远程仓库：第一次推送并建立跟踪，后续推送只需 git push
# -u 或 --set-upstream  设置上游跟踪关系，建立本地分支和远程分支的关联
git push -u origin master
```


## `git branch -M master` 详细解释
### 基本语法
```bash
git branch -M <新分支名>
```

#### 具体到你的命令
```bash
git branch -M master
# 等同于
git branch --move --force master
```

### 参数解释

| 参数 | 全称 | 含义 |
|------|------|------|
| `-M` | `--move --force` | 强制移动/重命名当前分支 |
| `master` | - | 新的分支名 |

### 实际作用
#### 1. 重命名当前分支为 `master`
```bash
# 假设当前分支名为 main
git branch
# 输出：* main

# 重命名为 master
git branch -M master

git branch
# 输出：* master

# 查看提交历史（保持不变）
git log --oneline -3
# 输出：
# abc1234 feat: 添加新功能
# def5678 fix: 修复 bug
# ghi9012 init: 初始提交

# 推送到远程（更新远程分支名）
git push -u origin master

# （可选）删除远程的 main 分支
git push origin --delete main
```

#### 2. **强制**重命名（即使目标分支已存在）
```bash
# 如果已经存在 master 分支
git branch
# 输出：
#   master
# * feature-branch

# 普通重命名会失败
git branch -m master
# 错误：fatal: 一个分支名 'master' 已经存在

# 使用 -M 强制重命名（覆盖）
git branch -M master
# 成功：feature-branch → master（原 master 分支被删除/覆盖）
```

### `-m` vs `-M` 对比

#### 安全重命名（`-m`）
```bash
# 只有当目标分支不存在时才重命名
git branch -m new-name

# 等同于
git branch --move new-name
```

#### 强制重命名（`-M`）
```bash
# 即使目标分支存在也强制重命名（覆盖）
git branch -M new-name

# 等同于
git branch --move --force new-name
```

### Q1: 为什么我的分支名是 `main` 不是 `master`？
```bash
A: GitHub 在 2020 年 10 月后默认使用 `main` 代替 `master`

# 更新默认分支（在 GitHub 网页设置）
Settings → Branches → Default branch
```

### Q2: 如何撤消重命名？
```bash
# 如果刚重命名，可以使用 reflog
git reflog
# 找到重命名前的 commit
# 例如：abc123 HEAD@{2}: branch: renamed ...

# 恢复
git reset --hard abc123
```
记住：分支只是指向提交的指针，重命名不会影响你的代码内容！



## 配置 Git 代理

--global参数是设置全局代理，--local参数是设置当前仓库的代理，去掉--global参数则只设置当前仓库的代理

```bash
### 设置代理(Windows CMD终端)
git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890

### 设置 HTTP 代理（如果有）(Windows PowerShell终端)
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

### 或者设置 socks5 代理（如果有）(Windows PowerShell终端)
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890

### 查看当前代理设置
git config --global --get http.proxy

### 取消代理设置
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## 配置 Git 用户名和邮箱
```bash
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。





## 参考
- https://liaoxuefeng.com/books/git/introduction/index.html

