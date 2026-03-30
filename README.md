# using-git-lab
内网环境下git lab使用的tips

# Git 开发协作规范指南 (通用版)

本文档旨在规范项目组的 Git 操作流程，涵盖从环境初始化到分支推送的完整生命周期。

---

## 一、 基础环境配置
> **重要**：在进行任何提交操作前，请务必先配置身份信息，否则提交记录将显示为未知作者。

### 1. 设置全局身份
```bash
# 配置用户名（建议使用姓名拼音或英文名）
git config --global user.name "your_name"

# 配置邮箱（建议使用公司邮箱或常用邮箱）
git config --global user.email "your_email@example.com"
```

### 2. 验证配置
```bash
# 查看当前所有配置信息，确认 name 和 email 正确
git config --list
```

---

## 二、 仓库初始化与克隆
> **安全提醒**：推荐使用 **Token 嵌入式克隆**，但请勿在公开脚本中明文保存 Token。

### 1. 快速克隆指定分支
使用您的个人访问令牌 (Access Token) 代替账号密码：
```bash
# 格式：git clone -b [分支名] http://[用户名]:[Token]@[内网域名或IP]/[项目路径].git
git clone -b dev-branch http://<USER_ID>:<YOUR_TOKEN>@<INTERNAL_GIT_DOMAIN>/<GROUP>/<REPO_NAME>.git
```

### 2. (备选) 现有仓库修复远程连接
如果您本地已有代码但远程地址变更，请按此顺序操作：
```bash
# 1. 查看当前远程关联
git remote -v

# 2. 修改现有远程地址
git remote set-url origin http://<INTERNAL_GIT_DOMAIN>/<GROUP>/<REPO_NAME>.git

# 3. 或者删除旧关联重新添加
git remote rm origin
git remote add origin http://<INTERNAL_GIT_DOMAIN>/<GROUP>/<REPO_NAME>.git
```

---

## 三、 分支管理与协同
> **规范**：禁止在 `main` 或 `master` 分支直接修改代码，所有开发应在 Feature 分支进行。

### 1. 创建并切换开发分支
```bash
# 基于当前分支创建并切换到新功能分支
git checkout -b feature/your_branch_name

# 查看当前所在分支（带 * 号的为当前分支）
git branch
```

### 2. 处理远程冲突 (历史不相关问题)
如果在 `git pull` 过程中提示 `refusing to merge unrelated histories`，请执行：
```bash
git pull origin <branch_name> --allow-unrelated-histories
```

---

## 四、 持续开发与提交工作流
> 遵循 **Add-Commit-Push** 循环，确保每次推送前本地代码可运行。

### 1. 检查变更状态
提交前确认修改范围，避免误删或误改：
```bash
# 查看哪些文件被修改/新增
git status

# 查看具体的代码改动细节（按 Q 退出）
git diff
```

### 2. 暂存修改
```bash
# 方式 A：暂存所有修改（最常用）
git add .

# 方式 B：精准暂存指定文件（推荐，避免提交临时文件）
git add train.py configs/config.yaml
```

### 3. 本地提交
提交信息应遵循 `type: description` 规范（如 `feat:` 新功能, `fix:` 修复）：
```bash
git commit -m "feat: 适配数据集加载路径，优化预热机制"
```

### 4. 推送到远程
```bash
# 首次推送该分支时：建立本地与远程分支的跟踪关系
git push -u origin feature/your_branch_name

# 后续推送只需执行：
git push origin feature/your_branch_name
```

---

## 五、 常用辅助命令速查

| 命令 | 用途 |
| :--- | :--- |
| `git log --oneline` | 简洁查看提交记录历史（ID 和标题） |
| `git status` | 随时查看当前工作区和暂存区状态 |
| `git checkout main` | 快速切换回主分支 |
| `git pull origin <branch>` | 拉取远程更新并自动合并到本地 |
| `git reset --hard HEAD` | **慎用**：放弃所有本地未提交的修改，回滚到上一次提交 |

---

> **💡 贴士 (针对深度学习开发):**
> 1. **大文件处理**：模型权重文件（`.pth`, `.bin`, `.ckpt`）严禁提交到 Git。请务必检查 `.gitignore` 是否已包含这些后缀。
> 2. **隐私保护**：提交代码前，请检查配置文件中是否包含数据库密码、个人 Token 或内部服务器的具体 IP。

---
