# MyBot Git 分支开发 SOP

本文档适用于 MyBot 的日常开发。目标是让 `main` 始终保持可运行，并让每项改动都能独立检查和回退。

## 1. 分支约定

- `main`：稳定主分支，只接收经过检查的改动。
- `feature/<名称>`：新功能，例如 `feature/onebot-connection`。
- `fix/<名称>`：缺陷修复，例如 `fix/message-duplicate`。
- `docs/<名称>`：文档改动，例如 `docs/git-sop`。
- `chore/<名称>`：配置、依赖或仓库维护，例如 `chore/add-test-script`。

分支名使用小写英文、数字和连字符，不使用空格。

## 2. 开始开发前

```bash
cd /home/admin/MyBot
git status
git switch main
git pull --ff-only origin main
```

`--ff-only` 可以避免 `git pull` 意外创建合并提交。如果工作区不干净，先提交当前改动，或确认改动归属后再处理，不要直接覆盖或删除。

## 3. 创建开发分支

从最新的 `main` 创建并切换到新分支：

```bash
git switch -c feature/onebot-connection
git branch --show-current
```

需要将新分支推送到远程时：

```bash
git push -u origin feature/onebot-connection
```

设置上游分支后，后续可以直接运行 `git push`。

## 4. 开发和提交

开发过程中查看改动：

```bash
git status --short
git diff
```

只暂存本次任务相关文件，避免无意提交凭据或运行数据：

```bash
git add path/to/file
git diff --cached
git diff --cached --check
git commit -m "feat: add OneBot connection"
```

推荐的提交类型：

- `feat`：新功能
- `fix`：缺陷修复
- `docs`：文档
- `test`：测试
- `refactor`：不改变行为的代码重构
- `chore`：配置、依赖和仓库维护

一个提交尽量只表达一件事。禁止提交 `.env.prod`、Token、Cookie、密码、数据库、日志、虚拟环境和 NapCat 运行目录。

## 5. 合并前同步主分支

完成功能并提交后，确认工作区干净并获取远程最新状态：

```bash
git status
git fetch origin
git merge origin/main
```

如果没有冲突，运行项目对应的格式化、静态检查和测试。当前仓库尚未建立统一测试脚本时，不要猜测命令；应先查看 `README.md` 和 `scripts/`。

同步后的分支需要更新到远程时：

```bash
git push
```

## 6. 处理合并冲突

发生冲突后查看文件：

```bash
git status
```

打开标记为冲突的文件，人工处理以下标记之间的内容：

```text
<<<<<<< HEAD
当前分支内容
=======
待合并分支内容
>>>>>>> branch-name
```

处理完成后：

```bash
git add path/to/resolved-file
git commit
```

如果确认暂时不应继续本次合并，可以安全取消尚未完成的合并：

```bash
git merge --abort
```

不要使用 `git reset --hard` 或 `git checkout --` 解决冲突，这些命令可能覆盖未保存的改动。

## 7. 合并到 main

### 7.1 本机直接合并

```bash
git switch main
git pull --ff-only origin main
git merge --no-ff feature/onebot-connection
```

`--no-ff` 会保留功能分支的合并节点，让历史边界更清晰。合并后再次运行相关测试并检查仓库：

```bash
git diff --check
git status
git push origin main
```

### 7.2 通过 GitHub Pull Request 合并

多人协作或希望保留代码审查记录时，推荐：

1. 将开发分支推送到远程。
2. 在 GitHub 创建从开发分支到 `main` 的 Pull Request。
3. 检查文件改动、自动测试和敏感信息。
4. 审查通过后在 GitHub 合并。
5. 回到服务器同步主分支。

```bash
git switch main
git pull --ff-only origin main
```

不要同时在本机和 GitHub 对同一分支各合并一次。

## 8. 删除已合并分支

确认功能已经进入 `main`：

```bash
git branch --merged main
```

删除本地和远程分支：

```bash
git branch -d feature/onebot-connection
git push origin --delete feature/onebot-connection
```

`-d` 会阻止删除尚未合并的分支。不要随意使用强制删除参数 `-D`。

## 9. 推荐的完整示例

```bash
cd /home/admin/MyBot
git switch main
git pull --ff-only origin main
git switch -c feature/onebot-connection

# 修改文件并完成测试
git status --short
git add path/to/changed-file
git diff --cached
git diff --cached --check
git commit -m "feat: add OneBot connection"
git push -u origin feature/onebot-connection

# 合并前同步主分支
git fetch origin
git merge origin/main
# 再次运行测试
git push

# 本机合并；如果使用 GitHub PR，则不要执行这一段
git switch main
git pull --ff-only origin main
git merge --no-ff feature/onebot-connection
# 再次运行测试
git push origin main

# 清理分支
git branch -d feature/onebot-connection
git push origin --delete feature/onebot-connection
```

## 10. 每次合并前检查清单

- 当前分支名称正确。
- 改动只包含当前任务内容。
- 没有 Token、Cookie、密码、私钥、数据库或日志。
- 已查看 `git diff` 和 `git diff --cached`。
- 已运行最相关的格式化、静态检查和测试。
- `git diff --check` 没有错误。
- 开发分支已同步最新 `main`。
- 合并后再次完成测试。
- `git status` 符合预期。
- 确认合并成功后再删除开发分支。
