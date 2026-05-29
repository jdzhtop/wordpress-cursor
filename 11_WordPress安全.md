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

### 安全检查清单

- [ ] WordPress 版本更新
- [ ] 主题/插件更新
- [ ] 强密码策略
- [ ] 文件权限正确
- [ ] 安全插件启用
- [ ] XML-RPC 禁用
- [ ] 登录安全设置
- [ ] 定期备份

---

**下一章：第十二章 高级主题开发**