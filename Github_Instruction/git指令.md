# Git 指令手册（秋招备战版）

> 面向计算机秋招的一站式 Git 笔记：
> **第一部分** 常用命令速查（含参数解释与使用场景）
> **第二部分** 高频场景命令组合速查表
> **第三部分** 秋招经典面试问答 + 真实面经题
>
> 建议配合动手练习：开个临时仓库 `git init`，把每一条命令都敲一遍。

## 目录

- [第一部分：常用命令速查](#第一部分常用命令速查)
  - [一、配置 git config](#一配置-git-config)
  - [二、仓库初始化与克隆](#二仓库初始化与克隆)
  - [三、工作区与暂存区](#三工作区与暂存区)
  - [四、提交 commit](#四提交-commit)
  - [五、查看历史 log](#五查看历史-log)
  - [六、分支 branch](#六分支-branch)
  - [七、远程仓库 remote](#七远程仓库-remote)
  - [八、暂存现场 stash](#八暂存现场-stash)
  - [九、版本回退 reset revert reflog](#九版本回退-reset-revert-reflog)
  - [十、拣选提交 cherry-pick](#十拣选提交-cherrypick)
  - [十一、变基 rebase](#十一变基-rebase)
  - [十二、标签 tag](#十二标签-tag)
  - [十三、差异查看 diff](#十三差异查看-diff)
  - [十四、忽略文件 .gitignore](#十四忽略文件-gitignore)
  - [十五、定位 bug git bisect](#十五定位-bug-git-bisect)
- [第二部分：高频场景速查表](#第二部分高频场景速查表)
- [第三部分：秋招高频面试问答](#第三部分秋招高频面试问答)
- [附：秋招前必背清单](#附秋招前必背清单)

---

# 第一部分 常用命令速查

## 一、配置 git config

### 1. 设置用户名和邮箱（全局）

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

- `--global`：全局配置，对本机所有仓库生效；不加则只对当前仓库生效
- 使用场景：**首次安装 Git 必做**，提交记录会带上作者信息

### 2. 查看配置

```bash
git config --list
git config user.name
```

- 使用场景：检查用户名、邮箱、远程地址等配置是否正确

### 3. 常用辅助配置（了解即可）

```bash
git config --global core.editor vim          # 修改默认编辑器
git config --global core.autocrlf true       # Windows 下自动转换换行符
git config --global alias.co checkout        # 配置别名，之后可用 git co
```

---

## 二、仓库初始化与克隆

### 1. 本地初始化仓库

```bash
git init
```

- 无参数：在当前目录生成 `.git` 隐藏目录，项目开始版本管理
- 使用场景：本地新项目，还没有远程仓库时

### 2. 克隆远程仓库

```bash
git clone <url>
git clone <url> myproj
git clone -b dev <url>          # 克隆后直接切到 dev 分支
git clone --depth 1 <url>       # 浅克隆，只拉最近一次提交（大仓库加速）
```

- `<url>`：远程仓库 https 或 ssh 地址
- `myproj`：可选，指定本地目录名，默认用仓库名
- 使用场景：拉取已有远程仓库到本地开发

---

## 三、工作区与暂存区

> 三个核心区域要能脱口而出：
> - **工作区**：磁盘上实际看到的文件
> - **暂存区（index / stage）**：`git add` 之后、等待提交的快照
> - **本地仓库（HEAD / repository）**：`git commit` 产生的提交历史

### 1. 查看状态

```bash
git status
git status -s
```

- `-s`：简短输出，每个文件一行，最常用
- 使用场景：随时查看哪些文件新增、修改、删除，哪些已暂存

### 2. 添加文件到暂存区

```bash
git add <filename>
git add .              # 当前目录及子目录下的所有变更（含删除）
git add -A             # 整个仓库的所有变更：新增/修改/删除
git add -p             # 交互式选择大文件里的部分改动
```

- `.` 与 `-A` 的区别只在**范围**：`.` 只覆盖当前目录及以下，`-A` 覆盖整个仓库；当前 Git 版本两者都会处理已跟踪文件的删除
- 使用场景：写完代码，把要提交的变更送入暂存区

### 3. 撤销暂存（文件保留在工作区）

```bash
git reset HEAD <filename>
git restore --staged <filename>    # git 2.23+ 推荐
```

- 使用场景：`git add` 加错文件，移出暂存区继续改

### 4. 丢弃工作区修改（危险！本地改动直接丢失）

```bash
git checkout -- <filename>
git restore <filename>             # git 2.23+ 推荐
```

- 使用场景：文件改乱了，想放弃本地改动、恢复到最后一次提交的状态

---

## 四、提交 commit

```bash
git commit -m "feat: 完成登录功能"
git commit -am "fix: 修复空指针"        # -a 自动暂存已跟踪文件的变更
git commit --amend -m "新的提交信息"    # 修改上一次提交
```

- `-m "msg"`：写提交说明，**信息要清晰规范**
- `-a`：跳过 `git add`，直接把已跟踪文件的变更提交，**不会包含新增文件**
- `--amend`：合并进上一次提交（改信息、补漏文件），会改写上一条记录；**已 push 远程后禁用**
- 使用场景：
  1. `-m`：常规提交
  2. `-am`：小改动省去 add 步骤
  3. `--amend`：刚提交完发现漏改/漏文件：

```bash
git add 漏掉的文件
git commit --amend --no-edit
```

> ⚠️ `--amend` 改写历史，该提交已 push 到远程时禁止使用，否则需 force push（团队严禁）。
---

## 五、查看历史 log

```bash
git log
git log --oneline
git log -n 5
git log --oneline --graph --decorate
git log --author="张三"
git log --since="2025-01-01"
git log -p                           # 每次提交的完整 diff
git log --follow -- <file>           # 文件（含重命名）的完整历史
git show <commit-id>                 # 查看某次提交改了什么
```

- `--oneline`：一行显示 commit id + 提交信息，最常用
- `-n 5`：只看最近 5 条
- `--graph`：图形化分支线；`--decorate`：显示分支/标签指向
- `--author` / `--since`：按作者、时间过滤，排查问题时很好用

### 查看某行代码是谁改的

```bash
git blame <file>
git blame -L 10,20 <file>   # 只看 10~20 行
```

- 使用场景：查"这行代码谁写的、什么时候写的、提交信息是什么"

---

## 六、分支 branch

```bash
git branch                  # 查看本地分支（* 为当前分支）
git branch -a               # 本地 + 远程所有分支
git branch dev              # 创建 dev 分支（不切换）
git branch -d dev           # 安全删除（需已合并）
git branch -D dev           # 强制删除（未合并也删，谨慎）
git branch -m old new       # 分支重命名
```

### 切换 / 创建分支

```bash
git checkout dev
git switch dev                 # git 2.23+ 推荐
git switch -c feature/login    # 创建并切换
git checkout -b feature/login  # 旧写法，等价
```

### 合并分支 merge

```bash
git switch main
git pull
git merge dev                  # 默认合并（可能快进）
git merge --no-ff dev          # 强制生成 merge commit，保留合并痕迹，团队常用
```

- 使用场景：功能分支开发完成，合并回主分支
- 遇到冲突：手动解决后 `git add` + `git commit`；或在解决前放弃合并 `git merge --abort`

---

## 七、远程仓库 remote

```bash
git remote -v
git remote add origin git@github.com:xxx/repo.git
git remote set-url origin <new_url>
git remote remove origin
git remote prune origin    # 清理本地残留的、远程已删除分支的引用
```

### 拉取远程代码

```bash
git fetch origin
git pull origin main
git pull --rebase origin main
```

- `git fetch`：只把远程更新下载到本地远程跟踪分支（origin/main），**不合并、工作区不变**
- `git pull` = `git fetch` + `git merge origin/main`，一步完成拉取并合并
- `git pull --rebase`：拉取后用 rebase 合入，历史更线性（见 [十一、变基 rebase](#十一变基-rebase)）

### 推送本地到远程

```bash
git push origin main
git push -u origin main        # 建立上游关联，之后直接 git push
git push origin dev:dev        # 本地 dev 推到远程 dev
git push origin --delete dev   # 删除远程分支
git push --tags                # 推送所有标签
```

- 使用场景：把本地提交同步到远程；多人协作**先 pull 再 push**

---

## 八、暂存现场 stash

> 场景：代码写一半不能提交，又要切分支 / 拉代码，把现场"存起来"

```bash
git stash
git stash -u                    # 连同未跟踪的新文件一起暂存
git stash save "备注：表单页未完成"
git stash list
git stash pop                   # 取出最新一条并删除
git stash apply stash@{1}       # 取出指定一条，不删除
git stash drop stash@{1}        # 删除指定一条
git stash clear                 # 清空全部
```

- `pop` vs `apply`：pop 取出即删；apply 保留可复用
- 使用场景：临时切分支修 bug、半成品不想提交；注意 pop 可能冲突，手动解决即可

---

## 九、版本回退 reset revert reflog

> 面试重点区，一定要能讲清三者的区别。

### 1. reset：回退本地历史（改写历史）

```bash
git reset --hard <commit-id>    # 工作区 + 暂存区 + 历史全回退（本地改动丢失！）
git reset --soft <commit-id>    # 只回退 commit，改动保留在暂存区
git reset --mixed <commit-id>   # 默认；回退 commit，改动回到工作区（未暂存）
git reset HEAD~1                # 撤销最近一次提交，代码保留
```

| 模式 | 历史回退 | 暂存区改动 | 工作区改动 |
| --- | --- | --- | --- |
| `--soft` | 是 | 保留 | 保留 |
| `--mixed`（默认） | 是 | 退回工作区 | 保留 |
| `--hard` | 是 | 丢失 | 丢失 |

- **已经 push 远程的 commit 不要用 reset，改用 revert**

### 2. revert：安全回退（生成反向提交，适合已推送）

```bash
git revert <commit-id>
git revert -m 1 <merge-commit-id>   # 回退 merge 提交时指定保留哪条主线
```

- revert 生成一个新提交，**历史不变**，可直接 push
- 使用场景：远程代码出问题，撤销某次提交且不破坏他人历史

### 3. reflog：找回丢失的提交（救命命令）

```bash
git reflog
git reset --hard <sha>
git branch recover <sha>      # 或把丢失的 commit 挂到新分支
```

- reflog 记录 HEAD 的所有移动（默认保留约 90 天）
- `git reset --hard` 误删的提交、被删分支最后指向的提交，都能靠它找回
---

## 十、拣选提交 cherry-pick

```bash
git cherry-pick <commit-id>
git cherry-pick A B C            # 连续拣选多个
git cherry-pick --continue       # 冲突解决后继续
git cherry-pick --abort          # 放弃本次拣选
```

- 使用场景：把某个 commit 单独搬到当前分支
- 与 merge 的区别：merge 合并整个分支，cherry-pick 只拿指定提交
- 典型场景：线上 hotfix 修复提交，同步到其他正在开发的分支

---

## 十一、变基 rebase

```bash
git rebase main          # 当前分支的提交重放到 main 最新提交之后
git rebase -i HEAD~3     # 交互式整理最近 3 个提交
git pull --rebase        # 拉取时用 rebase 代替 merge
```

- `-i` 交互模式常用操作：`pick` 保留 / `reword` 改信息 / `squash` 合并（保留信息）/ `fixup` 合并（丢弃信息）/ `edit` 停改 / `drop` 删除

```bash
# 把最近 3 个提交合并成 1 个
git rebase -i HEAD~3
# 编辑器里把第 2、3 行的 pick 改为 squash 或 fixup，保存退出
```

- 使用场景：本地整理提交历史、合并小 commit、同步主分支最新代码（push 之前）
- ⚠️ **已推送到远程的分支禁止 rebase**：改写历史会让团队其他人的仓库错乱

---

## 十二、标签 tag

```bash
git tag v1.0.0
git tag -a v1.0.0 -m "v1.0.0 正式发布"   # 带注释的标签，正式版本推荐
git tag
git tag -d v1.0.0
git push origin v1.0.0
git push origin --tags
```

- `-a`：annotated 带注释标签（含作者、日期、说明），比轻量标签信息全
- 使用场景：版本发布打 tag，配合 release、回滚
- 与 branch 的区别：branch 指针随提交移动；tag 固定指向某个提交

---

## 十三、差异查看 diff

```bash
git diff                 # 工作区 vs 暂存区（尚未 add 的改动）
git diff --cached        # 暂存区 vs HEAD（已 add 未 commit 的改动）
git diff HEAD            # 工作区 + 暂存区 vs HEAD
git diff main dev        # 两个分支之间的差异
git diff <commit1> <commit2>
```

- 使用场景：commit 前检查自己到底改了什么；code review 时对比分支

---

## 十四、忽略文件 .gitignore

```bash
# 仓库根目录创建 .gitignore，示例：
target/
*.class
.idea/
*.log
.env
```

- 规则支持 `*` 通配、`**` 目录、`!` 取反；常用模板：Node / Java / Python / macOS / Windows
- ⚠️ 已被跟踪的文件不受 .gitignore 控制，需先取消跟踪：

```bash
git rm --cached <file>          # 文件保留在磁盘，仅从 Git 移除跟踪
git commit -m "chore: 停止跟踪 xxx"
```

- 使用场景：忽略编译产物、IDE 配置、密钥、日志等不该入库的内容

---

## 十五、定位 bug git bisect

```bash
git bisect start
git bisect bad              # 当前提交有问题
git bisect good <sha>       # 某个已知正常的老提交
# 每验证一次都执行 git bisect good / git bisect bad
git bisect reset            # 结束二分
```

- 使用场景：功能"最近坏了"但不知道是哪个提交引入的
- 二分查找复杂度 O(log n)，1000 个提交最多验证 10 次（面试加分项）

---

# 第二部分 高频场景速查表

| 场景 | 推荐命令 |
| --- | --- |
| 新项目开始 | `git init` + 配置 user.name / email |
| 拉取已有项目 | `git clone <url>` |
| 日常提交一轮 | `git status` → `git add .` → `git commit -m "feat: xxx"` → `git pull --rebase` → `git push` |
| 写一半要切分支 | `git stash -u`；回来 `git stash pop` |
| 新建功能分支 | `git switch -c feature/xxx` |
| 功能分支合回 main | 切 main → `git pull` → `git merge --no-ff feature/xxx` → 解决冲突 → push |
| 放弃本地修改 | `git restore <file>`（危险，改动不可恢复） |
| add 错了文件 | `git restore --staged <file>` |
| 撤销最近一次提交但保留代码 | `git reset --soft HEAD~1` |
| 撤销已推送的提交 | `git revert <commit-id>` |
| 合并最近 n 个本地提交 | `git rebase -i HEAD~n` |
| 把某个修复带到别的分支 | `git cherry-pick <commit-id>` |
| 上线打版本 | `git tag -a v1.0.0 -m "xxx"` + `git push origin v1.0.0` |
| 找引入 bug 的提交 | `git bisect` |
| 查某行代码谁改的 | `git blame <file>` |
| 误删分支 / 丢了提交 | `git reflog` + `git reset --hard <sha>` |

## 重要警告（面试前背下来）

1. `git reset --hard`、`git rebase`、`git commit --amend` 会**改写历史**，已 push 远程的提交不要用
2. 冲突标记：`<<<<<<< HEAD` / `=======` / `>>>>>>> other`，手动修改后 add + commit
3. 提交信息规范：`feat:` 新功能、`fix:` 修复、`docs:` 文档、`refactor:` 重构、`chore:` 杂项、`test:` 测试
4. 破坏性操作前先确认：`git status`、`git diff`、`git stash`，能备份就备份
---

# 第三部分 秋招高频面试问答

> 标注说明：★★★★★ 必背高频 / ★★★★ 高频 / ★★★ 加分题
> 覆盖近两年一线大厂后端、客户端、测开岗位的 Git 考点与面经题。

## 一、基础概念类

### Q1. Git 和 SVN 的区别？（★★★★★）

**参考回答：**
- **分布式 vs 集中式**：Git 每个开发者本地都有完整仓库（含全部历史），可离线提交、离线建分支；SVN 历史集中在中央服务器，提交、查历史都要连服务器
- **分支成本**：Git 创建/切换/合并分支成本极低，鼓励大量短生命周期分支；SVN 分支是目录拷贝，合并代价高
- **安全性**：Git 所有内容按 SHA-1 校验，历史不易篡改；SVN 无此机制
- **协作生态**：Git 配合 GitHub / GitLab，支持 PR/MR、fork、Code Review 等现代流程

### Q2. 说说 Git 的四个区域和文件状态？（★★★★★）

**参考回答：** 工作区（磁盘文件）→ `git add` → 暂存区（index/stage）→ `git commit` → 本地仓库（HEAD）→ `git push` → 远程仓库。
文件状态流转：未跟踪（untracked）→ 已暂存（staged）→ 已提交（committed）→ 已修改（modified）。一句话：**改 → add → commit → push**，任何状态用 `git status` 查看。

### Q3. Git 底层对象模型？（★★★★ 大厂爱问）

**参考回答：** 四种对象，都存在 `.git/objects`：
- **blob**：文件内容快照（不存文件名）
- **tree**：目录结构，记录文件名与 blob 的映射
- **commit**：一次提交，指向一个 tree + 父提交 + 作者/时间/提交信息
- **tag**：annotated tag 指向一个 commit
- 每次 commit 保存的是**完整快照**（靠内容 hash 去重），不是增量 diff

### Q4. 什么是 HEAD？什么是游离态（detached HEAD）？（★★★★）

**参考回答：** HEAD 是指向当前分支的指针，分支再指向具体 commit。当 `git checkout <commit-id>`（而不是分支名）时进入 detached HEAD：此后的提交不属于任何分支，切走后容易"丢"。处理：想保留就 `git switch -c new-branch` 挂到新分支；不保留直接 `git switch main`。

### Q5. fork、clone、branch 有什么区别？（★★★）

**参考回答：** fork 是在 GitHub/GitLab 上把别人仓库复制到自己账号（常用于无写权限的贡献：fork → 修改 → PR）；clone 是把远程仓库完整复制到本地；branch 是仓库内的分支指针，用于隔离开发。三者层级不同：账号级 / 本地级 / 仓库级。

### Q6. Git 和 GitHub 是什么关系？（★★★ 校招高频）

**参考回答：** Git 是版本控制工具本身；GitHub 是基于 Git 的代码托管平台，额外提供 PR、Issue、Actions（CI/CD）、代码搜索等能力。面试时不要把两者混为一谈。

## 二、命令对比类

### Q7. git fetch 和 git pull 的区别？（★★★★★）

**参考回答：**
- `fetch`：只把远程提交下载到本地远程跟踪分支（origin/main），**不合并、工作区不变**
- `pull` = `fetch` + `merge`（默认），会直接合并进当前分支，工作区变化
- 追问加分：`git pull --rebase` 用 rebase 方式合入，本地提交会重放到远程提交之后，历史更线性（见 Q8）

### Q8. git merge 和 git rebase 的区别？（★★★★★）

**参考回答：**
- merge：把另一个分支合入当前分支，非快进时生成一个 merge commit，历史保留分叉，能看到并行开发痕迹；合入方分支的 commit id 不变
- rebase：把当前分支的提交**重放**到目标分支最新提交之后，重新生成 commit，历史成一条直线；被重放的提交 id 全变
- 冲突处理：merge 一次解决；rebase 可能每个被重放的提交都要解决
- 使用原则：**本地整理用 rebase，合入公共主线用 merge（--no-ff）**；已推送的公共分支禁止 rebase
- 加分：能说出 fast-forward——目标分支没有新提交时，merge 只是把指针快进，不产生 merge commit；`--no-ff` 可强制生成

### Q9. git reset 的 --soft / --mixed / --hard 区别？（★★★★★ 必背）

**参考回答：** 三个模式都移动 HEAD 指针回退提交，区别在于改动放哪、丢不丢：
- `--soft`：只回退提交，改动全部保留在**暂存区**，可直接再 commit
- `--mixed`（默认）：回退提交，改动回到**工作区**（未暂存）
- `--hard`：提交、暂存区、工作区全部回退，**本地改动丢失**
- 口诀：soft 留暂存、mixed 回工作区、hard 全清空
- 追问加分：`git reset --hard` 后能找回吗？—— 能，`git reflog` 找原 commit id，`git reset --hard <sha>` 回去（reflog 默认保留 90 天）

### Q10. git reset 和 git revert 的区别？（★★★★★）

**参考回答：** reset 移动指针、**改写历史**，适合本地未推送的提交；revert 生成**反向提交**抵消目标提交，历史向前走、**不改写历史**，适合已推送的提交。已 push 的提交只能 revert（个人分支经团队允许才 force push）。

### Q11. git log 和 git reflog 的区别？（★★★★）

**参考回答：** `git log` 展示从当前 HEAD 可到达的提交历史（正常记录）；`git reflog` 记录 HEAD 的**所有移动轨迹**，包括 reset、amend、checkout、被删分支等，是找回"丢失"提交的关键。类比：log 是相册，reflog 是行走记录仪。

### Q12. git checkout、git switch、git restore 的区别？（★★★）

**参考回答：** git 2.23+ 把 checkout 拆成两个语义清晰的命令：切分支用 `switch`，恢复文件用 `restore`（`--staged` 取消暂存、不带参数恢复工作区）。老命令 checkout 一个顶俩、语义不清，新写法更安全直观。

### Q13. git stash 和临时 commit 的区别？（★★★）

**参考回答：** stash 把改动暂存起来，可备注、可多次保存、可挑选恢复（pop/apply），不污染历史；临时 commit 也能保存现场，但会产生无意义提交，之后还得清理。补充：stash 默认不存未跟踪文件，需 `-u`；跨分支 pop 可能冲突。

### Q14. git tag 和 branch 的区别？（★★★）

**参考回答：** branch 是可移动指针，随提交前进，用于开发；tag 固定指向某个提交、不可移动，用于版本发布标识。正式版本建议用 `git tag -a` 带注释标签。
## 三、实战场景类

### Q15. 合并出现冲突怎么解决？（★★★★★）

**标准动作：**
1. `git status` 查看冲突文件（含 `<<<<<<<` `=======` `>>>>>>>` 标记）
2. 手动修改，保留需要的代码，删掉冲突标记
3. `git add <file>` 标记已解决
4. `git commit`（或 merge / cherry-pick / rebase 各自的 `--continue`）
5. 想放弃：`git merge --abort`（rebase 冲突用 `git rebase --abort`）

加分点：优先用 IDE 图形化解决工具；与同事协商保留哪部分，不要单方面覆盖。

### Q16. 提交已 push 远程，发现有 bug 怎么回退？（★★★★★）

**参考回答：** 用 `git revert <commit-id>` 生成反向提交后 push，不改写公共历史。不要 reset + force push；确需覆盖时要经过团队约定，且用 `git push --force-with-lease`（推送前校验远程状态，避免覆盖他人刚推的提交）。

### Q17. 提交信息写错 / 漏了文件怎么办？（★★★★）

**参考回答：** 只改信息：`git commit --amend -m "新信息"`；漏文件：`git add <file>` 后 `git commit --amend --no-edit`。前提：该提交未 push；push 过则只能在个人分支并经过允许后 force push。

### Q18. 代码写一半，突然要切分支修 bug？（★★★★★）

**参考回答：** `git stash -u` 保存现场 → 切分支修复提交 → 切回来 `git stash pop` 恢复。stash 建议加备注；pop 冲突就手动解决，不会丢数据。

### Q19. 误删了分支，刚写的代码全没了？（★★★★★ 面试最爱考）

**参考回答：** 用 reflog 找回：
1. `git reflog` 找到被删分支最后一次指向的 commit
2. `git branch recover <sha>` 或 `git switch -c recover <sha>` 重建
原理：分支只是指针，commit 对象在 GC 前一直存在；reflog 默认记录 90 天。

### Q20. 线上紧急 bug，标准的 Git 处理流程？（★★★★★ 真实高频）

**标准流程：**
1. 从稳定分支切 hotfix：`git switch -c hotfix/xxx main`
2. 修复并提交：`git commit -m "fix: xxx"`
3. 合回主分支：`git merge --no-ff hotfix/xxx`
4. 同步到其他分支：`git cherry-pick <hotfix commit id>`
5. 打 tag、部署、验证
加分点：主干永远可发布；不在 main 上直接改；hotfix 后补充回归用例。

### Q21. 多人协作 push 被拒绝（non-fast-forward）怎么办？（★★★★★）

**参考回答：** 原因是远程有本地没有的新提交。先 `git pull --rebase`（本地提交重放到远程之后）→ 解决冲突 → `git push`。原则：**先同步后推送**，绝不 force push 覆盖他人代码。

### Q22. 想把多个 commit 合并成一个？（★★★★）

**参考回答：** `git rebase -i HEAD~n`，把要合并的提交改成 `squash`（保留提交信息）或 `fixup`（丢弃信息），保存退出。前提：提交未 push 或在个人分支。

### Q23. 如何定位"哪个提交引入了 bug"？（★★★ 加分题）

**参考回答：** `git bisect` 二分查找：标一个 bad、一个 good，每次验证后标 good/bad，Git 自动在区间中间切换提交，O(log n) 次锁定问题提交。1000 个提交最多 10 次。配合单测脚本可全自动（`git bisect run`）。

### Q24. 怎么查看某个文件的修改历史或某行代码的归属？（★★★）

**参考回答：** 归属用 `git blame <file>`（可 `-L 10,20` 限定行）；文件完整历史用 `git log -p -- <file>`（`--follow` 跟踪重命名）；看某提交里的文件内容 `git show <commit>:<file>`。

### Q25. .gitignore 不生效怎么办？（★★★）

**参考回答：** 已跟踪的文件不受 .gitignore 控制，先 `git rm --cached <file>` 取消跟踪并提交，再写入 .gitignore 才会生效。新文件一开始没 add 的话，直接写 .gitignore 即可。

### Q26. revert 一个 merge 提交要注意什么？（★★★）

**参考回答：** merge 提交有两个父提交，需指定保留哪条主线：`git revert -m 1 <merge-commit>`（一般 -m 1 指合并后的主线）。另外 revert 合并不等于撤销内部子提交，后续重新 merge 该分支可能把已撤销的内容带回来，要谨慎并保持记录清晰。

### Q27. 为什么强调"小提交、勤提交、规范信息"？（★★★）

**参考回答：** 小提交易 review、易精准回退（revert / cherry-pick）、易 bisect 定位；规范前缀（feat/fix/docs）让历史可读，还能自动生成 changelog。这是团队工程质量的基础，也是面试官判断工程素养的点。

## 四、团队协作与工作流类

### Q28. 介绍一下 Git Flow / GitHub Flow？（★★★★★）

**参考回答：**
- **Git Flow**：常驻分支 main（可发布）+ develop（集成开发），短期分支 feature（功能）/ release（发版准备）/ hotfix（线上修复），管理严格，适合版本化发布
- **GitHub Flow**：只有 main + feature，main 始终可发布，配 PR + CI，轻量，适合持续交付的互联网团队
- 简洁主流方案：main 保护 + `feature/模块` 分支 + PR Review + 合并后自动部署
- 加分：main 分支加保护规则（禁止直接 push、必须过 CI、至少一人 approve）

### Q29. PR / MR 的流程和意义？（★★★★）

**参考回答：** 功能分支推送到远程后发起 PR/MR：指定 reviewer、关联 issue、自动跑 CI；review 通过后合入目标分支。合入方式有 merge / squash merge / rebase merge，其中 squash merge 把整个 PR 压成一个 commit，main 历史更干净（很多公司默认）。
意义：代码审查、质量门禁、变更留痕可追溯。

### Q30. Git hooks 了解吗？（★★★ 工程化加分）

**参考回答：** Git 事件触发的钩子脚本：pre-commit 跑 lint/格式化（如 husky + lint-staged）、commit-msg 校验提交信息格式（commitlint）、pre-push 跑测试。能体现工程化与自动化意识。

### Q31. Git 和 CI/CD 的关系？（★★★）

**参考回答：** CI 持续集成：每次 push / PR 自动构建、单测、静态检查（GitHub Actions / GitLab CI / Jenkins）；CD 持续部署：tag 或 main 变更触发发布。Git 的分支、tag、hooks 为 CI/CD 提供触发点和版本依据，比如"只有打 v 开头的 tag 才发布生产"。

## 五、真实面经题（近两年大厂秋招，换皮率极高）

### 面经 1：线上紧急 bug，你会怎么用 Git 操作？（后端一面）

参考答法：见 Q20 hotfix 标准流程。面试官通常会追问 cherry-pick 和 revert 细节，把这两个命令的用法和区别讲清楚。

### 面经 2：和同事冲突怎么解决的？两边都改坏怎么办？（客户端一面）

参考答法：`git status` 看冲突文件 → 沟通保留方案 → 手动/IDE 解决 → add + commit。若双方改坏：用 `git log -p`、`git blame` 还原上下文，必要时 `git checkout --ours/--theirs` 选择版本重做，或 revert 掉有问题的一方提交。

### 面经 3：reset 和 revert 的区别？你们团队平时用哪个？（测开一面）

参考答法：见 Q10。补充：团队线上统一 revert；本地个人分支偶尔 reset + `--force-with-lease`；禁止随意 force push。

### 面经 4：git pull 卡在合并冲突，环境重启了，分支状态混乱，怎么办？（后端一面）

参考答法：`git status` 确认状态 → 不想合并就 `git merge --abort` 回到拉取前 → 重新 `git pull --rebase`；rebase 冲突同理 `git rebase --abort`。核心：**任何合并/变基冲突都可以 abort 重置**，不要硬来。

### 面经 5：你和小张改同一个文件，你先 push 成功，他 push 被拒，怎么处理？（后端二面）

参考答法：小张 `git pull --rebase` → 解决冲突 → 再 push。引申：团队约定"先拉后推"、禁止 force push 覆盖、用 `--force-with-lease` 兜底。

### 面经 6：怎么回退到昨天上线前的版本？（客户端二面）

参考答法：找 release tag（如 v1.0.0）→ `git checkout v1.0.0` 验证 → 确认后在发布分支 `git revert` 之后的内容（若是 merge 提交用 `git revert -m 1`）→ 打新 tag 发布。体现"可回滚、有预案"的工程思维。

### 面经 7：分支很乱、提交信息全是 fix fix，怎么整理？（后端一面）

参考答法：未合并的个人分支用 `git rebase -i` 合并/改名；已进 main 的不改历史，靠 commitlint + commit-msg hook 约束新提交；团队层面用 squash merge 保证主分支干净。

### 面经 8：Git 管理模型/训练数据够用吗？（AI/算法岗）

参考答法（加分）：Git 适合代码，不适合大文件（数据集、权重），会让仓库膨胀变慢；可用 Git LFS（Large File Storage）或 DVC（Data Version Control）管理数据和实验版本。

## 附：秋招前必背清单（30 秒自检）

- [ ] 能说清四个区域和 add / commit / push 全流程
- [ ] fetch vs pull、merge vs rebase、reset vs revert 三组对比脱口而出
- [ ] 能讲 reset 三模式（soft / mixed / hard）
- [ ] 能讲冲突解决标准流程
- [ ] 会用 reflog 找回误删分支 / 丢失的 commit
- [ ] 会讲 hotfix + cherry-pick 线上修复流程
- [ ] 能说清 .gitignore、git blame、git bisect 的用途
- [ ] 能讲清 Git Flow 或你所在团队的分支模型与 PR 流程