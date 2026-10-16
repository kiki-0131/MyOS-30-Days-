# Git 操作手册（MyOS-30-Days-）

本仓库：<https://github.com/kiki-0131/MyOS-30-Days->
本地路径：`D:\MyOS`（Git Bash 里是 `/d/MyOS`）

---

## 一、这个仓库是怎么搭起来的（完整记录）

### 第 0 步：网络（不做这步后面全部失败）

Clash 节点 `🇸🇬新加坡5` 挂了（测速 504），GitHub 连不上。
在 Clash Verge 里：`代理` → `🚀节点选择` → 选一个能用的节点（当时选的是 `🇺🇸美国圣何塞03`，389 ms）。

验证：

```bash
curl -x http://127.0.0.1:7897 -o /dev/null -w "%{http_code}\n" https://github.com
# 200 = 通；000 = 节点还是死的，换节点
```

### 第 1 步：配置 git 身份与代理（全局，只需一次）

```bash
git config --global user.name  "kiki-0131"
git config --global user.email "kiki-0131@users.noreply.github.com"
git config --global init.defaultBranch main
git config --global core.autocrlf true
git config --global core.quotepath false
git config --global http.https://github.com.proxy "http://127.0.0.1:7897"
```

- 邮箱用 GitHub 的 `noreply` 匿名邮箱，提交记录不暴露真实邮箱
- `http.https://github.com.proxy` 是**按域名限定**的，只让 GitHub 走代理，克隆国内仓库不受影响
- 这些都写在 `~/.gitconfig`（即 `C:\Users\34045\.gitconfig`），PowerShell 和 Git Bash 共用

### 第 2 步：建本地仓库

```bash
mkdir -p /d/MyOS
cd /d/MyOS
git init -b main
```

### 第 3 步：写文件

- `README.md` —— 目录规划 + 学习路线
- `.gitignore` —— 排除大文件与第三方工具包

### 第 4 步：首次提交

```bash
git add -A
git commit -m "chore: 初始化操作系统学习笔记仓库"
```

### 第 5 步：关联远端

```bash
git remote add origin https://github.com/kiki-0131/MyOS-30-Days-.git
```

### 第 6 步：推送

```bash
git push -u origin main
```

`-u` 把本地 `main` 与 `origin/main` 绑定，**以后只要敲 `git push` 就行**。

### 第 7 步：验证（别省）

```bash
git rev-parse main      # 本地 HEAD 哈希
git ls-remote origin    # 远端 refs
git status -sb          # 应显示 ## main...origin/main，无 "ahead"
```

两边哈希相同才算真的推上去了。

> 补充：这个仓库原本建在 `D:\Deep Seek工作区\os-notes`，后来整体搬到 `D:\MyOS`。搬迁时只要把 `.git` 文件夹一起搬走，**历史记录一条都不会丢**。

---

## 二、Git Bash 基础

### 打开方式

- 进 `D:\MyOS`，空白处右键 → `Open Git Bash here`
- 或开始菜单搜 `Git Bash` 打开后手动 `cd`
- 或双击 `C:\Program Files\Git\git-bash.exe`

### ⚠️ 路径写法（最容易踩的坑）

Git Bash 是 Unix 风格，**不能用 `D:\MyOS` 这种反斜杠路径**。

| 位置 | PowerShell | Git Bash |
| --- | --- | --- |
| 仓库 | `D:\MyOS` | `/d/MyOS` |
| 桌面 | `C:\Users\34045\Desktop` | `~/Desktop` |
| 带空格的目录 | `"D:\Deep Seek工作区"` | `"/d/Deep Seek工作区"` |
| git 配置 | `C:\Users\34045\.gitconfig` | `~/.gitconfig` |

```bash
cd /d/MyOS      # ✅
cd D:\MyOS      # ❌ 反斜杠被当转义符，路径失效
```

### 日常三条命令

```bash
cd /d/MyOS
git status                                  # 先看有什么改动
git add .                                   # 放进暂存区
git commit -m "notes: 第3章 中断处理"        # 提交
git push                                    # 推送
```

### 常用查看命令

```bash
git log --oneline --graph --all   # 历史（图形化）
git log --oneline -10             # 最近 10 条
git diff                          # 未暂存的改动内容
git diff --staged                 # 已暂存待提交的改动
git remote -v                     # 远端地址
git branch -vv                    # 分支与跟踪关系
git status -sb                    # 简洁状态 + 是否领先/落后远端
git config --global --list        # 所有全局配置
cat ~/.gitconfig                  # 直接看配置文件
```

### Git Bash 特有注意事项

| 现象 | 解决 |
| --- | --- |
| `fatal: not a git repository` | 不在仓库目录，`cd /d/MyOS` |
| 中文名显示成 `\344\270\255` | `git config --global core.quotepath false` |
| `git log` 卡住只有个 `:` | 进了分页器，按 `q` 退出 |
| `LF will be replaced by CRLF` | 正常提示，不是错误 |
| `Ctrl+V` 粘贴无效 | 用 `Shift+Insert` 粘贴、`Ctrl+Insert` 复制 |
| 命令敲一半想取消 | `Ctrl+C` |

---

## 三、日常流程

```bash
cd /d/MyOS
git pull                          # 多台电脑写笔记时先拉
# ...写代码 / 写笔记...
git status                        # 看改动
git add notes/                    # 只提交指定目录，或 git add .
git commit -m "notes: 第3章 中断处理与PIC"
git push
```

提交信息建议加前缀，方便日后 `git log` 检索：

| 前缀 | 用途 |
| --- | --- |
| `notes:` | 学习笔记 |
| `lab:` | 实验代码/报告 |
| `src:` | 内核源码 |
| `docs:` | 文档、说明 |
| `chore:` | 杂项、配置、gitignore |

---

## 四、换电脑 / 新环境

```bash
# ① 配置身份（一次即可）
git config --global user.name  "kiki-0131"
git config --global user.email "kiki-0131@users.noreply.github.com"
git config --global init.defaultBranch main
git config --global core.autocrlf true
git config --global core.quotepath false
git config --global http.https://github.com.proxy "http://127.0.0.1:7897"   # 有代理才需要

# ② 直接克隆（比"本地新建再推"省事）
cd /d/
git clone https://github.com/kiki-0131/MyOS-30-Days-.git MyOS
cd MyOS
```

---

## 五、排查

```bash
# 1. GitHub 通不通（90% 的问题在这）
curl -x http://127.0.0.1:7897 -o /dev/null -w "%{http_code}\n" https://github.com

# 2. 代理配置
git config --global --get http.https://github.com.proxy    # 应为 http://127.0.0.1:7897

# 3. 远端地址
git remote -v

# 4. 本地与远端是否一致
git status -sb
git ls-remote origin
```

| 报错 | 原因与处理 |
| --- | --- |
| `SSL/TLS handshake failed` / `Failed to connect` | Clash 节点挂了，换节点 |
| `! [rejected] main -> main (fetch first)` | 远端有本地没有的提交：`git pull --rebase` 后再 `git push` |
| `Permission denied (publickey)` | SSH 场景的密钥问题；本仓库用 HTTPS，不会遇到 |
| `fatal: remote origin already exists` | 改用 `git remote set-url origin <地址>` |

### 撤销操作

```bash
git reset --soft HEAD~1                # 撤销提交，改动保留在暂存区
git commit --amend -m "新的提交信息"    # 只改提交信息
git restore 文件名                      # 丢弃某个文件的未暂存改动（不可恢复）
git remote set-url origin <新地址>      # 换远端
```

---

## 六、.gitignore 说明

已排除的"体积炸弹"（`D:\MyOS` 下可以放心放大文件）：

| 规则 | 说明 |
| --- | --- |
| `.toolchain/` `dist/` `_extract/` `_scratch/` `_tools/` | 工具链与提取产物（教程目录里占 3.6 GB） |
| `*.raw` `*.img` `*.iso` `*.vhd` `*.bin` `*.hrb` | 磁盘镜像与编译产物 |
| `*.pdf` `*.epub` `*.mobi` | 电子书（版权 + 体积） |
| `*.o` `*.exe` `*.dll` `*.lib` `*.elf` `*.map` | 编译中间产物 |
| `tolset_h/` | 《30天自制操作系统》第三方工具包（含 .exe/.dll 二进制） |

想让某个被忽略的内容入库，把那行规则删掉或改窄（例如把 `tolset_h/` 改成 `tolset_h/*.exe`）即可。
