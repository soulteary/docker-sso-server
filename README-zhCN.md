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
            -p 3000:80 soulteary/sso-server:1.1.0
```

服务启动之后，使用浏览器访问 `http://localhost:3000/login`，将可以看到登录界面。

默认的账号密码是 `useranme`（不带引号）和 `password`（密码由用户通过 `USER_PASS` 变量设置，示例中的密码是这个）。

注意：你可以阅读下文中“配置文件”以及参考示例文件，来了解如何获取授权文件。服务使用的 `username` 和 `user_email` 字段内容，默认将根据“授权许可文件”进行初始化。

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
  6. Outline User Auth (依旧在调整): `Function is ready, wait for scheme stability`

一般情况下，你不需要知道如何使用它们，你只需要知道如何将它们填入需要使用认证的程序配置里就行了，像是这样：

```ini
tokenURL           = 'http://host-name-or-ip/oauth/token'
authorizationURL   = 'http://host-name-or-ip/dialog/authorize'
profileUrl         = 'http://host-name-or-ip/api/userinfo'
```

## 程序指纹

为了保证程序的安全，程序启动时，会输出一个唯一的校验值。

**如果数值与下表不符，请不要继续运行程序。**

- 版本: **soulteary/sso-server:1.1.1**
  - 指纹: `060d0706d05dabd66bbc94b858b481765a08d4b79ffbc7d939a96db4f6a46d6b`
    - Docker Image: `7c7fa08a707acc6999d1e25cf14d3dd00d397f747e8a9dcb22868707f849f7e4`
- 版本: **soulteary/sso-server:1.1.0**
  - 指纹: `060d0706d05dabd66bbc94b858b4817651fc771139b3323018d9c1eeb1d9794c`
  - Docker Image: `f093c6ab5e825767957c60dfc8f580c7e267e1b7c365062e58fb15a726442c6d`
- 版本: **soulteary/sso-server:1.0.0**
  - 指纹: `060d0706d05dabd66bbc94b858b48176079287862e8111f46ee08a00e80a130d`
  - Docker Image: `979fa1fc30364647e366c0d0448c38924ad7776a057fd8623ee87e5868941565`

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
| SERVER_NAME | 是 | `My SSO Server` | `SSO Server` | 仅用于页面或命令行信息显示 |
| PORT | 是 | `8080` | `80` | docker中的程序监听端口 |
| OTP_OPTION | Yes | `KEY:m9sEH4JP6C;PERIOD:30` | 空 | 用户指定的OTP参数，第一次绑定后，可以在用户页面获得 |
| SESSION_SECRET | 是 | `iWoupoFYZ9Ud` | `RANDOM STRING()` | 仅用于页面或命令行信息显示 |
| SERVER_DOMAIN | 是 | `sso.example.com` | 空 | 仅在 Traefik docker-compose.yml 文件中需要 |
| LICENSE | 是 | `...(huge text)` | `PRESET_LICENSE_TEXT` | 用户许可协议的内容，和 `LICENSE_FILE` 二选一使用 |
| LICENSE_FILE | 是 | `/app/my.lic` | 空 | 用户许可协议文件的文件路径，和 `LICENSE` 二选一使用 |

## 示例

可以在[此处](./example)查看更多 docker 示例。

默认账号：

- 用户名: `username`
- 密码: `password`

为了进一步提高程序的安全性，默认服务重启时需要重新扫码设置账户的 OTP 验证。这个默认行为允许通设置固定的 `OTP_OPTION` 进行变更。

## 可选项：出站网络限制

本项目不需要使用外网服务。如果你担心这个程序，可以参考下面的方法来限制容器中的程序访问外网数据，从根本上解决你的顾虑，:D

https://github.com/francoisruty/fruty_docker-outbound-restriction
