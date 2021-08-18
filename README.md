# Docker SSO Server

Self-Hosted SSO Service, Simplify login authentication operations and support OTP login.

This program can also be used as the backend of traefik forward auth.

[中文文档](./README-zhCN.md) | [CHANGELOG](./CHANGELOG.md)

## Snapshots

As a simple and lightweight program, it only has four GUIs: Login, OTP Login, OTP Binding, Account Page

![GUI](./snapshots/gui.png)

## Docker

Try to run a private service in a few seconds:

```bash
docker run -d --name=sso-server \
            -e CLIENT_NAME="YOUR OWN SSO SERVER" \
            -e CLIENT_ID="YOUR_OAUTH_CLIENT_ID" \
            -e CLIENT_SECRET="YOUR_OAUTH_CLIENT_SECRET" \
            -e USER_PASS="password" \
            -p 3000:80 soulteary/sso-server:1.1.2
```

Open `http://localhost:3000/login`, you will see the login screen.

For user login, default username is `username` (without quotes), the password is set by `USER_PASS` variable, the password in the example is `password`.

Note: The contents of the `username` and `user_email` fields used by the service will be initialized according to the "license file" by default. You can read the "Configuration File" below and refer to the sample file to understand how to use the authorization file. You can define your `username` and `email address` by self-generating an authorization file, [click here to generate online](https://readers-lic-gift.suyang.wiki/).

## API && Router

There are two routes that users need to pay attention to:

- Router
  - Login Page: `/login`
  - Logout Page: `/logout`

There are two more routes you may need to know, but they are not important:

- Router
  - Health Check: `/health`
  - Account Page: `/account`

Currently the program supports five commonly used APIs:

- API
  1. User Logout: `/logout`
  2. OAuth Token: `/oauth/token`
  3. OAuth User Info: `/api/userinfo`,
  4. OAuth Client Info: `/api/clientinfo`,
  5. Traefik Forward Auth: `/api/traefik-auth-user`
  6. Outline User Auth (TBD): `Function is ready, wait for scheme stability`

Under normal circumstances, you don’t need to know how to use them, you just need to fill in them in the system you need to authenticate, like this:

```ini
tokenURL           = 'http://host-name-or-ip/oauth/token'
authorizationURL   = 'http://host-name-or-ip/dialog/authorize'
profileUrl         = 'http://host-name-or-ip/api/userinfo'
```

## Fingerprint

In order to ensure the safety of the program, when the program is started, a unique check value will be output.

**If the value does not match the list below, please do not continue to run the program.**


- Version: **soulteary/sso-server:1.1.2**
  - Fingerprint: `060d0706d05dabd66bbc94b858b48176fd0d285ac58416ac052e577b261e5a3d`
  - Docker Image: `e95fa1b6066cf964f9596b8d572db2a0024b1f3029bc34fa03fe721cb5ea30b4`
- Version: **soulteary/sso-server:1.1.1**
  - Fingerprint: `060d0706d05dabd66bbc94b858b481765a08d4b79ffbc7d939a96db4f6a46d6b`
  - Docker Image: `7c7fa08a707acc6999d1e25cf14d3dd00d397f747e8a9dcb22868707f849f7e4`
- Version: **soulteary/sso-server:1.1.0**
  - Fingerprint: `060d0706d05dabd66bbc94b858b4817651fc771139b3323018d9c1eeb1d9794c`
  - Docker Image: `f093c6ab5e825767957c60dfc8f580c7e267e1b7c365062e58fb15a726442c6d`
- Version: **soulteary/sso-server:1.0.0**
  - Fingerprint: `060d0706d05dabd66bbc94b858b48176079287862e8111f46ee08a00e80a130d`
  - Docker Image: `979fa1fc30364647e366c0d0448c38924ad7776a057fd8623ee87e5868941565`

## Login mode support

- OAuth2
- OTP
- (TBD) L2TP

## Resource usage

- CPU USAGE: <1%
- MEM USAGE: ~15MiB

## Config (`.env` file)

In order to ensure that the program can run correctly, we need to define some variables, Most configurations are optional.(Can be ignored)

| Environment variable | Optional | Example | Default | Note |
| --- | --- | --- | --- | --- |
| **USER_PASS** | NO | `YOUR_PASS_WORD` | `password` | The account password. |
| **CLIENT_ID** | NO | `YOUR_CLIENT_ID` | `SjVN7VhgOsku` | Client ID used for OAuth2 authorization |
| **CLIENT_SECRET** | NO | `YOUR_CLIENT_SECRET` | `t3Qt89nv9u5O` | Client Secret used for OAuth2 authorization |
| CLIENT_ISTRUSTED | YES | `true` | `false` | If it is true, the user information can be used without the user's explicit authorization. |
| CLIENT_NAME | YES | `My CLIENT NAME` | `GENERAL SSO SERVER` | Only used for page or command line information display. If not set, will try to use the `SERVER_NAME` variable. |
| SERVER_NAME | Yes | `My SSO Server` | `SSO Server` | Only used for page or command line information display |
| PORT | Yes | `8080` | `80` | Program listening port in the docker |
| OTP_OPTION | Yes | `KEY:m9sEH4JP6C;PERIOD:30` | empty | The OTP parameters specified by the user can be obtained on the user page after the first binding |
| SESSION_SECRET | Yes | `iWoupoFYZ9Ud` | `RANDOM STRING()` | Only used for page or command line information display |
| SERVER_DOMAIN | Yes | `sso.example.com` | empty | Only required in Traefik docker-compose.yml file |
| LICENSE | Yes | `...(huge text)` | `PRESET_LICENSE_TEXT` | The content of the user license agreement, and `LICENSE_FILE` can choose one to use |
| LICENSE_FILE | Yes | `/app/my.lic` | empty | The file path of the user license agreement file, and `LICENSE` can choose one to use |

### Differentiate authorization for different applications

If you need to set different authorization parameters for different applications, you can use a `CLIENT_*` series of parameters.

| Environment variable | Optional | Example | Default | Note |
| --- | --- | --- | --- | --- |
| CLIENT_1_ID | YES | `YOUR_CLIENT_ID` | `SjVN7VhgOsku` | The function is the same as `CLIENT_ID`, which is specified separately for a certain application or a certain type of application |
| CLIENT_1_SECRET | YES | `YOUR_CLIENT_SECRET` | `t3Qt89nv9u5O` | The function is the same as `CLIENT_SECRET`, which is specified separately for a certain application or a certain type of application |
| CLIENT_1_ISTRUSTED | YES | `true` | `false` | The function is the same as `CLIENT_ISTRUSTED`, which is specified separately for a certain application or a certain type of application |
| CLIENT_1_NAME | YES | `My CLIENT NAME` | `GENERAL SSO SERVER` | The function is the same as `CLIENT_NAME`, which is specified separately for a certain application or a certain type of application |

The current program supports `1` to `5`, so we can define five different applications, which are enough for general scenarios.


## Example

The docker example can be viewed [here](./example).

The default account:

- username: `username`
- password: `password`

In order to further improve the security of the program, when the default service restarts, you need to scan the code again to set the OTP verification of the account. This default behavior allows you to change it by setting the fixed `OTP_OPTION`.

## Option: Outbound Restriction

This project does not need to use an external network. If you are not worried about this program, you can refer to the following method to restrict the program from accessing external network data.

https://github.com/francoisruty/fruty_docker-outbound-restriction
