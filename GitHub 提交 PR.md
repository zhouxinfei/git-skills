
# GitHub 提交 PR（Pull Request）完整指南

## 准备工作
### Fork 仓库
1. 访问要贡献的仓库（如 https://github.com/Boris-code/feapder）
2. 点击右上角的 Fork 按钮
3. 等待 Fork 完成，你会拥有一个自己的副本

### 克隆仓库到本地
```bash
# 克隆你 Fork 的仓库
[//]: # (http地址)
git clone https://github.com/zhouxinfei/feapder.git
[//]: # (ssh地址)
git clone git@github.com:zhouxinfei/feapder.git

# 进入目录
cd feapder

# 添加上游仓库（原始仓库）
git remote add upstream https://github.com/Boris-code/feapder.git

# 验证远程仓库
git remote -v
# 应该显示：
# origin    https://github.com/你的用户名/feapder.git (fetch)
# origin    https://github.com/你的用户名/feapder.git (push)
# upstream  https://github.com/Boris-code/feapder.git (fetch)
# upstream  https://github.com/Boris-code/feapder.git (push)
```

## 修复 Feapder RedisDB bug 并提交 PR
### 创建分支
```bash
# 同步最新代码
git fetch upstream  # 拉取上游最新代码（只下载，不合并）
git log --oneline upstream/master  # 查看拉取的内容, 查看上游 master 分支的提交历史,按q 退出
git checkout master  # 切换到master分支
git merge upstream/master  # 合并上游的 master 分支到本地的 master 分支

# 创建修复分支（推荐使用描述性名称）
git checkout -b fix/redisdb-kwargs-bug
```

#### upstream 解释
> upstream 是一个远程仓库的别名，通常指原始仓库（你 Fork 的那个仓库）

当你 Fork 一个仓库后：
- origin：指向你的 Fork（zhouxinfei/feapder）
- upstream：指向原始仓库（Boris-code/feapder）

基本语法
```bash
git fetch upstream
```
完整语法
```bash
git fetch upstream <远程分支名>:<本地分支名>
```

场景1：同步特定分支
```bash
# 拉取上游的 develop 分支
git fetch upstream develop

# 查看上游 develop 分支的内容
git log --oneline upstream/develop
```


场景2：将上游分支合并到本地
```bash
# 1. 切换到你的本地分支
git checkout my-feature-branch

# 2. 拉取上游最新代码
git fetch upstream

# 3. 合并上游 master 到你的分支
git merge upstream/master

# 或者使用 rebase（保持提交历史线性）
git rebase upstream/master
```

### 修复代码
修改代码，并添加测试用例

### 提交更改
```bash
# 查看修改的文件
git status

# 查看具体修改内容
git diff

# 添加修改的文件
git add feapder/db/redisdb.py
# 或者 添加所有修改
git add .

# 提交更改
git commit -m "fix: 修复 RedisDB kwargs 问题"

```

### 推送到 GitHub
```bash
# 推送到你的 Fork
git push origin fix/redisdb-kwargs-bug

# 如果分支不存在远程，使用：
git push --set-upstream origin fix/redisdb-kwargs-bug
```

这里推送可能会失败，这时需要设置代理，--global参数是设置全局代理，--local参数是设置当前仓库的代理，去掉--global参数则只设置当前仓库的代理
```bash
# 查看当前代理设置
git config --global --get http.proxy

# 设置代理(Windows CMD终端)
git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890

# 取消代理设置
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### 创建 Pull Request

#### 通过 GitHub 网页创建
1. 访问你的 Fork
  - 打开 https://github.com/你的用户名/feapder

2. 切换到你的分支
  - 点击分支下拉菜单
  - 选择 fix/redisdb-kwargs-bug

3. 创建 Pull Request
  - 点击 "Compare & pull request" 按钮
  - 或者点击 Pull requests 标签页 → New pull request


#### 填写 PR 信息

**PR 标题示例**：
```text
fix(redisdb): correct kwargs None check in RedisDB.__init__
```

**PR 描述模板**：
```markdown
## Description
Fixed the kwargs check in `RedisDB.__init__()` from `if kwargs is None:` to `if not kwargs:`.

### Problem
The original code incorrectly checks `if kwargs is None:`, but `kwargs` is always a dictionary (even if empty) in Python, never `None`. This causes the fallback to `setting.REDISDB_KWARGS` to never trigger when `RedisDB()` is called without arguments.

### Solution
Changed the condition to `if not kwargs:` which correctly checks for an empty dictionary.

### Changes Made
1. **feapder/db/redisdb.py**: 
   - Line XX: Changed `if kwargs is None:` to `if not kwargs:`

2. **tests/test_redisdb_kwargs.py** (optional):
   - Added test cases to verify the fix

### Testing
- [x] Tested with empty kwargs: `RedisDB()` now correctly loads from `setting.REDISDB_KWARGS`
- [x] Tested with provided kwargs: `RedisDB(host='localhost')` works as before
- [x] Added/updated unit tests

### Related Issues
Closes #XXX (如果有相关的 issue)

### Checklist
- [x] My code follows the code style of this project
- [x] I have performed a self-review of my own code
- [x] I have commented my code, particularly in hard-to-understand areas
- [x] I have made corresponding changes to the documentation
- [x] My changes generate no new warnings
- [x] I have added tests that prove my fix is effective or that my feature works
- [x] New and existing unit tests pass locally with my changes
```
