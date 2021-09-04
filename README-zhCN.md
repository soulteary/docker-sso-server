# 单点登录服务

一个简单、轻量支持自托管的单点登录服务，用于简化登录认证操作，支持 OTP 方式登录，同时可以作为 `Traefik Forward Auth` 后端使用。

[变更日志](./CHANGELOG-zhCN.md)

## 程序截图

作为一个简单的轻量级程序，它只有四个图形界面：登录、OTP登录、OTP绑定、帐户页面。

![用户界面](./snapshots/gui.png)

## Docker

你可以尝试使用 Docker 在几秒钟内运行一个属于你的私有服务：

```bash
docker run -d --name=sso-server \
            -e CLIENT_NAME="YOUR OWN SSO SERVER" \
            -e CLIENT_ID="YOUR_OAUTH_CLIENT_ID" \
            -e CLIENT_SECRET="YOUR_OAUTH_CLIENT_SECRET" \
            -e USER_PASS="password" \
            -p 3000:80 soulteary/sso-server:1.1.5
```

服务启动之后，使用浏览器访问 `http://localhost:3000/login`，将可以看到登录界面。

默认的账号密码是 `username`（不带引号）和 `password`（密码由用户通过 `USER_PASS` 变量设置，示例中的密码是这个）。

注意：服务使用的 `username` 和 `user_email` 字段内容，默认将根据“授权许可文件”进行初始化。你可以阅读下文中“配置文件”以及参考示例文件，来了解如何使用授权文件。你可以通过自助生成授权文件来定义你的`用户名`和`邮箱地址`，[点击此处在线生成](https://readers-lic-gift.suyang.wiki/)。


## API 及路由

作为用户，我们只需要了解两个路由即可：

- Router
  - 登录页面: `/login`
  - 登出登录: `/logout`

除此之外，还有两个不太重要的路由，它们是：

- Router
  - 用于容器健康检查使用: `/health`
  - 用户账号信息展示: `/account`

程序当前支持五种常用的 API：

- API
  1. 当前账号登出: `/logout`
  2. OAuth Token: `/oauth/token`
  3. OAuth 用户信息: `/api/userinfo`,
  4. OAuth 客户端信息: `/api/clientinfo`,
  5. Traefik Forward Auth: `/api/traefik-auth-user`
  6. Outline 用户信息: `/api/outline/oidc`

一般情况下，你不需要知道如何使用它们，你只需要知道如何将它们填入需要使用认证的程序配置里就行了，像是这样：

```ini
tokenURL           = 'http://host-name-or-ip/oauth/token'
authorizationURL   = 'http://host-name-or-ip/dialog/authorize'
profileUrl         = 'http://host-name-or-ip/api/userinfo'
```

## 程序指纹

为了保证程序的安全，程序启动时，会输出一个唯一的校验值。

**如果数值与下表不符，请不要继续运行程序。**

- 版本: **soulteary/sso-server:1.1.5**
  - 指纹: `060d0706d05dabd66bbc94b858b481763150ef669078dfa92e9e0c76b1758bf8`
  - Docker Image: `2cf9ba5e79fb47cbd768381e4ba7e34a1d446a5495bf4c5e32d283ee00360738`

查看所有[历史版本](./CHECKSUM.md)的指纹。
## 当前支持登录模式

- OAuth2
- OTP
- (计划中) L2TP

## 资源使用

- CPU 使用率: <1%
- MEM 使用率: ~15MiB

## 配置文件 (`.env` 及环境变量)

为了保证程序能够正确运行，我们需要定义一些变量，其中大部分配置都是可选的。（可以忽略）

| 环境变化 | 是否可选 | 示例 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| **USER_PASS** | 否 | `YOUR_PASS_WORD` | `password` | 设置属于你的账号密码。 |
| **CLIENT_ID** | 否 | `YOUR_CLIENT_ID` | `SjVN7VhgOsku` | OAuth2 认证需要使用的 Client ID  |
| **CLIENT_SECRET** | 否 | `YOUR_CLIENT_SECRET` | `t3Qt89nv9u5O` | OAuth2 认证需要使用的 Client Secret |
| CLIENT_ISTRUSTED | 是 | `true` | `false` | 如果设置为 True，则需要授权的程序无需用户明确授权确认即可使用用户信息 |
| CLIENT_NAME | 是 | `My CLIENT NAME` | `GENERAL SSO SERVER` | 仅用于页面或命令行信息显示。如果未设置，将尝试使用 `SERVER_NAME` 变量进行替代。 |
| SERVER_NAME | 是 | `My SSO Server` | `SSO Server` | 仅用于OTP工具、Web 页面或命令行信息显示 |
| PORT | 是 | `8080` | `80` | docker中的程序监听端口 |
| OTP_OPTION | Yes | `KEY:MIZUSR2ZJZTWUSDY;PERIOD:30` | 空 | 用户指定的OTP参数，第一次绑定后，可以在用户页面获得 |
| SESSION_SECRET | 是 | `iWoupoFYZ9Ud` | `RANDOM STRING()` | 仅用于页面或命令行信息显示 |
| SERVER_DOMAIN | 是 | `sso.example.com` | 空 | 仅在 Traefik docker-compose.yml 文件中需要 |
| LICENSE | 是 | `...(huge text)` | `PRESET_LICENSE_TEXT` | 用户许可协议的内容，和 `LICENSE_FILE` 二选一使用 |
| LICENSE_FILE | 是 | `/app/my.lic` | 空 | 用户许可协议文件的文件路径，和 `LICENSE` 二选一使用 |

### 针对不同应用进行授权区分

如果你需要针对不同的应用设置不同的授权参数，则可以使用 `CLIENT_*` 系列参数。

| 环境变化 | 是否可选 | 示例 | 默认值 | 说明 |
| --- | --- | --- | --- | --- |
| CLIENT_1_ID | 否 | `YOUR_CLIENT_ID` | `SjVN7VhgOsku` | 作用和 `CLIENT_ID` 一致，为某个应用或者某类应用单独指定 |
| CLIENT_1_SECRET | 否 | `YOUR_CLIENT_SECRET` | `t3Qt89nv9u5O` | 作用和 `CLIENT_SECRET` 一致，为某个应用或者某类应用单独指定 |
| CLIENT_1_ISTRUSTED | 是 | `true` | `false` | 作用和 `CLIENT_ISTRUSTED` 一致，为某个应用或者某类应用单独指定 |
| CLIENT_1_NAME | 是 | `My CLIENT NAME` | `GENERAL SSO SERVER` | 作用和 `CLIENT_NAME` 一致，为某个应用或者某类应用单独指定 |

目前程序支持 1 ～ 5，所以我们可以定义五个不同的应用，应该足够一般场景使用。

## 示例

可以在[此处](./example)查看更多 docker 示例。

默认账号：

- 用户名: `username`
- 密码: `password`

为了进一步提高程序的安全性，默认服务重启时需要重新扫码设置账户的 OTP 验证。这个默认行为允许通设置固定的 `OTP_OPTION` 进行变更。

## 可选项：出站网络限制

本项目不需要使用外网服务。如果你担心这个程序，可以参考下面的方法来限制容器中的程序访问外网数据，从根本上解决你的顾虑，:D

https://github.com/francoisruty/fruty_docker-outbound-restriction
