---
source_title: 多用户RUST开发
categories:
- Develop
- Rust
last_modified: '2026-05-04T12:19:38Z'
---
多个用户均使用同一个 GitHub 账号、Rust 环境及项目。

### Root 执行一次

#### Project
* **设置项目根目录所属组**
```
 chgrp -R bi /u01/github/SRDS
```
* **赋予 2775 权限** (SGID 确保新文件继承 bi 组)
```
 sudo chmod -R 2775 /u01/github/SRDS
```
* **配置 ACL 默认权限**：让未来产生的所有文件（含 target 内的编译产物）自动对组开放 rwx
```
 setfacl -R -m g:bi:rwx /u01/github/
 setfacl -R -d -m g:bi:rwx /u01/github/
```
* **ACL 权限冲突**
```
 git config core.filemode false           # 仅对当前库有效
```

### Root 每用户一次

#### SSH 身份克隆
```
 U=ldscf1
 cp -rp /home/bi/.ssh /home/${U}/
 chown ${U}:${U} -R /home/${U}/.ssh
```

#### Rust 环境与用户组
* **将用户加入 bi 组**
```
 usermod -aG bi ${U}
```
* **注入环境变量到 .bashrc**
```
 # /home/${U}/.bashrc
```
 
```
 # --- Rust Environment Sharing ---
 # 指向 bi 用户已经安装好的路径
 export RUSTUP_HOME=/home/bi/.rustup
 export CARGO_HOME=/home/bi/.cargo
```
 
```
 # 将 Cargo 二进制路径加入 PATH
 export PATH="$CARGO_HOME/bin:$PATH"
```
 
```
 # --- Collaboration Settings ---
 # 确保该用户创建的文件，组内成员默认可写
 umask 002
```
 
```
 # --- share target ---
 # 使用相同依赖库目录
 export CARGO_TARGET_DIR=/u01/github/rust_target
```

### 每用户一次

#### Git 全局配置
```
 git config --global user.name ldscfe
 git config --global user.email ldscfe@gmail.com
 git config --global color.ui true
 git config --global push.default matching
 git config --global core.sshCommand "ssh -i ~/.ssh/id_rsa_mc3"
```
 
```
 # 使用 SSH 模式签名
 git config --global gpg.format ssh
 git config --global user.signingkey ~/.ssh/id_rsa_mc3.pub
 git config --global commit.gpgsign true
```

### 验证配置

以用户身份登录，执行以下检查：

| 测试步骤 | 执行命令 | 预期结果 |
|:---|:---|:---|
| 权限检查 | ls -ld /u01/github/SRDS/srds/target | 输出应包含 + 号（表示 ACL 生效），组为 bi |
| 版本检查 | rustc --version | 应能正确显示 bi 安装的 Rust 版本 |
| 编译测试 | cd /u01/github/SRDS/srds && cargo check | 能够读取 bi 的缓存，不报错且不重新下载依赖 |
