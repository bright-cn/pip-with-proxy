# 通过代理使用 pip

[![推广](https://github.com/bright-cn/Awesome-Web-Scraping/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner%20CN.png)](https://www.bright.cn/) 

本指南介绍如何为 pip 设置代理，以绕过限制、提高安全性并简化软件包管理：

- [公共代理与私有代理](#公共代理与私有代理)
- [通过代理使用 pip](#通过代理使用-pip-1)
- [使用命令行配置 pip 代理](#使用命令行配置-pip-代理)
- [使用 pip 配置文件配置代理](#使用-pip-配置文件配置代理)
- [使用环境变量配置 pip 代理](#使用环境变量配置-pip-代理)
- [测试配置](#测试配置)
- [pip 代理故障排除](#pip-代理故障排除)
- [通过动态代理使用 pip](#通过动态代理使用-pip)
- [通过代理使用 pip 的优势](#通过代理使用-pip-的优势)
- [常见错误与最佳实践](#常见错误与最佳实践)
- [使用 Bright Data 代理](#使用-bright-data-代理)
- [总结](#总结)

## 公共代理与私有代理

### 公共代理

任何人都可以访问公共代理，而且通常不需要身份验证。虽然它们能让你快速使用其他 IP 地址，但也存在一些明显缺点，包括速度较慢、连接不稳定以及 IP 被封禁的风险较高。由于公共代理免费且应用广泛，它们通常缺少代理轮换、缓存和访问控制等关键功能，因此不适合在生产环境中可靠使用。

公共 URL 的格式可能如下： `https://proxyserver:port`。

### 私有代理

[私有代理](https://www.bright.cn/solutions/private-proxies)需要身份验证，可提供更高的安全性、稳定性和高级功能，但通常需要付费。它们通过[专用 IP 地址](https://www.bright.cn/solutions/dedicated-proxies)提供快速、可靠的连接，并包含代理身份验证和轮换等功能，从而提升性能和控制能力。

访问权限通常通过身份验证来控制，一般是在代理 URL 前添加用户名和密码，如下所示： `https://username:password@proxyserver:port`。

## 通过代理使用 pip

通过代理使用 pip 之前，你需要收集代理的相关信息。以下示例演示如何使用具有这些设置的公共代理：

- 代理服务的**代理地址**
- 代理服务通信所需的**端口**

[以下 `proxy-list` 仓库](https://github.com/clarketm/proxy-list)提供每日经过测试的公共代理地址，可用于测试，但不应在生产环境中使用。

在 `proxy-list` 仓库中，检查 [`proxy-list-status.txt`](https://github.com/clarketm/proxy-list/blob/master/proxy-list-status.txt) 文件以查找可用的公共代理。你可以查看文件中地址旁是否带有 `success` 标记，该标记表示代理可用：

![选择公共代理](https://github.com/bright-cn/pip-with-proxy/blob/main/Images/Selecting-a-public-proxy.png)

本教程使用 `45.185.162.203:999` 作为公共代理地址。这意味着代理服务器地址为 `http://45.185.162.203:999`。

## 使用命令行配置 pip 代理

配置 pip 代理最快捷的方法是在调用 `pip install` 命令时，通过 `--proxy` 命令行选项传入地址。

要验证能否通过公共代理访问并测试软件包检索，请运行以下命令：

```
# Public Proxy
pip install boto3 --proxy http://45.185.162.203:999
```

这种方法适合在永久配置代理之前快速进行测试和验证。对于发布 pip 软件包的用户，它还有助于确认是否可以通过其他 IP 地址访问。

## 使用 pip 配置文件配置代理

要永久配置 pip 代理，可以使用简单的声明式解决方案：pip 配置文件。该文件的位置取决于你的操作系统，可在以下目录中找到：

- **全局：** 系统级配置文件，由所有用户共享。
- **用户：** 针对运行 pip 进程的用户设置的配置文件。
- **站点：** 使用 Python 虚拟环境时针对每个环境设置的配置文件。

在不同系统中，可以在以下位置找到或创建这些配置文件：

### Linux/macOS

在 Linux 和 macOS 上，pip 配置文件名为 `pip.conf`，可在以下位置找到：

- **全局：**
    - **基于 Debian 的系统：** 在 `/etc` 目录中编辑或创建 `pip.conf`。
    - **基于 macOS 的系统：** 编辑或创建 `/Library/Application Support/pip/pip.conf`。
- **用户：**
    - **基于 Debian 的系统：** 编辑或创建 `~/pip/pip.conf` 文件。
    - **基于 macOS 的系统：** 编辑或创建 `~/.config/pip/pip.conf` 配置文件。
- **站点：** 在 Python 虚拟环境中加载时，该文件位于 `$VIRTUAL_ENV/pip.conf`。

### Windows

在 Windows 系统上，该文件为 `pip.ini`，可在以下位置找到：

- **全局：** 编辑或创建 `C:\ProgramData\pip\pip.ini` 文件。请注意，此文件在 Windows 系统上默认隐藏，但可以写入。
- **用户：** 在 `%APPDATA%\pip\` 中编辑或创建 `pip.ini`。
- **站点：** 在 Python 虚拟环境中加载时，编辑或创建位于 `%VIRTUAL_ENV%\pip.ini` 的配置文件。

### 配置文件内容

此示例假设使用 Python 虚拟环境的 pip 配置文件。在已激活的虚拟环境中，基于 Debian 的系统需编辑 `$VIRTUAL_ENV/pip.conf`，Windows 系统则需编辑 `%VIRTUAL_ENV%\pip.ini`。

在配置文件中，你需要将 `proxy` 键更新为所需的 HTTP 或 HTTPS 代理：

```
[global]
proxy = http://45.185.162.203:999
```

保存文件后，代理将自动应用于所有 pip 命令，无需每次都指定代理标志：

```bash
(venv) $ pip install boto3
```

有关配置选项的更多详情，请参阅[项目文档](https://pip.pypa.io/en/stable/topics/configuration/)。

## 使用环境变量配置 pip 代理

配置系统环境变量可确保 pip 和系统上的所有其他 HTTP 请求都使用代理。为此，需要设置 `HTTP_PROXY` 和 `HTTPS_PROXY` 变量，包括 pip 在内的许多应用程序都会依赖这些变量，将其作为系统处理 HTTP 请求的默认代理。

### Linux/macOS

如果你使用 Linux 操作系统，请更新 `/etc/environment` 文件；如果你使用 macOS，请更新主目录中的 `.zshrc` 文件。然后，为代理服务器添加新条目：

```
HTTP_PROXY=https://proxyserver:port
HTTPS_PROXY=https://proxyserver:port
```

重启终端会话或系统，环境变量即可生效。

### Windows

在 Windows 系统上，可以在命令提示符终端中使用以下命令设置环境变量：

```
setx HTTP_PROXY "https://proxyserver:port" /M
setx HTTPS_PROXY "https://proxyserver:port" /M
```

重启命令提示符以使更改生效。

## 测试配置

使用 pip 配置文件或环境变量完成系统级配置后，请测试代理，确保它能够成功连接和传输数据。

### Linux/macOS

在 Linux/macOS 上，使用以下命令：

```bash
$ python -m venv venv
$ source venve/scripts/activate

# for pip config file or environment variables
(venv) $ pip install requests
```

如果你需要使用特定代理覆盖这些设置，可以改用 CLI 标志：

```bash
# pip cli flag
(venv) $ pip install requests --proxy https://proxyserver:port
```

在此命令中，请务必将 `https://proxyserver:port` 更新为你自己的代理。

### Windows

在 Windows 上，使用以下命令：

```powershell
> python -m venv venv
> .\venv\Scripts\Activate.bat
(venv) > pip install requests
```

你始终可以使用 pip CLI 标志覆盖这些设置：

```bash
# pip cli flag
(venv) $ pip install requests --proxy https://proxyserver:port
```

### pip 代理故障排除

通过 pip 连接 HTTP 或 HTTPS 代理时，你可能会遇到以下常见问题，尤其是在使用具有更多高级功能的私有代理或 HTTPS 代理时。

#### 身份验证问题

通过 pip 连接代理时，身份验证问题通常表现为 `407 Proxy Authentication Required` 错误。这表示代理需要用户名和密码才能连接，或者你提供了错误的代理凭据。

#### 证书问题

连接 HTTPS 代理时，你可能会收到 pip 返回的 `Certificate verify failed` 错误。这表示代理服务器提供的证书存在问题。

如果你的私有代理服务器使用自签名证书，由于该证书未经证书颁发机构验证，你可能会遇到错误。要绕过此问题，在连接特定域名时，可以考虑使用 `--trusted-host` CLI 选项忽略自签名证书错误。

## 通过动态代理使用 pip

[动态代理](https://www.bright.cn/solutions/rotating-proxies)会自动为每个请求切换 IP 地址，从而帮助避免 IP 被封禁。这可以模拟多个用户并绕过限制。

你可以从列表中随机选择代理来实现此功能。下面是一个简单的 bash 脚本，它会在轮换公共代理的同时安装 pip 软件包。

创建以下名为 `rotate-proxies.sh` 的 bash 脚本：

```bash
proxy_list=(
  'http://45.185.162.203:999'
  'http://177.23.176.58:8080'
  'http://83.143.24.66:80'
)

pip_packages=(
  'requests'
  'numpy'
  'pandas'
)

# Loop through packages and install them
for package in "${pip_packages[@]}"
do
  # Randomly select a proxy from the list
  proxy=${proxy_list[$RANDOM % ${#proxy_list[@]}]}
  echo -e  "\nInstalling $package with proxy $proxy"
  pip install --proxy $proxy $package
done
```

创建文件后，你可以运行它来下载 pip 软件包，并为每条 pip 命令随机轮换代理。以下是脚本输出的摘要：

```
$ ./rotate-proxies.sh 

Installing requests with proxy http://177.23.176.58:8080
Collecting requests
  Downloading requests-2.32.3-py3-none-any.whl.metadata (4.6 kB)

….

Downloading urllib3-2.3.0-py3-none-any.whl (128 kB)
Installing collected packages: urllib3, idna, charset-normalizer, certifi, requests
Successfully installed certifi-2025.1.31 charset-normalizer-3.4.1 idna-3.10 requests-2.32.3 urllib3-2.3.0

Installing six with proxy http://45.185.162.203:999
Collecting numpy
 Downloading numpy-2.2.2-cp313-cp313-macosx_14_0_x86_64.whl.metadata (62 kB)

…

Installing collected packages: numpy
Successfully installed numpy-2.2.2

Installing pandas with proxy http://83.143.24.66:80
Collecting pandas
  Downloading pandas-2.2.3-cp313-cp313-macosx_10_13_x86_64.whl.metadata (89 kB)

….

Installing collected packages: pytz, tzdata, six, python-dateutil, pandas
Successfully installed pandas-2.2.3 python-dateutil-2.9.0.post0 pytz-2025.1 six-1.17.0 tzdata-2025.1
```

## 通过代理使用 pip 的优势

代理可帮助开发者绕过网络限制、访问被屏蔽的资源并优化软件下载速度。私有代理通过隐藏身份来增强安全性，同时还可提供缓存和更快的连接。

[与 VPN 不同](https://brightdata.com/blog/proxy-101/vpn-vs-proxy)，VPN 会加密所有互联网流量以增强隐私保护，但也可能增加延迟，而代理则是处理 pip 请求的轻量级替代方案。它们能以更快、更高效的方式管理依赖项，且不会产生 VPN 带来的性能开销。

## 常见错误与最佳实践

通过代理使用 pip 时，务必避免可能导致安全漏洞的常见错误。代理 URL 不正确或地址格式不规范（例如缺少 HTTP/HTTPS 协议或协议错误）等问题，都可能中断与软件包仓库的连接。

一个主要安全风险是在源代码、脚本或 CI/CD 流水线配置中硬编码代理凭据（例如用户名和密码）。如果代码被共享或泄露，可能会导致代理遭到未经授权的访问。凭据泄露可能引发滥用，导致成本增加，甚至可能被用于网络攻击。

为避免这些错误，建议不要将代理凭据直接写入代码，而应将其存储在环境变量或加密配置文件中，以确保安全。此外，在使用 pip 之前，你必须测试代理连接，确保配置正确并避免运行时错误。借助 [curl](https://curl.se/) 或 [ping](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/ping) 等工具，你可以在正式使用代理之前验证其性能，从而获得更顺畅的软件包管理体验。

## 使用 Bright Data 代理

Bright Data 提供多种[代理服务](https://www.bright.cn/proxy-types)，包括住宅代理和数据中心代理。它可以帮助你根据项目需求轻松创建代理。下面创建一个可与 pip 搭配使用的私有[住宅代理](https://www.bright.cn/proxy-types/residential-proxies)，以便通过其他 IP 地址访问软件包。

首先注册一个免费的 Bright Data 账户，然后前往[用户控制面板](https://www.bright.cn/cp/start/)。

在侧边菜单中，点击 **代理与抓取**：

![Bright Data 代理](https://github.com/bright-cn/pip-with-proxy/blob/main/Images/Bright-Data-proxies-2048x1041.png)

表单加载后，设置新的住宅代理。使用默认设置会为你分配一个由多个 Bright Data 用户共享的 IP 地址。

![创建住宅代理](https://github.com/bright-cn/pip-with-proxy/blob/main/Images/Creating-a-residential-proxy-2048x1040.png)

如果你需要特定地区的 IP 地址，可以在设置过程中选择所需国家/地区。

创建代理后，系统会将你重定向到显示端点和身份验证详情的控制面板。请务必记下用户名、密码和服务器地址。

![代理控制面板](https://github.com/bright-cn/pip-with-proxy/blob/main/Images/Proxy-dashboard-2048x1040.png)

使用 `--proxy` 标志测试这些端点值是否可用：

```bash
$ pip install pandas \
    --trusted-host pypi.org \
    --trusted-host files.pythonhosted.org \
    --proxy https://username:password@brd.superproxy.io:33335
```

由于 Bright Data 代理使用自签名证书，你可以使用 `trusted-host` 标志，将 `pypi.org` 和 `files.pythonhosted.org` 加入受信任域名白名单。

## 总结

为 pip 配置代理很简单，可以使用 CLI 标志、pip 配置文件和环境变量等多种方式。不过，公共代理存在局限性，并不适合大型工作负载或生产环境。若需要更可靠的解决方案，Bright Data 可提供住宅 IP 和[数据中心 IP](https://www.bright.cn/proxy-types/datacenter-proxies)，以及快速、稳定的连接和用于网页爬虫与数据采集的高级工具。免费注册即可开始使用。
