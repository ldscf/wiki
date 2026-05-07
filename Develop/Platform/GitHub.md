---
source_title: GitHub
categories:
- Develop
- Platform
last_modified: '2026-05-07T01:46:16Z'
---
### Overview

#### GitHub Person
- https://github.com/ldscfe
- https://github.com/ldscf

#### 价格计划(免费版)
- Free
  - The basics for individuals and organizations
- Unlimited public/private repositories
  - Host open source projects in public GitHub repositories, accessible via web or command line. Public repositories are accessible to anyone at GitHub.com.
- 2,000 automation minutes/month
  - Free for public repositories
  - Use execution minutes with GitHub Actions to automate your software development workflows. Write tasks and combine them to build, test, and deploy any code project on GitHub.
- 500MB of Packages storage
  - Free for public repositories
  - Host your own software packages or use them as dependencies in other projects. Both private and public hosting available.
- New Issues & Projects (beta)
  - Community support
  - 免费(个人)版支持无限个数的私有(或公有)仓库, 每月2000分钟的自动构建时长, 单仓库最大 500M.
  - 查看详情: https://github.com/pricing
  - CI/CD，全称：持续集成 (Continuous Integration) ，持续部署 (Continuous Deployment) ，是开发流程的自动化利器

### Git
1. 当前机器 .ssh 中有 id_rsa 或指定私钥文件
1. 远程 github 中已加载公钥 (settings -> Access -> SSH & GPG keys -> New SSH key -> Authentication keys)

#### 指定私钥文件

**全局变量**
```
 git config --global core.sshCommand "ssh -i ~/.ssh/id_rsa_mc3"
 # 正常 git 命令
```

**临时环境变量**
```
 GIT_SSH_COMMAND="ssh -i ~/.ssh/id_rsa_mc3" git clone git@github.com:ldscfe/DocAI.git
```
```
 # 修改本地仓库配置（克隆后），将配置写入该仓库的 .git/config 文件中
 git config core.sshCommand "ssh -i ~/.ssh/id_rsa_mc3 -F /dev/null"
```

**配置别名**
 ```

# .ssh/config
github-ldscf                # 别名
    HostName github.com     # 服务器地址
    User ldscf              # 用户名
    IdentityFile ~/.ssh/id_rsa_mc3
    IdentitiesOnly yes      # 强制只使用指定的密钥，防止因尝试过多密钥被服务器拒绝
git clone git@github-ldscf:ldscfe/DocAI.git
```

#### 全局设置
```
 git config --global user.name ldscfe
 git config --global user.email ldscfe@gmail.com
```
 
```
 # git status等命令自动着色
 git config --global color.ui true
 # 自动判断提交位置（clone 多个库）
 git config --global push.default matching
```
 
```
 # 设置 SOCKS5 代理
 git config --global https.proxy "socks5://127.0.0.1:1080"
 # 取消
 # git config --global --unset https.proxy
```
```
 # 配置信息（本地）: git config --list
 # 仓库地址（远程）: git remote -v
 # 所在分支（本地）: git branch -vv
```

#### Init
```
 git init
```

在当前目录下创建一个新的 Git 仓库，该目录下的所有文件都处于 Untracked（未跟踪） 状态。

#### Clone
```
 git clone git@github.com:ldscfe/snippets.git
```

创建 snippets 目录，将远程仓库 snippets 的代码克隆到本地。

#### fork
```
 https://api.github.com/repos/ldscfe/项目名称/forks
```

查看项目被 fork 情况

### Repository

#### 查询

| cmd | 说明 |
|:---|:---|
| git status | 工作区和暂存区相对于最后一次提交的状态 |
| git log -1 | 查看最近一次提交的作者、日期和注释 |
| git log -n 1 --pretty=format:"%h - %an, %ar : %s" origin/main | 获取最近一次提交的摘要（哈希 - 作者, 时间 : 说明） |
| git show HEAD~1 --stat | 只查看倒数第二次提交的统计数据 |
| git fetch origin | 从远程仓库获取最新的元数据 |
| git diff --name-only HEAD origin/main | 比较差异，只列出文件名 |
| git diff HEAD -- <文件路径> | 相比于上一次提交（HEAD）的所有修改 |

#### 同步

| cmd | 说明 |
|:---|:---|
| + |
| git pull | 总是尝试合并到当前所在的分支 |
| git pull origin main | 拉取并合并远程代码 |

#### 增加
```
 git status           # 查看文件状态
```
 
```
 git add $FN
 git add .            # 所有
```

#### 提交
```
 # 需要 git add
 git commit -m "comment content"
 git push
```
```
 # 冲突
 git pull --rebase origin main
 git push origin main     # 如果有冲突，Git 会停下来等待处理
 git rebase --continue    # 继续流程，如果还有下一个冲突，会再次停下来
```
 
 ```
<<<<<<< HEAD
  let cache_size = 1024; 
  =======
  let cache_size = 2048; 
  >>>>>>> 7b3a1d2
  
  其中：以 ======= 为分隔，上面是“远程”代码，下面是“本地”修改。
```
```
 # 冲突文件会变成：（用工具修改会非常方便）
```

#### Deploy Key

在 GitHub 中，Deploy Key（部署密钥） 通常是针对单个仓库的，不允许同一个 Deploy Key 用于多个仓库。

访问账号下的所有私有仓库：
```
 在 Settings -> SSH and GPG keys 中增加公钥。
```

#### 数字签名

在 GitHub 的 PR 页面中，提交记录旁边的绿色 Verified 标志代表这个提交（Commit）拥有一个有效的数字签名（通常是 GPG、SSH 或 S/MIME 签名）。Git 默认并不要求对提交进行数字签名。

**GitHub**
1. 点击右上角个人信息 -> Settings -> (Access) SSH and GPG keys，点击 New SSH key（不要去点下方的 New GPG keys）
1. Key type: 必须选择 "Signing Key"（默认是 Authentication Key，负责登录）
1. Key: 粘贴 cat ~/.ssh/id_rsa_mc3.pub 内容

**本机**
```
 # 使用 SSH 模式签名
 git config --global gpg.format ssh
```
 
```
 # 设置公钥路径（.ssh 目录下有私钥（id_rsa），或已加载 ssh-add ~/.ssh/id_rsa_mc3）
 git config --global user.signingkey ~/.ssh/id_rsa_mc3.pub
 # 设置公钥文件，一样可以：读取私钥 -> 推导出公钥 -> 签名
 # 问题：私钥在 ssh-agent、硬件 token，或者多人共用、CI/CD情况下，直接使用私钥文件不安全
```
 
```
 # 开启数字签名（默认关闭）
 git config --global commit.gpgsign true
```
```
 # 有可能需要设置帐号的正确信息
 git config --global user.name ldscfe
 git config --global user.email ldscfe@gmail.com
```
```
 # 创建一个空提交并签名
 git commit --allow-empty -S -m "test: verify ssh commit signing"
```

#### 去除文件&目录

.gitignore
```
 .DS_Store
 .idea
 target/
 log/
 src/test/
```
 
```
 # 已 git add 的文件，在 .gitignore 中标识无效
 # 删除远程文件/目录
 git rm -r --cached 目录名/
 git commit -m "删除远程多余的目录/文件"
 git push
```

#### 分支

##### 分支管理

切换分支
```
 git switch             # 切换到已有分支（如 main）
 git switch -                        # 快速返回上一个分支（极其常用）
```

创建并切换
```
 git switch -c      # [常用] 创建新分支并立即切换过去
```

##### 文件恢复

丢弃工作区修改 (!!!)
```
 git restore                   # 将文件恢复至最后一次提交状态，清空未提交改动
 git restore .                       # 恢复当前目录下所有改动 !!!
```

取消暂存
```
 git restore --staged          # 将文件从暂存区(Index)移回工作区，不丢弃内容变化
```

##### 推送策略
```
 git config --global push.default simple    # 只推送当前分支到对应的远程分支，Git 2.0+ 默认值
```

#### 冲突

冲突 = 同一文件的同一位置，在不同分支被修改。可以选择 Rebase（变基）或 Merge（合并）。

**Rebase**: 保持提交历史呈线性，更整洁。把本地的提交先“临时搁置”，将远程的新提交拉取下来，然后再把本地的提交挨个追加到最前面。
1. 执行变基拉取: git pull --rebase  

有冲突: Git 会在冲突的地方停下。需要手动解决冲突文件，保存后运行：  

> git add .  

> git rebase --continue  

放弃Rebase: git rebase --abort
1. 推送: git push origin main

**解决冲突其实是三方合并：**

| 名称 | 含义 |
|:---|:---|
| BASE | 共同祖先 |
| LOCAL (HEAD) | 你当前分支 |
| REMOTE | 要合并进来的分支 |
**Git 会在文件里插入标记：**
 ```
<<<<<<< HEAD
你的代码（本地）

###### 
远程代码
>>>>>>> origin/main
```

**更安全地处理冲突 - rebase**

备份本地修改 -> 从远程更新 -> 恢复本地备份
```
 git stash push -m "xxx"
 git pull --rebase
 git stash apply
 git push
```

因为 apply 不会自动删除记录，时间久了 git stash list 会堆积很多垃圾。
```
 git stash drop      # 删除最近的一条
 git stash clear     # 清空
```

#### 命令禁用

例如：禁用 push
- 状态: git remote -v
- 禁用: git remote set-url --push origin DISABLE
- 恢复: git config --unset remote.origin.pushurl

#### 验证 PR

在本地创建一个隔离的环境来运行代码。本地代码先与远程主线拉齐。
 ```
git checkout main
git pull origin main
```
1. 获取远程更改，在本地创建临时测试分支  

LOCAL_BRANCH=test-branch  

git fetch origin pull/1/head:${LOCAL_BRANCH}
1. 切换到临时测试分支  

git checkout ${LOCAL_BRANCH}
1. 回到主线  

git checkout main
1. 删掉临时分支  

git branch -D ${LOCAL_BRANCH}

### Hooks

#### Webhooks

Webhook 可让外部服务在特定事件发生时获得通知。发生指定事件时，我们将向您提供的每个 URL 发送 POST 请求。

当 GitHub 收到提交时（也可以是其他的事件）HTTP POST 请求到指定地址，如：https://XXX/webhook:
1. 请求体（通常是 JSON）
1. 请求头 X-Hub-Signature-256
1. 签名验证
1. 解析 JSON 数据
1. 执行指令（通常是版本更新/重启）
1. 通常 通常 20 秒内返回 200

See also: [webhook Example - MWWiki](https://github.com/ldscfe/snippets/blob/main/python/webhook.py)

#### pre-commit

提交前进行验证，可启用仓库内置 Git hook：
```
 # 当然如果只是本地使用，也可以使用 git 默认的路径：.git/hooks/
 git config core.hooksPath .githooks
```
```
 # .githooks/pre-commit 主要内容
 if python3 tools/check_doc_links.py --summary; then
```

   exit 0
```
 fi
```

[check_doc_links.py](https://github.com/ldscfe/snippets/tree/main/python/check_doc_links.py) 为 Markdown 引用有效性检查工具。

### Example

#### Init sample

全局设置: 使用 id_rsa_mc3 公私钥

**github**
1. 登录公钥 (settings -> Access -> SSH & GPG keys -> New SSH key -> Authentication keys)
1. 签名公钥 (Settings -> Access -> SSH & GPG keys -> New SSH key -> Signing Key)

**Local**
 ```
git config --global user.name ldscfe
git config --global user.email ldscfe@gmail.com
git config --global color.ui true
git config --global push.default matching
git config --global core.sshCommand "ssh -i ~/.ssh/id_rsa_mc3"

# 使用 SSH 模式签名
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_rsa_mc3.pub
git config --global commit.gpgsign true

# 443 端口: GitHub 提供了一个备用端口，以备 22 端口不通畅的环境使用。

# .ssh/config
Host github.com
    Hostname ssh.github.com
    Port 443
    User git
```

#### 提交脚本

提交前展示提交内容，Y/y 确认后提交。
 ```
#!/bin/bash
 
 echo "--- Current Git Status ---"
 git status
 echo "--------------------------"
 
 read -p "Confirm commit? (y/n): " confirm
 
 if [ "$confirm" = "y" ] || [ "$confirm" = "Y" ]; then
     DT=$(date '+%Y/%m/%d %H:%M:%S')
     git add .
     git commit -m "User-defined class libraries, $DT"
     git push
     echo "Process completed successfully!"
 else
     echo "Operation cancelled by user."
 fi
```

#### 放弃本地未暂存的修改
```
 # 撤销本地修改，还未 git add
 git restore filename.ext
 # 全部已跟踪 (Tracked) 的文件：git restore :/
 # 全部未跟踪 (Untracked) 的文件：git clean -fd
```

#### 撤销 git add
```
 # 已经 git add，但尚未 git push
 git restore --staged filename.ext
```

#### 文件回退到上一次提交
```
 # 已经 git push，在本地恢复到本次提交的上一次提交
 git restore --source HEAD~1 filename.ext
```

#### 当前分支回退
```
 # 此操作是毁灭性的，丢弃最后一次提交，并丢弃本地的更改。Git 历史记录会减少一个。
 git reset --hard HEAD~1
```

#### 强制推送
```
 git push -f origin <你的分支名>
```

#### 常见提交类型
- feat：新功能
- fix：修补 Bug
- perf：性能优化
- refactor：重构
- chore：不属于以上情况时，通常归类为 chore

#### 提交多行文本
```
 git commit -m "$(cat <<'EOF'
 ...
 EOF
 )"
```

### collaborator

在 GitHub 仓库设置为 private 时，想要让其他人加入到这个私人项目中，可以添加合作者(collaborator)。
```
 Settings -> Access -> collaborators(左上侧)
```

合作者可以在右上角看到邀请提示，同意即可。

### Error

#### RSA 证书无或错误
```
 ERROR: Permission to XXX/XXX.git denied to deploy key
 fatal: Could not read from remote repository.
 -.OR.-
 git@github.com: Permission denied (publickey).
 fatal: Could not read from remote repository.
```
1. repository 下 settings/Deploy keys 中有相应 RSA publickey
1. 客户端中 .ssh 中有相应的 id_rsa(MacOS 使用 ssh-add)
1. 不同的 repository 使用不同的 RSA publickey

#### 分支冲突，本地版本陈旧
```
 Automatic merge failed; fix conflicts and then commit the result.
```

丢弃本地分支内容
```
 git reset --hard origin/master
```

#### 首次推送
```
 clone 后，首次 git push 出现:
 No refs in common and none specified; doing nothing.
 Perhaps you should specify a branch.
```

未指定推送到哪个分支，一般发生在首次推送。
```
 git push origin master
```

#### git init
```
 fatal: Not a git repository (or any of the parent directories): .git
```

git remote add 时出现，原因：未 git init

#### repository 不存在或无权限
```
 git pull 时出现:
 ssh_exchange_identification: Connection closed by remote host
 fatal: Could not read from remote repository.
```

Please make sure you have the correct access rights and the repository exists.
