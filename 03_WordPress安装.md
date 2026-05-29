## 第3章：WordPress 安装

### 原理剖析：WordPress 安装本质

**WordPress 是一个 PHP + MySQL 应用，安装过程就是：**
1. 将代码上传到服务器
2. 创建数据库存储数据
3. 配置数据库连接
4. 初始化系统数据

**核心文件结构：**
```
wordpress/
├── wp-config.php    # 数据库配置（安装时生成）
├── wp-admin/        # 后台管理界面
├── wp-includes/     # 核心函数库
└── wp-content/      # 主题、插件、上传文件
```

### 下载 WordPress 7.0

**步骤 1：访问官网**
```
浏览器打开：https://wordpress.org/download/
```

**步骤 2：下载最新版本**
```
点击 "Download WordPress 7.0" 按钮
保存 ZIP 文件到本地
```

**原理：** WordPress 官网提供的是最新稳定版，包含完整的核心代码和默认主题。WordPress 7.0 代号"Armstrong"，于2026年5月20日发布，包含原生AI集成、后台界面重新设计等重要更新。

### 上传到服务器

**方法1：使用 FileZilla（推荐新手）**

1. **连接服务器**
```
主机：yourdomain.com（或服务器IP）
用户名：ftp_username（主机商提供）
密码：ftp_password（主机商提供）
端口：21（默认FTP端口）
```

2. **上传文件**
```
本地：解压后的 wordpress 文件夹
远程：public_html 或 www 目录（网站根目录）
```

**方法2：使用 cPanel 文件管理器**

1. **登录 cPanel**
```
访问：yourdomain.com/cpanel
输入用户名和密码（主机商提供）
```

2. **上传压缩包**
```
文件 → 文件管理器 → public_html
点击 "上传" → 选择 wordpress.zip
右键压缩包 → "解压" → "提取到当前目录"
```

**原理：** 服务器需要将 WordPress 文件放置在网站根目录，这样用户访问域名时才能加载。

### 配置数据库

**原理：** WordPress 使用 MySQL 存储所有数据（文章、用户、设置等），必须先创建数据库和用户。

**步骤1：创建数据库（cPanel）**

1. 登录 cPanel → 点击「MySQL数据库」
2. 数据库名：输入 `wp_db` → 点击「创建数据库」
3. 创建用户：用户名 `wp_user`，密码使用「密码生成器」生成强密码
4. 添加用户到数据库：选择 `wp_user` 和 `wp_db` → 点击「添加」
5. 权限：勾选「全部权限」→ 点击「进行更改」

**步骤 2：手动 SQL 创建（高级用户）**
```sql
-- 创建数据库（指定UTF-8字符集）
CREATE DATABASE wp_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 创建用户
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'YourStrongPassword123!';

-- 授权
GRANT ALL PRIVILEGES ON wp_db.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
```

**原理：** `utf8mb4` 支持完整的 Unicode 字符（包括emoji），比旧的 `utf8` 更全面。

**安全建议：** 
- 数据库用户名不要使用 `root`，应创建专用用户
- 密码应包含大小写字母、数字和特殊字符，长度至少12位
- 建议修改表前缀 `wp_` 为自定义前缀（如 `myblog_`），防止 SQL 注入攻击

### 完成安装流程

**Step 1：访问安装页面**
```
浏览器输入：http://yourdomain.com/wp-admin/install.php
```
**原理：** WordPress 检测到没有 wp-config.php 文件时，自动启动安装向导。

**Step 2：选择语言**
```
选择 "简体中文" → 点击 "继续"
```

**Step 3：填写数据库信息**

| 字段 | 示例值 | 说明 |
|-----|-------|-----|
| 数据库名 | wp_db | 刚才创建的数据库名称 |
| 用户名 | wp_user | 数据库用户名 |
| 密码 | YourStrongPassword123! | 数据库密码 |
| 数据库主机 | localhost | 数据库服务器地址（通常是localhost） |
| 表前缀 | wp_ | 数据库表前缀，**安全建议修改为自定义前缀**（如 myblog_） |

点击「提交」→ 「运行安装程序」

**原理：** WordPress 7.0 采用新的 DataViews 安装界面，比旧版列表表格更直观。表前缀用于区分同一数据库中的多个 WordPress 安装，使用自定义前缀可增加安全性。

**Step 4：设置网站信息**

| 字段 | 说明 |
|-----|-----|
| 网站标题 | 你的网站名称（可修改） |
| 用户名 | 管理员账户名（建议不要用admin） |
| 密码 | 使用自动生成的强密码，保存到安全地方 |
| 邮箱 | 管理员邮箱（用于找回密码） |

**原理：** 这一步会在数据库中创建初始数据（管理员用户、默认设置等）。

**Step 5：完成安装**
```
点击 "安装 WordPress" → 成功后点击 "登录"
```

### 安装后的必要设置

**1. 更新固定链接（重要）**
```
仪表盘 → 设置 → 固定链接
选择 "文章名称" → 点击 "保存更改"
```
**原理：** 固定链接设置决定 URL 结构，`/%postname%/` 格式最利于 SEO。WordPress 通过 `.htaccess`（Apache）或 `nginx.conf`（Nginx）实现 URL 重写，将美观的 URL 转换为实际的查询参数。

**2. 删除默认内容**
```
文章 → 所有文章 → 悬停在 "欢迎来到 WordPress" 上 → 点击 "删除"
页面 → 所有页面 → 删除 "示例页面"
```

**3. 安装必要插件**
```
插件 → 安装插件
搜索并安装：
- Yoast SEO 或 Rank Math（SEO优化）
- WP Super Cache 或 WP Rocket（缓存加速）
- Wordfence Security（安全防护）
```

**4. 配置 AI Connectors（WordPress 7.0 新功能）**
```
仪表盘 → 设置 → Connectors
添加 AI 提供商（OpenAI、Anthropic 等）
配置 API 密钥
测试连接
```
**原理：** WordPress 7.0 原生集成 AI 能力，通过 Connectors UI 集中管理 AI 连接。配置后，可在区块编辑器中使用 AI 生成内容、调整语气、创建区块模式等。

**5. 安全加固设置**
```
- 修改默认登录 URL（使用 WPS Hide Login 插件）
- 启用双因素认证（使用 Google Authenticator 插件）
- 限制登录尝试次数（使用 Limit Login Attempts Reloaded 插件）
- 禁用 XML-RPC（如不需要远程发布功能）
```

### 实战：本地快速安装（Laragon）

**Step 1：启动 Laragon**
```
双击 Laragon 图标 → 点击 "Start All"
```

**Step 2：创建站点**
```
右键 Laragon 托盘图标 → Quick App → WordPress
输入站点名：myblog → 回车
```

**Step 3：访问网站**
```
浏览器输入：http://myblog.test
用户名：admin
密码：密码在命令行窗口显示
```

**原理：** Laragon 自动完成了：
- 创建数据库
- 配置 wp-config.php
- 设置虚拟主机

### 常见错误及原理分析

**错误1：数据库连接失败**
```
原因：wp-config.php 中的数据库信息不正确
解决：检查 DB_NAME、DB_USER、DB_PASSWORD 是否与数据库设置一致
```

**原理：** WordPress 通过 wp-config.php 中的数据库配置信息连接 MySQL 数据库，任何一项配置错误都会导致连接失败。

**详细解决方案：**
- 检查数据库名、用户名、密码是否正确
- 确认数据库主机地址（VPS 通常用 localhost）
- 验证数据库服务是否正常运行
- 检查数据库用户权限设置
- 使用 phpMyAdmin 测试数据库连接

**错误2：权限不足**
```
原因：服务器没有写入 wp-content 目录的权限
解决：设置目录权限为 755
命令：chmod -R 755 wp-content
```

**详细解决方案：**
- **宝塔面板：** 文件管理 → 权限设置为 755，所有者改为 www
- **命令行：** chmod -R 755 * && chown -R www:www *
- 确保 wp-content 目录具有写入权限

**错误3：内存不足**
```
原因：PHP 默认内存限制（通常128M）不足以运行 WordPress
解决：在 wp-config.php 添加
define('WP_MEMORY_LIMIT', '256M');
```

**错误4：主题插件安装错误**
```
原因：网络连接、服务器环境或兼容性问题
解决：检查 PHP 版本是否符合要求，增加服务器内存限制
```

**详细解决方案：**
- 检查 PHP 版本是否符合要求（WordPress 7.0 要求 PHP ≥ 8.0）
- 增加服务器内存限制（Memory Limit）
- 网站崩溃时通过 FTP 删除问题文件夹
- 使用手动上传方式安装

**错误5：429 Too Many Requests**
```
原因：请求过于频繁被服务器限制
解决：等待一段时间后重试，或联系主机商调整限制
```

### WordPress 安全预防措施与最佳实践

**定期备份**
```
安装新主题或插件前，务必备份网站数据和数据库。
推荐使用 UpdraftPlus 插件进行自动备份。
```

**测试环境**
```
在本地或测试服务器上先测试新功能，确认无误后再部署到生产环境。
```

**记录信息**
```
保存好数据库连接信息、FTP 账号等重要配置信息。
```

**保持更新**
```
定期更新 WordPress 核心、主题和插件到最新版本。
仪表盘 → 更新 → 一键更新
```

### 安装流程图

```
下载 WordPress
    ↓
上传到服务器
    ↓
创建数据库（MySQL）
    ↓
配置 wp-config.php
    ↓
运行安装向导（初始化数据库表）
    ↓
完成安装
    ↓
基础设置（固定链接、插件）
```

---

**上一章：[第2章 准备工作](./02_准备工作.md)**

**下一章：[第4章 WordPress后台基础](./04_WordPress后台基础.md)**