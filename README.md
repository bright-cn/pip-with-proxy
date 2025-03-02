# 使用 pip 搭配代理

[![推广横幅](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://www.bright.cn/)

本指南将向您说明如何在 pip 中设置代理，从而绕过网络限制、提升安全性并简化包管理流程：

- [公共代理 vs 私人代理](#公共代理-vs-私人代理)
- [在 pip 中使用代理](#在-pip-中使用代理)
- [通过命令行配置 pip 代理](#通过命令行配置-pip-代理)
- [通过 pip 配置文件配置 pip 代理](#通过-pip-配置文件配置-pip-代理)
- [通过环境变量配置 pip 代理](#通过环境变量配置-pip-代理)
- [测试配置](#测试配置)
- [pip 代理故障排查](#pip-代理故障排查)
- [使用 pip 搭配旋转代理](#使用-pip-搭配旋转代理)
- [使用代理与 pip 的好处](#使用代理与-pip-的好处)
- [常见错误与最佳实践](#常见错误与最佳实践)
- [使用 Bright Data 代理](#使用-bright-data-代理)
- [结论](#结论)

## 公共代理 vs 私人代理

### 公共代理

公共代理对所有人开放使用，通常不需要认证。尽管它提供了快速更换 IP 地址的方法，但也拥有明显的缺点，例如速度较慢、连接不稳定以及更易被封禁。由于免费且随处可见，公共代理往往缺少关键功能，例如代理轮换、缓存及访问控制，因此并不适合在生产环境中可靠地使用。

公共代理的 URL 一般形如：`https://proxyserver:port`。

### 私人代理

[私人代理](https://www.bright.cn/solutions/private-proxies)需要进行身份验证，能提供更高的安全性、更稳定的连接以及高级功能，通常需要付费。它们能够为您提供快速、可靠的 [专用 IP 地址](https://www.bright.cn/solutions/dedicated-proxies)，并包含身份验证与代理轮换等功能，让您获得更好的性能与可控性。

通常通过在代理 URL 之前加入用户名与密码控制访问，例如： `https://username:password@proxyserver:port`。

## 在 pip 中使用代理

在通过 pip 使用代理之前，您需要了解代理的相关信息。下面示例展示了如何使用一个公共代理：

- 代理服务的 **代理地址**  
- 代理服务所需的 **端口**

此 [“proxy-list” 仓库](https://github.com/clarketm/proxy-list) 提供了每日测试过的公共代理 IP 地址，可用于测试，但不建议在生产环境使用。

在 `proxy-list` 仓库中，查看 [`proxy-list-status.txt`](https://github.com/clarketm/proxy-list/blob/master/proxy-list-status.txt)，找出一个可用的公共代理。只要在文件里查找带有 `success` 标志的地址即可，表示它可正常工作：

![选择公共代理](https://github.com/luminati-io/pip-with-proxy/blob/main/Images/Selecting-a-public-proxy.png)

本教程中，我们将使用 `45.185.162.203:999` 作为公共代理地址。换言之，代理服务器地址即为 `http://45.185.162.203:999`。

## 通过命令行配置 pip 代理

配置 pip 代理的最快方式，是在使用 `pip install` 命令时，通过 `--proxy` 命令行选项传入代理地址。

想要验证通过公共代理访问并测试下载包功能，可执行以下命令：

```
# 公共代理
pip install boto3 --proxy http://45.185.162.203:999
```

这种做法非常适合快速测试和验证代理的可用性，而无需进行永久配置。对于需要发布 pip 包的用户来说，它也能帮助您确认从不同 IP 地址的访问可用性。

## 通过 pip 配置文件配置 pip 代理

若想要长期使用某个代理，编辑 pip 配置文件是简单且更具声明性的方法。根据操作系统的不同，pip 配置文件可能位于不同的位置：

- **Global（全局配置文件）：** 系统范围内所有用户共享。
- **User（用户配置文件）：** 当前用户的单独配置文件。
- **Site（环境级配置文件）：** 在 Python 虚拟环境中使用的配置文件。

在不同系统下，这些配置文件可能存在或需要创建于如下路径：

### Linux/macOS

Linux 与 macOS 上的 pip 配置文件名为 `pip.conf`，可能位于以下位置：

- **Global（全局）:**
  - **基于 Debian 的系统：** 编辑或在 `/etc` 目录创建 `pip.conf`。
  - **macOS 系统：** 编辑或在 `/Library/Application Support/pip/pip.conf` 创建该文件。
- **User（用户）:**
  - **基于 Debian 的系统：** 编辑或在 `~/pip/pip.conf` 文件中创建。
  - **macOS 系统：** 编辑或在 `~/.config/pip/pip.conf` 中创建该文件。
- **Site（环境级）:** 在加载 Python 虚拟环境后，该文件位于 `$VIRTUAL_ENV/pip.conf`。

### Windows

在 Windows 系统上，文件名为 `pip.ini`，位于下列路径：

- **Global（全局）:** 编辑或创建 `C:\ProgramData\pip\pip.ini` 文件。注意该文件默认隐藏，但可以写入。  
- **User（用户）:** 在 `%APPDATA%\pip\` 下编辑或创建 `pip.ini`。  
- **Site（环境级）:** 加载 Python 虚拟环境后，在 `%VIRTUAL_ENV%\pip.ini` 编辑或创建此配置文件。

### 配置文件内容

以下示例假设使用 Python 虚拟环境的 pip 配置文件。激活虚拟环境后，在 Debian 系统里编辑 `$VIRTUAL_ENV/pip.conf`，或者在 Windows 系统里编辑 `%VIRTUAL_ENV%\pip.ini`。

在配置文件中，更新 `proxy` 项，将其指向您希望使用的 HTTP 或 HTTPS 代理：

```
[global]
proxy = http://45.185.162.203:999
```

保存后，您在该虚拟环境里执行所有 pip 命令时会自动使用此代理，无需每次都指定：

```bash
(venv) $ pip install boto3
```

如需了解更多配置选项，详见 [pip 官方文档](https://pip.pypa.io/en/stable/topics/configuration/)。

## 通过环境变量配置 pip 代理

通过在系统中设置环境变量，您不仅能让 pip，也能让其他所有 HTTP 客户端默认使用指定的代理。通常只需设置 `HTTP_PROXY` 与 `HTTPS_PROXY` 变量，许多应用（包含 pip）在发出网络请求时会优先使用此配置。

### Linux/macOS

如果使用 Linux，可更新 `/etc/environment` 文件；若使用 macOS，可更新位于 Home 目录中的 `.zshrc` 文件，添加如下条目：

```
HTTP_PROXY=https://proxyserver:port
HTTPS_PROXY=https://proxyserver:port
```

重新启动终端会话或系统后，这些环境变量便会生效。

### Windows

在 Windows 系统里，可通过命令行如下设置环境变量（以管理员身份运行 CMD）：

```
setx HTTP_PROXY "https://proxyserver:port" /M
setx HTTPS_PROXY "https://proxyserver:port" /M
```

请重启命令提示符后再进行测试，使新设置生效。

## 测试配置

通过 pip 配置文件或系统环境变量这两种方法配置完代理后，您需要进行测试以确定代理可以正常联网并传输数据。

### Linux/macOS

在 Linux/macOS 系统下，示例命令如下：

```bash
$ python -m venv venv
$ source venv/scripts/activate

# 测试（通过 pip 配置文件或环境变量）
(venv) $ pip install requests
```

若您想临时覆盖上述代理设置，也可以在命令行中指定可用的代理：

```bash
# 使用 pip 命令行参数覆盖
(venv) $ pip install requests --proxy https://proxyserver:port
```

在此命令里，请将 `https://proxyserver:port` 替换为您的实际代理。

### Windows

在 Windows 上，示例如下：

```powershell
> python -m venv venv
> .\venv\Scripts\Activate.bat
(venv) > pip install requests
```

同样，可以使用 pip 命令行参数覆盖之前的配置：

```bash
# 使用 pip 命令行参数覆盖
(venv) $ pip install requests --proxy https://proxyserver:port
```

## pip 代理故障排查

在通过 HTTP 或 HTTPS 代理连接 pip 时，尤其是使用私有或 HTTPS 代理时，可能会遇到下列常见问题。

### 身份验证问题

当 pip 在连接代理时需要用户名与密码，而提供的凭据不正确或没有提供时，会出现 `407 Proxy Authentication Required` 错误。这意味着代理要求提供正确的身份验证信息才能成功连接。

### 证书问题

连接到 HTTPS 代理时，pip 可能会给出 `Certificate verify failed` 错误。这说明代理提供的证书有问题。

如果您的私有代理服务器使用自签名证书，pip 会因为证书未被权威机构认证而报错。要绕过此问题，可以在命令行使用 `--trusted-host` 参数，对指定域名跳过证书验证。

## 使用 pip 搭配旋转代理

[旋转代理](https://www.bright.cn/solutions/rotating-proxies)会在每次发起请求时自动更换 IP 地址，从而有效地避免 IP 被封禁，模拟多个用户的流量访问。

您可以在脚本中随机选择不同的代理来实现此功能。下面是一个简单的 Bash 脚本示例，用于在安装 pip 包的过程中依次切换公共代理。

新增名为 `rotate-proxies.sh` 的脚本文件：

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

# 依次遍历要安装的包
for package in "${pip_packages[@]}"
do
  # 从列表里随机选择一个代理
  proxy=${proxy_list[$RANDOM % ${#proxy_list[@]}]}
  echo -e  "\n正在使用代理 $proxy 安装 $package"
  pip install --proxy $proxy $package
done
```

保存文件后执行它，每次 `pip install` 时都会随机更换一个代理。下面是脚本输出的简要示例：

```
$ ./rotate-proxies.sh 

正在使用代理 http://177.23.176.58:8080 安装 requests
Collecting requests
  Downloading requests-2.32.3-py3-none-any.whl.metadata (4.6 kB)

….

Downloading urllib3-2.3.0-py3-none-any.whl (128 kB)
Installing collected packages: urllib3, idna, charset-normalizer, certifi, requests
Successfully installed certifi-2025.1.31 charset-normalizer-3.4.1 idna-3.10 requests-2.32.3 urllib3-2.3.0

正在使用代理 http://45.185.162.203:999 安装 numpy
Collecting numpy
  Downloading numpy-2.2.2-cp313-cp313-macosx_14_0_x86_64.whl.metadata (62 kB)

…

Installing collected packages: numpy
Successfully installed numpy-2.2.2

正在使用代理 http://83.143.24.66:80 安装 pandas
Collecting pandas
  Downloading pandas-2.2.3-cp313-cp313-macosx_10_13_x86_64.whl.metadata (89 kB)

….

Installing collected packages: pytz, tzdata, six, python-dateutil, pandas
Successfully installed pandas-2.2.3 python-dateutil-2.9.0.post0 pytz-2025.1 six-1.17.0 tzdata-2025.1
```

## 使用代理与 pip 的好处

代理有助于开发者绕过网络限制、访问受限资源并加快包下载速度。私人代理通过隐藏真实 IP 提高了安全性，同时往往能提供缓存功能及更快的连接速度。

与 [VPN](https://www.bright.cn/blog/proxy-101/vpn-vs-proxy)（加密所有流量，从而提高隐私但可能会带来较高延迟）相比，使用代理会显得更轻量并且性能开销更低。对于仅需要管理 pip 依赖的场景，代理能带来更快更高效的体验。

## 常见错误与最佳实践

在 pip 中使用代理时，需注意一些常见错误，否则可能带来安全隐患。例如，使用错误的代理 URL 或格式不正确（比如缺少协议 `http://` 或 `https://`）会导致无法连接到包仓库。

另一个较大的安全风险是将代理凭据（例如用户名与密码）硬编码于源代码、脚本或 CI/CD 配置中。一旦代码被共享或泄露，就会导致代理被滥用，带来额外费用或被黑客利用的风险。

为避免以上问题，建议通过环境变量或加密配置文件来存放代理认证信息，而不是直接写进代码里。此外，在 pip 使用前，可借助 [curl](https://curl.se/) 或 [ping](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/ping) 等工具测试代理连通性，防止在实际使用时出错。这能为包管理带来更流畅的使用体验。

## 使用 Bright Data 代理

Bright Data 提供多种[代理服务](https://www.bright.cn/proxy-types)，包括住宅、数据中心以及移动端代理，能帮助您为各种项目需求轻松创建代理。下面以创建一个私有 [住宅代理](https://www.bright.cn/proxy-types/residential-proxies) 的示例说明如何在 pip 中使用不同的 IP 地址来安装包。

首先，注册一个免费的 Bright Data 账号，然后进入 [用户面板](https://www.bright.cn/cp/start/)。

在左侧菜单，点击“Proxies & Scraping”：

![Bright Data 代理](https://github.com/bright-cn/pip-with-proxy/blob/main/Images/Bright-Data-proxies-2048x1041.png)

加载表单后，设置一个新的住宅代理。使用默认设置时，您将获得一个共享 IP 地址（与其他 Bright Data 用户共享）。

![创建住宅代理](https://github.com/bright-cn/pip-with-proxy/blob/main/Images/Creating-a-residential-proxy-2048x1040.png)

如果您需要特定地区的 IP 地址，可在设置时选择相应的国家或地区。

代理创建完成后，系统会转到一个显示端点及认证信息的页面，您需要记录其中的用户名、密码和服务器地址。

![代理仪表板](https://github.com/bright-cn/pip-with-proxy/blob/main/Images/Proxy-dashboard-2048x1040.png)

接着，使用下列命令测试该代理是否能正常下载：

```bash
$ pip install pandas \
    --trusted-host pypi.org \
    --trusted-host files.pythonhosted.org \
    --proxy https://username:[email protected]:33335
```

由于 Bright Data 代理使用的是自签名证书，您可通过 `--trusted-host` 选项把 `pypi.org` 与 `files.pythonhosted.org` 加入信任列表，避开由于证书问题导致的错误。

## 结论

为 pip 配置代理非常简单，您可以使用命令行参数、pip 配置文件或环境变量等多种方法。不过，公共代理局限性大，不适合大型工作负载或生产场景。如果您需要可靠性更高的解决方案，Bright Data 提供住宅与[数据中心 IP](https://www.bright.cn/proxy-types/datacenter-proxies)，带来快速、稳定的连接以及用于网络爬取和数据收集的高级工具。现在就免费注册，体验这些服务吧。
