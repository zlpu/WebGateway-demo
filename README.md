<div align="center">

# WebGateway

### 让站点入口、安全策略与运行状态一目了然

面向个人服务器与团队业务的 Web 入口管理和安全运营平台。

![版本](https://img.shields.io/badge/版本-v1.0.0-2563eb?style=flat-square)
![部署方式](https://img.shields.io/badge/部署-Docker%20Compose-0ea5e9?style=flat-square)

[快速部署](#-docker-compose-安装与部署) · [功能介绍](#-功能一览) · [使用教程](#-快速上手) · [问题反馈](https://github.com/zlpu/webgateway-demo/issues)

</div>

---
<img width="1912" height="966" alt="image" src="https://github.com/user-attachments/assets/56188a8b-7e16-4b18-ba29-39976bb8e514" />

## ✨ 关于 WebGateway

WebGateway 把站点转发、HTTPS 证书、访问控制、流量分析、安全检测和运行告警放进一个简洁的中文控制台。你可以集中维护对外入口，快速了解访问变化，并在出现异常时及时采取措施。

无论是个人服务器、家庭实验环境，还是小型团队的业务网关，都可以通过浏览器完成日常配置与巡检。

## 🧭 功能一览

| 模块 | 能做什么 |
| --- | --- |
| **站点入口** | 按域名转发请求、设置域名跳转、管理 TCP/UDP 端口转发，并配置默认响应站点。 |
| **HTTPS 证书** | 申请和自动续期证书、上传已有证书、查看证书到期时间，并为站点启用 HTTPS。 |
| **访问控制** | 用账号密码或 IP/CIDR 规则限制访问；管理允许名单、阻止名单与安全策略。 |
| **实时流量分析** | 查看请求趋势、访问来源、状态码、响应耗时、热门域名和实时请求记录。 |
| **安全检测** | 识别常见恶意请求特征，查看攻击来源与风险变化；按需启用自动封禁。 |
| **监控告警** | 根据请求量、错误率、攻击事件和证书有效期设置阈值，并发送通知。 |
| **账户与审计** | 管理账户和权限、设置多因素认证、查看操作记录，并维护个人邮箱与头像。 |

> 自动封禁默认关闭。建议先观察实际访问情况，再根据业务流量设置阈值。

## 🚀 Docker Compose 安装与部署

### 1. 准备 Docker Compose

WebGateway 使用 Compose v2。Windows 和 macOS 用户安装 [Docker Desktop](https://www.docker.com/products/docker-desktop/) 后即可使用 `docker compose` 命令。

Ubuntu 或 Debian 服务器请先安装 Docker Engine，并配置 Docker 官方软件源，然后安装 Compose 插件：

```bash
sudo apt-get update
sudo apt-get install -y docker-compose-plugin
docker compose version
```

若系统提示找不到安装包，请先按照 [Docker Engine 安装说明](https://docs.docker.com/engine/install/) 配置对应发行版的软件源。看到 `Docker Compose version v2...` 即表示准备完成。

### 2. 创建部署目录并取得配置文件

Linux/macOS：

```bash
sudo mkdir -p /opt/webgateway
sudo chown "$USER":"$USER" /opt/webgateway
cd /opt/webgateway
curl -fsSL https://raw.githubusercontent.com/zlpu/webgateway-demo/main/docker-compose.yml -o docker-compose.yml
```

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force C:\webgateway | Out-Null
Set-Location C:\webgateway
Invoke-WebRequest https://raw.githubusercontent.com/zlpu/webgateway-demo/main/docker-compose.yml -OutFile docker-compose.yml
```

### 3. 设置首次登录信息

在 `docker-compose.yml` 同一目录创建 `.env` 文件：

```dotenv
TZ=Asia/Shanghai
INITIAL_ADMIN_EMAIL=webgateway
INITIAL_ADMIN_PASSWORD=请替换为至少8位的强密码
LETSENCRYPT_EMAIL=admin@example.com

# 可选：启用 IP 归属地数据时填写
GEOLITE2_LICENSE_KEY=
```

`INITIAL_ADMIN_EMAIL` 是首次登录使用的账户名，可先使用 `webgateway`。首次登录后，系统会要求你设置有效邮箱并更换初始密码；完成后请使用新邮箱和新密码重新登录。请勿将 `.env` 分享到公开位置。

### 4. 启动并检查服务

```bash
docker compose pull
docker compose up -d
docker compose ps
docker compose logs -f webgateway
```

容器启动完成后，打开：

```text
http://服务器地址:8234
```

首次启动会准备系统数据，可能需要几十秒。浏览器首次访问时使用 `.env` 中设置的账户名和密码。

### 5. 常用 Compose 命令

| 操作 | 命令 |
| --- | --- |
| 查看运行状态 | `docker compose ps` |
| 查看启动日志 | `docker compose logs -f webgateway` |
| 重启服务 | `docker compose restart` |
| 更新到当前配置指定的版本 | `docker compose pull && docker compose up -d` |
| 停止服务但保留数据 | `docker compose down` |

`docker compose down` 不会删除 `./data` 和 `./letsencrypt`。请不要使用会同时删除数据卷的清理命令，除非你确实要重置系统。

## 📘 快速上手

### 第一步：完善账户资料

首次登录按页面提示修改密码并填写有效邮箱。进入右上角用户菜单，可随时更新个人资料；个人头像支持 JPG、PNG 图片，单张最大 2 MB。

### 第二步：准备域名与上游服务

将需要管理的域名解析到服务器公网地址，并确认上游服务可以从 WebGateway 所在环境访问。申请 HTTPS 证书时，公网需要能够访问服务器的 80 和 443 端口。

### 第三步：添加站点转发

1. 打开「站点入口」中的代理主机管理，选择新增。
2. 填写域名、上游协议、上游地址和端口。
3. 按需启用 WebSocket、访问限制或高级规则。
4. 保存后访问域名，确认页面可以正常打开。

### 第四步：启用 HTTPS

1. 打开证书管理并新增证书。
2. 选择自动申请，填写域名和可接收通知的邮箱；也可以上传已有证书。
3. 回到站点规则，选择证书并启用强制 HTTPS。
4. 确认域名解析正确，且服务器 80、443 端口可以从公网访问。

### 第五步：查看访问与安全情况

「系统总览」展示站点规则、请求趋势和服务状态；「流量分析」提供更细的来源与响应统计；「安全策略」和「告警中心」可用于查看风险、配置通知和调整阈值。

### TCP/UDP 端口转发

Compose 默认开放 Web 访问所需的 80、443 和管理端口 8234。使用 TCP/UDP 转发时，还需要在 `docker-compose.yml` 的 `ports` 下增加对应映射，例如：

```yaml
      - "2222:2222/tcp"
      - "5353:5353/udp"
```

同时检查云平台安全组和服务器防火墙，确保对应端口允许访问。

## ⚙️ 部署配置

| 配置项 | 用途 | 默认值 |
| --- | --- | --- |
| `TZ` | 系统显示时区 | `Asia/Shanghai` |
| `INITIAL_ADMIN_EMAIL` | 首次登录账户名 | `webgateway` |
| `INITIAL_ADMIN_PASSWORD` | 首次登录密码 | 必填，请自行设置 |
| `LETSENCRYPT_EMAIL` | 证书申请与到期通知邮箱 | 必填 |
| `GEOLITE2_LICENSE_KEY` | IP 归属地数据更新许可 | 可留空 |
| `ANALYTICS_RETENTION_DAYS` | 分析数据保留天数 | `30` |
| `WAF_AUTO_BLOCK_THRESHOLD` | 自动封禁触发阈值；`0` 表示关闭 | `0` |

**默认端口**

| 端口 | 用途 | 建议 |
| --- | --- | --- |
| `80/tcp` | 站点访问与证书验证 | 按站点需要开放 |
| `443/tcp` | HTTPS 站点访问 | 按站点需要开放 |
| `8234/tcp` | WebGateway 管理控制台 | 建议仅允许可信地址访问 |

若服务器的 80 或 443 端口已被其他服务占用，请先调整现有服务，或在 Compose 文件中配置合适的宿主机端口映射。

## 💾 数据备份与升级

系统数据保存在部署目录下的 `data/` 和 `letsencrypt/`。建议定期备份这两个目录；升级前也应先完成备份。

```bash
docker compose down
tar -czf webgateway-backup-$(date +%F).tar.gz data letsencrypt
docker compose pull
docker compose up -d
```

恢复时先停止服务，再将备份中的 `data/` 和 `letsencrypt/` 放回部署目录，最后运行 `docker compose up -d`。

## 🛠️ 常见问题

**管理页面无法打开**

- 确认服务已启动：`docker compose ps`。
- 查看启动日志：`docker compose logs --tail=200 webgateway`。
- 确认访问地址端口为 `8234`，并检查服务器防火墙和云平台安全组。
- 如果端口被占用，修改 Compose 映射左侧的宿主机端口。

**证书申请失败**

- 确认域名解析到当前服务器公网地址。
- 确认公网可以访问服务器的 80、443 端口。
- 检查证书申请邮箱和域名拼写，并查看系统日志中的具体提示。

**登录后仍提示完善首次资料**

- 确认邮箱格式正确且未被其他账户使用。
- 新密码需要 8 至 64 位，且必须与当前密码不同。
- 保存完成后使用新邮箱和新密码重新登录。

**修改 Compose 配置后如何生效**

```bash
docker compose up -d
```

## 🔐 安全建议

- 首次登录立即更换初始密码，并填写可用邮箱。
- 管理端口 8234 只对管理员可信网络开放；需要远程使用时优先通过 VPN 或受控入口访问。
- 定期备份 `data/` 与 `letsencrypt/`，并妥善保管备份文件。
- 自动封禁会影响真实访问者，启用前请先观察分析结果并设置适合业务的阈值。

## 💬 问题反馈

使用过程中遇到问题或有功能建议，请前往 [WebGateway 项目主页](https://github.com/zlpu/webgateway-demo) 提交反馈。
