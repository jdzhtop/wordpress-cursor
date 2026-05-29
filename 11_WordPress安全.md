## 第十一章：WordPress 安全

### 基础安全设置

**1. 更新 WordPress**
```
仪表盘 → 更新 → 更新 WordPress
```

**2. 更新主题和插件**
```
仪表盘 → 更新 → 更新主题/插件
```

**3. 强密码策略**
```
设置 → 常规 → 密码强度指示器
推荐：12+ 字符，包含大小写、数字、特殊字符
```

### 文件权限设置

**推荐权限：**
```
目录：755
文件：644
wp-config.php：600
```

**设置命令：**
```bash
find /path/to/wordpress -type d -exec chmod 755 {} \;
find /path/to/wordpress -type f -exec chmod 644 {} \;
chmod 600 wp-config.php
```

### 安全插件配置

**Wordfence Security 设置：**

1. **启用防火墙**
```
Wordfence → 防火墙 → 启用 Web Application Firewall
```

2. **配置规则**
```
- 启用暴力破解保护
- 设置登录尝试限制
- 启用恶意软件扫描
```

3. **扫描计划**
```
设置 → 每周自动扫描
```

### 禁用 XML-RPC

**方法1：使用插件**
```
安装 Disable XML-RPC 插件
```

**方法2：手动禁用**
```php
// functions.php 添加
add_filter('xmlrpc_enabled', '__return_false');
```

**方法3：.htaccess**
```
<Files xmlrpc.php>
    Order Deny,Allow
    Deny from all
</Files>
```

### 登录安全

**1. 限制登录尝试**
```
使用 Limit Login Attempts Reloaded 插件
```

**2. 更改登录 URL**
```
使用 WPS Hide Login 插件
设置 → WPS Hide Login → 自定义登录 URL
```

**3. 启用双因素认证**
```
使用 Google Authenticator 插件
```

### 备份策略

**UpdraftPlus 配置：**

1. **备份频率**
```
设置 → UpdraftPlus → 备份计划
- 每日备份
- 每周完整备份
```

2. **备份位置**
```
- Google Drive
- Dropbox
- FTP
```

3. **备份内容**
```
- 数据库
- 文件
- 主题/插件
```

### WordPress 安全预防措施与最佳实践

**定期备份**
```
安装新主题或插件前，务必备份网站数据和数据库。
推荐使用 UpdraftPlus 或 VaultPress 插件进行自动备份。
备份频率建议：
- 开发环境：每日自动备份
- 生产环境：每日增量备份，每周完整备份
```

**测试环境**
```
在本地或测试服务器上先测试新功能，确认无误后再部署到生产环境。
推荐工具：
- Local by Flywheel（本地开发）
- WP Staging（创建测试站点）
```

**记录信息**
```
保存好以下重要配置信息：
- 数据库连接信息（wp-config.php）
- FTP/SFTP 账号密码
- 域名注册商账号
- 主机服务商账号
- SSL证书信息
```

**保持更新**
```
定期更新 WordPress 核心、主题和插件到最新版本。
- 仪表盘 → 更新 → 一键更新
- 开启自动更新通知
- 订阅安全公告邮件
```

**安全审计**
```
定期进行安全审计：
- 使用 Wordfence 进行恶意软件扫描
- 检查文件权限是否正确
- 审查用户账户列表
- 检查登录日志
```

**访问控制**
```
- 限制后台访问IP（通过 .htaccess 或安全插件）
- 使用 VPN 访问敏感管理区域
- 定期审查用户权限
```

### 禁用 XML-RPC 其他方法

**方法4：使用功能钩子**
```php
// 在 functions.php 中添加
function disable_xmlrpc_pingback($methods) {
    unset($methods['pingback.ping']);
    return $methods;
}
add_filter('xmlrpc_methods', 'disable_xmlrpc_pingback');
```

**方法5：使用 .htaccess 限制访问**
```apache
<Files xmlrpc.php>
    Order Deny,Allow
    Deny from all
    Allow from 123.123.123.123  # 允许特定IP
</Files>
```

### 登录安全增强

**4. 限制后台访问**
```
使用 .htaccess 限制 wp-admin 访问：
<Directory /path/to/wp-admin>
    Order Deny,Allow
    Deny from all
    Allow from 123.123.123.123
</Directory>
```

**5. 更改默认管理员用户名**
```
避免使用 "admin" 作为管理员用户名：
1. 创建新管理员账户
2. 登录新账户
3. 删除旧的 admin 账户
```

**6. 登录失败锁定**
```
使用 Limit Login Attempts Reloaded 插件：
- 设置最大尝试次数（建议5次）
- 设置锁定时间（建议15分钟）
- 启用通知邮件
```

### 安全检查清单

- [ ] WordPress 版本更新
- [ ] 主题/插件更新
- [ ] 强密码策略（12+字符）
- [ ] 文件权限正确（目录755，文件644）
- [ ] 安全插件启用（Wordfence/Defender）
- [ ] XML-RPC 禁用
- [ ] 登录安全设置（双因素认证、登录限制）
- [ ] 定期备份（自动备份已配置）
- [ ] 管理员用户名非 admin
- [ ] 后台访问IP限制
- [ ] SSL证书启用
- [ ] 安全审计定期执行

---

**上一章：[第十章 性能优化](./10_性能优化.md)**

**下一章：[第十二章 高级主题开发](./12_高级主题开发.md)**