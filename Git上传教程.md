# 把本地项目上传到 GitHub —— 完整操作教程


## 一、先搞懂三个概念（很重要，看懂了后面才不会懵）

### 1. 三个区域：工作区 → 暂存区 → 仓库

```
你改代码的地方        临时待提交区        永久保存的快照
┌──────────┐        ┌──────────┐        ┌──────────┐
│  工作区   │  add   │  暂存区   │ commit │  本地仓库  │
│ (文件夹)  │ ─────► │  (购物车) │ ─────► │  (快照)   │
└──────────┘        └──────────┘        └──────────┘
```

- **工作区**：你在 VS Code 里编辑的文件
- **暂存区（Stage）**：像购物车，先挑好这次要提交哪些文件
- **提交（Commit）**：按下快门，把暂存区的东西拍个快照存进历史

### 2. 两个仓库：本地 和 远程

- **本地仓库**：你自己电脑上的 `.git` 文件夹，commit 只存在这里
- **远程仓库（Remote）**：GitHub 网站上的仓库，需要 `push` 才能把本地提交送上去

**关键理解**：`commit` 只保存到本地，`push` 才是真正上传到 GitHub。很多人以为 commit 完就上传了，其实没有。

### 3. 为什么有些文件不能上传

依赖包（`node_modules`、`.venv`）动辄几百 MB，GitHub 有体积限制且没必要传——别人拿到代码后跑一条 `npm install` 就能装回来。密钥文件（`.env`）传上去会被人盗用 API 额度。**所以上传前必须清理。**

---

## 二、第一次上传（完整流程，共 5 步）

### 第 1 步：整理要上传的文件

新建项目时，核心是写一个 `.gitignore` 文件，告诉 git 哪些东西不要：

```gitignore
# 依赖
node_modules/
.venv/

# Python 缓存
__pycache__/
*.pyc

# 密钥（最重要！）
.env

# 运行时数据
logs/
*.db
```

**检查清单**：上传前确认没有 `.env`、没有 `node_modules`、没有 `.venv`、没有个人隐私文件（简历等）。

### 第 2 步：初始化本地仓库

```powershell
cd <项目地址>

git init -b main
```

`git init` 会在文件夹里创建一个隐藏的 `.git` 目录，从此这个文件夹就被 git 接管了。`-b main` 指定主分支叫 main（GitHub 现在的默认叫法）。

**只需做一次。**

### 第 3 步：配置你的身份

```powershell
git config --global user.name "<用户名>"
git config --global user.email "<邮箱>"
```

⚠️ **注意**：引号必须用**英文半角引号** `""`，不要用中文引号 `""`。**只需做一次**（这台电脑上所有 git 项目共用）。

### 第 4 步：提交到本地仓库

```powershell
# 1. 把所有文件加入暂存区
git add .

# 2. 拍快照，写一句说明
git commit -m "Initial commit"

# 3. 看一眼状态（可选）
git status
```

看到 `nothing to commit, working tree clean` 就说明成功了。

⚠️ **git 命令成功时通常没有任何输出**，这是正常的，不要以为卡住了。有输出往往才是报错。

### 第 5 步：在 GitHub 建仓库 + 推送

**（1）先在网页上创建仓库**

打开 <https://github.com/new>，填写：
- Repository name：`aiops-agent-platform`
- 选 Public 或 Private
- ⚠️ **"Add a README"、".gitignore"、"License" 三项都不要勾选**（因为你本地已经有了，勾了会造成冲突）

点 **Create repository**。

**（2）关联远程仓库并推送**

```powershell
# 告诉 git，远程仓库在哪（origin 是这个地址的简称）
git remote add origin https://github.com/XuanKai1212/aiops-agent-platform.git

# 推送！-u 表示记住对应关系，以后直接 git push 就行
git push -u origin main
```

**第一次推送会弹出 GitHub 登录窗口**，登录并点绿色的 **Authorize** 授权，推送就会继续。之后就再也不用登录了（凭证会存在 Windows 凭据管理器里）。

看到类似下面的输出就是成功：

```
Enumerating objects: 150, done.
Writing objects: 100% (150/150), done.
To https://github.com/XuanKai1212/aiops-agent-platform.git
 * [new branch]      main -> main
```

---

## 三、以后每次改完代码（日常只需 3 条命令）

```powershell
cd D:\大模型项目\aiops-agent-platform

git add .                          # ① 把所有改动放进暂存区
git commit -m "修复了XX问题"         # ② 提交，写清这次改了什么
git push                           # ③ 推送到 GitHub
```

就这三步，不需要再 init、不需要再 remote add。

### 几个常用的辅助命令

| 命令 | 作用 |
|------|------|
| `git status` | 看当前有哪些文件改动了 |
| `git log --oneline` | 看提交历史 |
| `git diff` | 看具体改了哪些内容 |
| `git pull` | 把 GitHub 上的新内容拉到本地（别人改了或你在别的电脑改了时） |
| `git remote -v` | 查看远程仓库地址 |

### 只想提交部分文件？

```powershell
git add backend/app/main.py        # 只加这一个文件
git add frontend/src/              # 只加这个文件夹
```

### 手滑 add 错了？

```powershell
git reset                          # 撤销 add，改动还在
```

---

## 四、不想敲命令？用 VS Code 图形界面

VS Code 左侧的**源代码管理**图标（带分叉的那个）：

1. 改动的文件会列在上方
2. 点文件旁边的 **`+`** = `git add`
3. 上方输入框写提交信息，点 **✓ 提交** = `git commit`
4. 点 **同步更改 / 推送** 按钮 = `git push`

左下角显示的 `main` 旁边的数字（如 `0↓1↑`）表示有 1 个提交待推送。

**"发布 Branch" 按钮**：只在本地有提交、但 GitHub 上还没有对应仓库时出现，点了会自动走创建仓库 + 推送的流程。

---

## 五、常见错误速查（你这次遇到的全在里面）

### ❌ `SSL certificate problem: unable to get local issuer certificate`

**原因**：git 用 OpenSSL 验证证书，企业网络/安全软件拦截 HTTPS 时证书链断裂。

**解决**：改用 Windows 系统证书库
```powershell
git config --global http.sslBackend schannel
```
**不要用** `git config --global http.sslVerify false` 关闭验证，那等于裸奔。

---

### ❌ `Repository not found`

三种可能，按顺序排查：

1. **仓库没建** → 去 github.com/new 创建
2. **仓库是私有的且没登录** → 完成浏览器授权
3. **仓库改名过** → git 会提示 `This repository moved`，用新地址：
   ```powershell
   git remote set-url origin https://github.com/用户名/新仓库名.git
   ```

---

### ❌ 弹窗 `git-credential-manager: 字符串绑定无效`

**这是假警报。** 只要浏览器显示 "Authentication Succeeded"，凭证其实已经存好了。直接重试推送即可。

---

### ❌ VS Code 显示 `main...origin/main [gone]`

**原因**：本地的远程跟踪引用文件丢了。

**解决**：
```powershell
git fetch origin
git branch --set-upstream-to=origin/main main
```

---

### ❌ 网页显示 404，但代码明明推成功了

**原因**：仓库是私有的（未登录访问一律显示 404），或者网页缓存了旧的 404。

**判断方法**：登录 GitHub 后刷新，或用命令验证：
```powershell
git ls-remote origin
```
能列出提交哈希就说明推送是成功的。

---

### ❌ `Updates were rejected`（推送被拒绝）

**原因**：GitHub 上的仓库有你本地没有的提交（比如建仓库时勾选了 README）。

**解决**（仅限确认远程内容是自动生成的空文件时）：
```powershell
git push -u origin main --force
```
⚠️ 强制推送会覆盖远程内容，只在确定远程没有重要内容时使用。

---

##六、安全红线

1. **永远不要上传 `.env`** —— 里面有 API 密钥
2. 发现密钥泄露：立刻去对应服务商（OpenAI/DeepSeek 等）**吊销该密钥**，删掉 GitHub 上的文件也没用，因为提交历史里还在
3. 提交前养成习惯看一眼 `git status`，确认没有奇怪的文件被加进去

---

## 速查卡片（贴墙上那种）

```powershell
# 第一次
git init -b main
git add .
git commit -m "Initial commit"
git remote add origin <仓库地址>
git push -u origin main

# 日常
git add .
git commit -m "说明"
git push
```
