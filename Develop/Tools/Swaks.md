---
source_title: Swaks
categories:
- Doc
- Tools
last_modified: '2025-08-04T02:44:02Z'
---
swaks 是一个功能强大的命令行工具，用于测试和与 SMTP 服务器交互。它的全名是 "Swiss Army Knife smtp"，意为“SMTP的瑞士军刀”，形象地说明了它在 SMTP 测试方面的多功能性。

swaks 几乎可以测试 SMTP 协议的每一个方面，这对于系统管理员、网络工程师和开发者来说非常有用。其核心功能包括：
- 测试 SMTP 服务器连通性： 检查服务器是否正常运行，端口是否开放。
- 验证邮件传输： 模拟发送电子邮件，验证邮件是否成功送达。
- 调试 SMTP 协议： 它会显示详细的 SMTP 协议会话日志，让你看到客户端和服务器之间的每一条命令和响应，这对于诊断问题（如认证失败、连接中断）至关重要。
- 支持多种身份验证： 能够测试不同的 SMTP 认证机制（如 PLAIN、LOGIN、CRAM-MD5 等），确保认证配置正确。
- 支持 TLS/SSL： 可以测试加密的 SMTP 连接（STARTTLS 或 SMTPS），验证证书是否有效。
- 测试垃圾邮件过滤器： 开发者可以用它来模拟发送特定类型的邮件，以测试垃圾邮件或内容过滤器的工作效果。

### 示例
 ```
swaks \
  --to ldscfe@gmail.com           \
  --from info@mwwiki.eu.org       \
  --server smtp.smtpserver.com    \
  --port 587                      \
  --auth                          \
  --auth-user ldscf_170081967562  \
  --auth-password ${PASSWD}       \
  --tls                           \
  --data ./html-email.html
```
 
```
 # --tls: 启用 STARTTLS。如果服务器不支持，它会回退到非加密连接。
```
