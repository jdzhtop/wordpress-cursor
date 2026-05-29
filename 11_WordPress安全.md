## 第11章：WordPress 安全

### 基础安全设置

**1. 保持 WordPress 更新**
```
仪表盘 → 更新 → 更新 WordPress
```
**原理：** 每个 WordPress 更新都包含安全补丁，及时更新可防止已知漏洞被利用。WordPress 7.0 包含多项安全增强，建议开启自动更新。

**2. 更新主题和插件**
```
仪表盘 → 更新 → 更新主题/插件
```
**原理：** 主题和插件也可能存在安全漏洞，应及时更新。建议：
- 开启自动更新（适用于小版本更新）
- 大版本更新前先在测试环境验证
- 删除未使用的主题和插件

**3. 强密码策略**
```
用户 → 个人资料 → 账户管理 → 生成密码
```
**建议：**
- 长度至少 12 个字符
- 包含大小写字母、数字和特殊字符
- 使用密码管理器（如 Bitwarden、1Password）
- 不要在不同网站使用相同密码

### 文件权限设置

**推荐权限：**
```
目录：755
文件：644
wp-config.php：600 或 440
```

**Linux 环境设置命令：**
```bash
find /path/to/wordpress -type d -exec chmod 755 {} \;
find /path/to/wordpress -type f -exec chmod 644 {} \;
chmod 600 wp-config.php
```

**Windows 环境设置（使用 IIS 或 Laragon）：**
```
1. 右键 WordPress 安装目录 → 属性 → 安全
2. 确保 IUSR 和 IIS_IUSRS 有读取权限
3. wp-content 目录需要写入权限
4. wp-config.php 设置为只读
```

**原理：** 正确的文件权限可防止未授权访问和文件篡改。`wp-config.php` 包含数据库凭证，应严格限制访问。

### 安全插件配置

**Wordfence Security 设置：**

**步骤 1：安装并启用**
```
插件 → 安装插件 → 搜索 "Wordfence Security"
安装 → 启用
```

**步骤 2：配置防火墙**
```
Wordfence → 防火墙 → 启用 Web Application Firewall
优化防火墙 → 选择 "PHP 模块" 模式（性能最佳）
```

**步骤 3：配置扫描计划**
```
Wordfence → 选项 → 计划扫描
- 启用每日自动扫描
- 扫描核心文件、主题和插件
- 检查已知漏洞
- 发送扫描报告到邮箱
```

**步骤 4：配置登录安全**
```
Wordfence → 登录安全 → 配置
- 启用暴力破解保护
- 设置登录尝试限制（建议 5 次/5分钟）
- 锁定后通知管理员
- 启用 reCAPTCHA 或 hCaptcha
```

### 禁用 XML-RPC

**原理：** XML-RPC 是 WordPress 的远程过程调用接口，可被用于暴力破解攻击和 DDoS 攻击。如不需要远程发布功能（如通过手机 App 发布文章），建议禁用。

**方法 1：使用插件（推荐）**
```
插件 → 安装插件 → 搜索 "Disable XML-RPC"
安装 → 启用（无需配置，自动禁用）
```

**方法 2：通过 functions.php**
```php
// 完全禁用 XML-RPC
add_filter('xmlrpc_enabled', '__return_false');
```

**方法 3：通过 .htaccess（Apache）**
```apache
<Files xmlrpc.php>
    Order Deny,Allow
    Deny from all
</Files>
```

**方法 4：通过 nginx.conf（Nginx）**
```nginx
location = /xmlrpc.php {
    deny all;
    return 403;
}
```

### 登录安全

**1. 修改默认登录 URL**
```
插件 → 安装插件 → 搜索 "WPS Hide Login"
安装 → 启用
设置 → WPS Hide Login → 自定义登录 URL（如 /my-login）
```
**原理：** 攻击者通常尝试访问 `/wp-admin` 或 `/wp-login.php`，修改登录 URL 可减少暴力破解攻击。

**2. 启用双因素认证（2FA）**
```
插件 → 安装插件 → 搜索 "Google Authenticator" 或 "Wordfence Login Security"
安装 → 启用
用户 → 个人资料 → 配置双因素认证
使用手机 App（Google Authenticator、Authy）扫描二维码
```

**3. 限制登录尝试次数**
```
插件 → 安装插件 → 搜索 "Limit Login Attempts Reloaded"
安装 → 启用
设置 → Limit Login Attempts → 配置
- 最大尝试次数：5 次
- 锁定时间：15 分钟
- 启用通知邮件
```

**4. 限制后台访问 IP（可选）**
```
在 .htaccess 添加（Apache）：
<Files wp-login.php>
    Order Deny,Allow
    Deny from all
    Allow from 123.123.123.123  # 替换为你的 IP
</Files>
```

### 安全头配置

**原理：** HTTP 安全头可增强网站安全性，防止常见攻击（如 XSS、点击劫持等）。

**在 .htaccess 添加（Apache）：**
```apache
# 防止点击劫持
Header always set X-Frame-Options "SAMEORIGIN"

# 防止 MIME 类型嗅探
Header always set X-Content-Type-Options "nosniff"

# 启用 XSS 保护
Header always set X-XSS-Protection "1; mode=block"

# 严格传输安全（HTTPS）
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

# 内容安全策略（CSP）
Header always set Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' data:;"

# 引用者策略
Header always set Referrer-Policy "strict-origin-when-cross-origin"

# 权限策略
Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()"
```

**在 nginx.conf 添加（Nginx）：**
```nginx
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' data:;" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Permissions-Policy "geolocation=(), microphone=(), camera=()" always;
```

### 备份策略

**原理：** 定期备份是最后一道防线，可在网站被攻击或数据丢失时快速恢复。

**UpdraftPlus 配置：**

**步骤 1：安装并启用**
```
插件 → 安装插件 → 搜索 "UpdraftPlus"
安装 → 启用
```

**步骤 2：配置备份计划**
```
设置 → UpdraftPlus Backups → 设置
- 文件备份计划：每周
- 数据库备份计划：每日
- 保留备份数量：4 份文件 + 10 份数据库
```

**步骤 3：配置远程存储**
```
选择存储位置：
- Google Drive（推荐）
- Dropbox
- Amazon S3
- 阿里云 OSS（国内用户推荐）

按照提示完成授权配置
```

**步骤 4：测试备份恢复**
```
1. 执行一次手动备份
2. 在测试环境尝试恢复
3. 验证网站功能正常
```

### 安全审计

**定期执行安全审计：**

**每周检查：**
- [ ] 查看 Wordfence 扫描报告
- [ ] 检查登录日志（是否有异常 IP）
- [ ] 验证备份是否成功执行

**每月检查：**
- [ ] 审查用户账户列表（删除不需要的账户）
- [ ] 检查文件权限是否正确
- [ ] 更新所有主题和插件
- [ ] 测试备份恢复流程

**每季度检查：**
- [ ] 执行完整安全扫描
- [ ] 审查安全头配置
- [ ] 更新密码（管理员和数据库）
- [ ] 检查 SSL 证书有效期

### 安全检查清单

- [ ] WordPress 版本更新到最新
- [ ] 主题/插件更新到最新
- [ ] 强密码策略（12+字符，使用密码管理器）
- [ ] 文件权限正确（目录755，文件644，wp-config.php 600）
- [ ] 安全插件启用（Wordfence/Defender）
- [ ] XML-RPC 禁用（如不需要）
- [ ] 登录安全设置（双因素认证、登录限制、修改登录URL）
- [ ] 定期备份（自动备份已配置，远程存储已设置）
- [ ] 管理员用户名非 admin
- [ ] 后台访问IP限制（可选）
- [ ] SSL证书启用
- [ ] 安全头配置（X-Frame-Options、CSP、HSTS等）
- [ ] 安全审计定期执行

---

**上一章：[第10章 性能优化](./10_性能优化.md)**

**下一章：[第12章 高级主题开发](./12_高级主题开发.md)**
