---
source_title: Oracle Cloud
categories:
- Doc
- Help
last_modified: '2025-12-29T05:39:20Z'
---
[Oracle Cloud](https://www.oracle.com/cloud/sign-in.html) 是首款从零开始打造的公有云技术解决方案，旨在成为更适合每种应用的云技术。我们针对云计算的核心工程设计和系统设计进行了再思考，利用创新为客户解决了现有公有云技术的问题。我们加快了现有企业工作负载迁移，为所有应用提供更高的可靠性和性能，并为客户提供构建创新型云技术应用所需的全面服务。客户选择 Oracle Cloud Infrastructure (OCI) 来处理所有云端工作负载。

### Always Free

#### AMD Compute Instance
```
 AMD based Compute VMs with 1/8 OCPU and 1 GB memory each
```
 
```
 Always Free
 2 AMD based compute VMs
```

#### Arm Compute Instance
```
 Arm-based Ampere A1 cores and 24 GB of memory usable as 1 VM or up to 4 VMs 
```
 
```
 Always Free
 3,000 OCPU hours and 18,000 GB hours per month
```

### Tutorial

#### MFA

甲骨文为了进一步改善租户的安全状况，已于 2023.07.27 UTC 00:00 强制开启 MFA(Multifactor Authentication) 验证。

一般设置一部以上的 MFA 设备，以及一个以上绕过码。

登录时，点击“显示备用登录方法”可在所有验证方式中选择。

##### 增加 MFA 设备

右上角头像(Profile) - 使用者設定值(My profile, 我的概要信息) - 安全(Security)TAB
1. 帳戶復原選項: 复原邮箱
1. 两步验证(2-step verification) - 增加移动应用程序(Add mobile app) 
1. 使用，Oracle Mobile Authenticator，或：“脱机模式或使用其他验证程序”，如：Microsoft Authenticator
1. 扫描 QR，输入手机端生成的验证码（6位数字）

P.S. 若禁用“二次验证”，则下次登录时会强制打开，并可通过扫描 QR 重置验证设备。

##### 绕过码(Bypass codes)

“两步验证”的下面是"绕过码"生成及删除，每个"绕过码"使用一次

##### 识别与安全

识别与安全 -> 身分識別 -> 網域（区间选择：bj5i5j根） -> OracleIdentityCloudService -> 使用者管理
```
 # 其他
 识别与安全 -> 使用者(右下)，可以设定登录人员恢复邮箱、重设密码、建立永久绕过码等。（2024）
 身分識別 -> 網域 -> OracleIdentityCloudService 網域 -> 使用者（2022）
```

#### Cloud Shell

若出现 22 端口未启动情况，可以经由云管理平台登入：
 ```
運算 -> 執行處理 -> (mc3) 執行處理詳細資訊 -> 作業系統管理 -> 主控台連線
 -> 啟動 Cloud Shell 連線
输入用户名密码登录
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
bi       ttyAMA0  -                10:10    5.00s  0.09s  0.02s -bash
```

#### 操作
- 预留公网IP: （左上角菜单）网络 -> IP管理 -> 预留的公共IP
- 变更公网IP: （左上角菜单）计算 -> 实例 -> mc1 -> 网络 -> 附加的 VNIC -> mc1 -> IP管理 -> IPv4 地址 -> （行右...编辑）
