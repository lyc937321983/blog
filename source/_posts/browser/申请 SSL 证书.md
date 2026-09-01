---
title: 申请 SSL 证书
date: 2026-09-01 14:20:22
tags: http SSL
comment: 'valine'
categories: 
- browser
---

# 申请 SSL 证书

### **🔒 服务器配置 SSL 证书完整指南**

在服务器上配置 SSL 证书，整体流程可以分为 **准备 → 申请证书 → 部署配置 → 验证优化** 四个阶段。以下是详细步骤：

------

### **一、配置前准备**

- **域名解析**：确保域名已通过 A 记录解析到香港服务器的 IP 地址
- **端口放行**：检查服务器防火墙和云服务商安全组，确保 **443 端口**（HTTPS 默认端口）已开放
- **服务器环境**：确认 Nginx/Apache 版本支持 TLS 1.2/1.3，并安装相应 SSL 模块（如 Apache 的 `mod_ssl`）

------

### **二、申请 SSL 证书**

根据你的需求选择合适的证书类型和申请渠道：

#### **证书类型**

表格

| 类型               | 验证方式          | 适用场景           | 签发速度    |
| :----------------- | :---------------- | :----------------- | :---------- |
| **DV（域名验证）** | 仅验证域名所有权  | 个人博客、小型网站 | 5分钟内     |
| **OV（企业验证）** | 验证企业资质+域名 | 电商、中型企业     | 2-3个工作日 |
| **EV（扩展验证）** | 严格企业+法律验证 | 银行、支付类网站   | 1-2周       |

> 💡 对于大多数香港服务器用户，DV 证书已足够，成本更低甚至免费。

#### **申请渠道**

- **免费方案**：Let's Encrypt（支持自动续期，有效期90天）
- **国内云服务商**：阿里云、腾讯云等（需实名认证，售后支持完善）
- **国际 CA 机构**：DigiCert、Sectigo 等（适合面向海外用户）

------

### **三、部署配置（核心步骤）**

#### **方式一：Let's Encrypt 自动部署（推荐新手）**

使用 Certbot 工具，一条命令即可完成申请+配置+自动续期：

**安装 Certbot：**

```sh
# CentOS
yum install -y epel-release
yum install -y certbot python3-certbot-nginx

# Ubuntu/Debian
apt update
apt install -y certbot python3-certbot-nginx
```

**一键申请并配置：**

```sh
certbot --nginx -d example.com -d www.example.com
```

Certbot 会自动完成证书申请、修改 Nginx 配置、设置 HTTP→HTTPS 跳转和自动续期。

#### **方式二：手动部署（已有证书文件）**

**1. 上传证书文件**

将证书文件（`.crt`）和私钥文件（`.key`）上传至服务器指定目录：

```sh
mkdir -p /etc/nginx/ssl
cp example.com.crt /etc/nginx/ssl/
cp example.com.key /etc/nginx/ssl/
chmod 600 /etc/nginx/ssl/*
```

**2. Nginx 配置示例：**

```nginx
# HTTPS 配置
server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate /etc/nginx/ssl/example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/example.com.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # 启用 HSTS
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    root /home/wwwroot/example.com;
    index index.html index.php;
}

# HTTP 强制跳转 HTTPS
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}
```

**3. Apache 配置示例：**

```apl
<VirtualHost *:443>
    ServerName example.com
    DocumentRoot /path/to/your/website

    SSLEngine on
    SSLCertificateFile /etc/httpd/ssl/example.com.crt
    SSLCertificateKeyFile /etc/httpd/ssl/example.com.key
    SSLCertificateChainFile /etc/httpd/ssl/ca_bundle.crt

    SSLProtocol -all +TLSv1.2 +TLSv1.3
    SSLCipherSuite ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
</VirtualHost>
```

**4. 检查并重载服务：**

```sh
# Nginx
nginx -t && systemctl reload nginx

# Apache
apachectl configtest && systemctl restart httpd
```

------

### **四、验证与优化**

配置完成后，建议进行以下检查：

- **浏览器验证**：访问 `https://你的域名`，确认地址栏显示🔒小锁图标
- **跳转测试**：访问 HTTP 版本，确认自动跳转到 HTTPS
- **在线检测**：使用 [SSL Labs SSL Test](https://www.ssllabs.com/ssltest/) 扫描域名，理想结果为 **A+ 评级**
- 常见问题排查：
  - **443 端口无法访问** → 检查安全组和防火墙规则
  - **证书链不完整** → 使用 `fullchain.crt`（包含中间证书）替代单独的 `cert.crt`
  - **评级低于 B** → 优化加密套件或启用 OCSP Stapling

------

### **五、香港服务器特别注意事项**

- 香港服务器面向的用户可能覆盖内地、东南亚及海外，HTTPS 是建立国际信任的基础
- 若使用 CDN（如 Cloudflare），需在 CDN 后台单独配置证书
- 若使用共享 IP，部分 CA 机构可能限制证书类型，需提前确认
- 建议启用 **TLS 1.3** 和 **HSTS**，禁用 SSLv3、TLS 1.0/1.1 等不安全协议
