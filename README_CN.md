<img src="static/logo.png#gh-light-mode-only" alt="dirsearch logo (light)" width="675px">
<img src="static/logo-dark.png#gh-dark-mode-only" alt="dirsearch logo (dark)" width="675px">

dirsearch - Web路径发现工具
=========

![Build](https://img.shields.io/badge/Built%20with-Python-Blue)
![License](https://img.shields.io/badge/license-GNU_General_Public_License-_red.svg)
![Stars](https://img.shields.io/github/stars/maurosoria/dirsearch.svg)
[![Release](https://img.shields.io/github/release/maurosoria/dirsearch.svg)](https://github.com/maurosoria/dirsearch/releases)
[![Sponsors](https://img.shields.io/github/sponsors/maurosoria)](https://github.com/sponsors/maurosoria)
[![Discord](https://img.shields.io/discord/992276296669339678.svg?logo=discord)](https://discord.gg/2N22ZdAJRj)
[![Twitter](https://img.shields.io/twitter/follow/_dirsearch?label=Follow)](https://twitter.com/_dirsearch)


> 一个高级的Web路径暴力破解工具

**dirsearch** 由 [@maurosoria](https://twitter.com/_maurosoria) 和 [@shelld3v](https://twitter.com/shells3c_) 积极开发

*加入我们的 [Discord服务器](https://discord.gg/2N22ZdAJRj) 与团队进行最佳沟通*


目录
------------
* [安装](#安装--使用)
* [字典文件](#字典文件重要)
* [选项](#选项)
* [配置](#配置)
* [使用方法](#使用方法)
  * [简单使用](#简单使用)
  * [暂停进度](#暂停进度)
  * [递归](#递归)
  * [线程](#线程)
  * [异步](#异步)
  * [前缀后缀](#前缀--后缀)
  * [黑名单](#黑名单)
  * [过滤器](#过滤器)
  * [原始请求](#原始请求)
  * [字典格式](#字典格式)
  * [排除扩展名](#排除扩展名)
  * [扫描子目录](#扫描子目录)
  * [代理](#代理)
  * [报告](#报告)
  * [更多示例命令](#更多示例命令)
* [Docker支持](#docker支持)
  * [Linux安装Docker](#linux安装docker)
  * [构建dirsearch镜像](#构建dirsearch镜像)
  * [使用dirsearch](#使用dirsearch)
* [参考资料](#参考资料)
* [技巧](#技巧)
* [贡献](#贡献)
* [许可证](#许可证)


安装 & 使用
------------

**要求: python 3.9 或更高版本**

选择以下安装选项之一:

- 使用 **git** 安装: `git clone https://github.com/maurosoria/dirsearch.git --depth 1` (**推荐**)
- 使用ZIP文件安装: [在此下载](https://github.com/maurosoria/dirsearch/archive/master.zip)
- 使用Docker安装: `docker build -t "dirsearch:v0.4.3" .` (更多信息请查看[这里](https://github.com/maurosoria/dirsearch#docker支持))
- 使用PyPi安装: `pip3 install dirsearch` 或 `pip install dirsearch`
- 使用Kali Linux安装: `sudo apt-get install dirsearch` (已弃用)


字典文件 (重要)
---------------
**总结:**
  - 字典文件是一个文本文件，每行是一个路径。
  - 关于扩展名，与其他工具不同，dirsearch只将 `%EXT%` 关键字替换为 **-e** 标志中的扩展名。
  - 对于没有 `%EXT%` 的字典文件（如 [SecLists](https://github.com/danielmiessler/SecLists)），需要 **-f | --force-extensions** 开关来为字典中的每个词添加扩展名，以及 `/`。
  - 要将您的扩展名应用到已有扩展名的字典条目，请使用 **-O** | **--overwrite-extensions** (注意: 某些扩展名被排除在覆盖之外，如 *.log*, *.json*, *.xml*, ... 或媒体扩展名如 *.jpg*, *.png*)
  - 要使用多个字典文件，您可以用逗号分隔字典文件。示例: `wordlist1.txt,wordlist2.txt`。

**示例:**

- *普通扩展名*:
```
index.%EXT%
```

传递 **asp** 和 **aspx** 作为扩展名将生成以下字典:

```
index
index.asp
index.aspx
```

- *强制扩展名*:
```
admin
```

传递 **php** 和 **html** 作为扩展名并使用 **-f**/**--force-extensions** 标志将生成以下字典:

```
admin
admin.php
admin.html
admin/
```

- *覆盖扩展名*:
```
login.html
```

传递 **jsp** 和 **jspa** 作为扩展名并使用 **-O**/**--overwrite-extensions** 标志将生成以下字典:

```
login.html
login.jsp
login.jspa
```


选项
-------

```
用法: dirsearch.py [-u|--url] target [-e|--extensions] extensions [options]

选项:
  --version             显示程序版本号并退出
  -h, --help            显示此帮助信息并退出

  必需参数:
    -u URL, --url=URL   目标URL(s)，可以使用多个标志
    -l PATH, --urls-file=PATH
                        URL列表文件
    --stdin             从STDIN读取URL(s)
    --cidr=CIDR         目标CIDR
    --raw=PATH          从文件加载原始HTTP请求 (使用 '--scheme' 标志
                        设置协议)
    --nmap-report=PATH  从nmap报告加载目标 (确保在nmap扫描中包含
                        -sV标志以获得全面结果)
    -s SESSION_FILE, --session=SESSION_FILE
                        会话文件
    --config=PATH       配置文件路径 (默认: 'DIRSEARCH_CONFIG' 环境变量，
                        否则为 'config.ini')

  字典设置:
    -w WORDLISTS, --wordlists=WORDLISTS
                        字典文件或包含字典的目录 (用逗号分隔)
    -e EXTENSIONS, --extensions=EXTENSIONS
                        扩展名列表，用逗号分隔 (例如 php,asp)
    -f, --force-extensions
                        为每个字典条目末尾添加扩展名。默认情况下
                        dirsearch只将 %EXT% 关键字替换为扩展名
    -O, --overwrite-extensions
                        用您的扩展名覆盖字典中的其他扩展名 (通过 `-e` 选择)
    --exclude-extensions=EXTENSIONS
                        排除扩展名列表，用逗号分隔 (例如 asp,jsp)
    --remove-extensions
                        移除所有路径中的扩展名 (例如 admin.php -> admin)
    --prefixes=PREFIXES
                        为所有字典条目添加自定义前缀 (用逗号分隔)
    --suffixes=SUFFIXES
                        为所有字典条目添加自定义后缀，忽略目录 (用逗号分隔)
    -U, --uppercase     大写字典
    -L, --lowercase     小写字典
    -C, --capital       首字母大写字典

  常规设置:
    -t THREADS, --threads=THREADS
                        线程数
    --async             启用异步模式
    -r, --recursive     递归暴力破解
    --deep-recursive    对每个目录深度执行递归扫描 (例如
                        api/users -> api/)
    --force-recursive   对所有找到的路径进行递归暴力破解，不仅仅是
                        以 `/` 结尾的路径
    -R DEPTH, --max-recursion-depth=DEPTH
                        最大递归深度
    --recursion-status=CODES
                        执行递归扫描的有效状态码，支持范围 (用逗号分隔)
    --subdirs=SUBDIRS   扫描给定URL[s]的子目录 (用逗号分隔)
    --exclude-subdirs=SUBDIRS
                        在递归扫描期间排除以下子目录 (用逗号分隔)
    -i CODES, --include-status=CODES
                        包含状态码，用逗号分隔，支持范围 (例如 200,300-399)
    -x CODES, --exclude-status=CODES
                        排除状态码，用逗号分隔，支持范围 (例如 301,500-599)
    --exclude-sizes=SIZES
                        按大小排除响应，用逗号分隔 (例如 0B,4KB)
    --exclude-text=TEXTS
                        按文本排除响应，可以使用多个标志
    --exclude-regex=REGEX
                        按正则表达式排除响应
    --exclude-redirect=STRING
                        如果此正则表达式 (或文本) 匹配重定向URL则排除响应
                        (例如 '/index.html')
    --exclude-response=PATH
                        排除与此页面响应相似的响应，路径作为输入
                        (例如 404.html)
    --skip-on-status=CODES
                        每当命中这些状态码之一时跳过目标，用逗号分隔，
                        支持范围
    --min-response-size=LENGTH
                        最小响应长度
    --max-response-size=LENGTH
                        最大响应长度
    --max-time=SECONDS  扫描的最大运行时间
    --exit-on-error     每当发生错误时退出

  请求设置:
    -m METHOD, --http-method=METHOD
                        HTTP方法 (默认: GET)
    -d DATA, --data=DATA
                        HTTP请求数据
    --data-file=PATH    包含HTTP请求数据的文件
    -H HEADERS, --header=HEADERS
                        HTTP请求头，可以使用多个标志
    --headers-file=PATH
                        包含HTTP请求头的文件
    -F, --follow-redirects
                        跟随HTTP重定向
    --random-agent      为每个请求选择随机User-Agent
    --auth=CREDENTIAL   认证凭据 (例如 user:password 或 bearer token)
    --auth-type=TYPE    认证类型 (basic, digest, bearer, ntlm, jwt)
    --cert-file=PATH    包含客户端证书的文件
    --key-file=PATH     包含客户端证书私钥的文件 (未加密)
    --user-agent=USER_AGENT
    --cookie=COOKIE

  连接设置:
    --timeout=TIMEOUT   连接超时
    --delay=DELAY       请求之间的延迟
    -p PROXY, --proxy=PROXY
                        代理URL (HTTP/SOCKS)，可以使用多个标志
    --proxies-file=PATH
                        包含代理服务器的文件
    --proxy-auth=CREDENTIAL
                        代理认证凭据
    --replay-proxy=PROXY
                        用于重放找到路径的代理
    --tor               使用Tor网络作为代理
    --scheme=SCHEME     原始请求的协议或URL中没有协议时的协议
                        (默认: 自动检测)
    --max-rate=RATE     每秒最大请求数
    --retries=RETRIES   失败请求的重试次数
    --ip=IP             服务器IP地址
    --interface=NETWORK_INTERFACE
                        要使用的网络接口

  高级设置:
    --crawl             在响应中爬取新路径

  视图设置:
    --full-url          输出中的完整URL (在安静模式下自动启用)
    --redirects-history
                        显示重定向历史
    --no-color          无彩色输出
    -q, --quiet-mode    安静模式

  输出设置:
    -o PATH/URL, --output=PATH/URL
                        输出文件或MySQL/PostgreSQL URL (格式:
                        scheme://[username:password@]host[:port]/database-name)
    --format=FORMAT     报告格式 (可用: simple, plain, json, xml,
                        md, csv, html, sqlite, mysql, postgresql)
    --log=PATH          日志文件
```


配置
---------------

默认情况下，使用dirsearch目录内的 `config.ini` 作为配置文件，但您可以通过 `--config` 标志或 `DIRSEARCH_CONFIG` 环境变量选择另一个文件。

```ini
# 如果您想编辑dirsearch默认配置，可以
# 编辑此文件中的值。`#` 之后的所有内容都是注释
# 不会被应用

[general]
threads = 25
async = False
recursive = False
deep-recursive = False
force-recursive = False
recursion-status = 200-399,401,403
max-recursion-depth = 0
exclude-subdirs = %%ff/,.;/,..;/,;/,./,../,%%2e/,%%2e%%2e/
random-user-agents = False
max-time = 0
exit-on-error = False
# subdirs = /,api/
# include-status = 200-299,401
# exclude-status = 400,500-999
# exclude-sizes = 0b,123gb
# exclude-text = "Not found"
# exclude-regex = "^403$"
# exclude-redirect = "*/error.html"
# exclude-response = 404.html
# skip-on-status = 429,999

[dictionary]
default-extensions = php,aspx,jsp,html,js
force-extensions = False
overwrite-extensions = False
lowercase = False
uppercase = False
capitalization = False
# exclude-extensions = old,log
# prefixes = .,admin
# suffixes = ~,.bak
# wordlists = /path/to/wordlist1.txt,/path/to/wordlist2.txt

[request]
http-method = get
follow-redirects = False
# headers-file = /path/to/headers.txt
# user-agent = MyUserAgent
# cookie = SESSIONID=123

[connection]
timeout = 7.5
delay = 0
max-rate = 0
max-retries = 1
## 通过禁用 `scheme` 变量，dirsearch将自动识别URI协议
# scheme = http
# proxy = localhost:8080
# proxy-file = /path/to/proxies.txt
# replay-proxy = localhost:8000

[advanced]
crawl = False

[view]
full-url = False
quiet-mode = False
color = True
show-redirects-history = False

[output]
## 支持: plain, simple, json, xml, md, csv, html, sqlite
report-format = plain
autosave-report = True
autosave-report-folder = reports/
# log-file = /path/to/dirsearch.log
# log-file-size = 50000000
```


使用方法
---------------

[![Dirsearch演示](https://asciinema.org/a/380112.svg)](https://asciinema.org/a/380112)

一些使用dirsearch的示例 - 这些是最常用的参数。如果您需要所有参数，只需使用 **-h** 参数。

### 简单使用
```
python3 dirsearch.py -u https://target
```

```
python3 dirsearch.py -e php,html,js -u https://target
```

```
python3 dirsearch.py -e php,html,js -u https://target -w /path/to/wordlist
```

---
### 暂停进度
dirsearch允许您使用CTRL+C暂停扫描进度，从这里您可以保存进度（稍后继续）、跳过当前目标或跳过当前子目录。

<img src="static/pause.png" alt="暂停dirsearch" width="475px">

----
### 递归
- 递归暴力破解是持续对找到的目录之后进行暴力破解。例如，如果dirsearch找到 `admin/`，它将暴力破解 `admin/*`（`*` 是它暴力破解的地方）。要启用此功能，请使用 **-r** (或 **--recursive**) 标志

```
python3 dirsearch.py -e php,html,js -u https://target -r
```
- 您可以使用 **--max-recursion-depth** 设置最大递归深度，使用 **--recursion-status** 设置要递归的状态码

```
python3 dirsearch.py -e php,html,js -u https://target -r --max-recursion-depth 3 --recursion-status 200-399
```
- 还有2个选项: **--force-recursive** 和 **--deep-recursive**
  - **强制递归**: 对所有找到的路径进行递归暴力破解，不仅仅是以 `/` 结尾的路径
  - **深度递归**: 对路径的所有深度进行递归暴力破解 (`a/b/c` => 添加 `a/`, `a/b/`)

- 如果有您不想递归暴力破解的子目录，请使用 `--exclude-subdirs`

```
python3 dirsearch.py -e php,html,js -u https://target -r --exclude-subdirs image/,media/,css/
```

----
### 线程
线程数 (**-t | --threads**) 反映了分离的暴力破解进程的数量。因此线程数越大，dirsearch运行得越快。默认情况下，线程数为25，但如果您想加快进度，可以增加它。

尽管如此，速度仍然很大程度上取决于服务器的响应时间。作为警告，我们建议您不要将线程数设置得太大，因为它可能导致DoS（拒绝服务）。

```
python3 dirsearch.py -e php,htm,js,bak,zip,tgz,txt -u https://target -t 20
```

----
### 异步
您可以通过 `--async` 切换到异步模式，让dirsearch使用协程而不是线程来处理并发请求。

理论上，异步模式提供更好的性能和更低的CPU使用率，因为它不需要在不同线程上下文之间切换。此外，按CTRL+C将立即暂停进度，无需等待线程挂起。

----
### 前缀 / 后缀
- **--prefixes**: 为所有条目添加自定义前缀

```
python3 dirsearch.py -e php -u https://target --prefixes .,admin,_
```
字典:

```
tools
```
使用前缀生成:

```
tools
.tools
admintools
_tools
```

- **--suffixes**: 为所有条目添加自定义后缀

```
python3 dirsearch.py -e php -u https://target --suffixes ~
```
字典:

```
index.php
internal
```
使用后缀生成:

```
index.php
internal
index.php~
internal~
```

----
### 黑名单
在 `db/` 文件夹内，有几个"黑名单文件"。如果这些文件中的路径具有文件名中提到的相同状态，它们将从扫描结果中被过滤。

示例: 如果您将 `admin.php` 添加到 `db/403_blacklist.txt` 中，每当您进行扫描时 `admin.php` 返回403，它将被从结果中过滤。

----
### 过滤器
使用 **-i | --include-status** 和 **-x | --exclude-status** 来选择允许和不允许的响应状态码

对于更高级的过滤器: **--exclude-sizes**, **--exclude-texts**, **--exclude-regexps**, **--exclude-redirects** 和 **--exclude-response**

```
python3 dirsearch.py -e php,html,js -u https://target --exclude-sizes 1B,243KB
```

```
python3 dirsearch.py -e php,html,js -u https://target --exclude-texts "403 Forbidden"
```

```
python3 dirsearch.py -e php,html,js -u https://target --exclude-regexps "^Error$"
```

```
python3 dirsearch.py -e php,html,js -u https://target --exclude-redirects "https://(.*).okta.com/*"
```

```
python3 dirsearch.py -e php,html,js -u https://target --exclude-response /error.html
```

----
### 原始请求
dirsearch允许您从文件导入原始请求。内容看起来像这样:

```http
GET /admin HTTP/1.1
Host: admin.example.com
Cache-Control: max-age=0
Accept: */*
```

由于dirsearch无法知道URI协议是什么，您需要使用 `--scheme` 标志设置它。默认情况下，dirsearch自动检测协议。

----
### 字典格式
支持的字典格式: 大写、小写、首字母大写

#### 小写:

```
admin
index.html
```
#### 大写:

```
ADMIN
INDEX.HTML
```
#### 首字母大写:

```
Admin
Index.html
```

----
### 排除扩展名
使用 **-X | --exclude-extensions** 和扩展名列表将移除字典中包含给定扩展名的所有路径

`python3 dirsearch.py -u https://target -X jsp`

字典:

```
admin.php
test.jsp
```
之后:

```
admin.php
```

----
### 扫描子目录
- 从URL，您可以使用 **--subdirs** 扫描子目录列表。

```
python3 dirsearch.py -e php,html,js -u https://target --subdirs /,admin/,folder/
```

----
### 代理
dirsearch支持SOCKS和HTTP代理，有两个选项: 代理服务器或代理服务器列表。

```
python3 dirsearch.py -e php,html,js -u https://target --proxy 127.0.0.1:8080
```

```
python3 dirsearch.py -e php,html,js -u https://target --proxy socks5://10.10.0.1:8080
```

```
python3 dirsearch.py -e php,html,js -u https://target --proxylist proxyservers.txt
```

----
### 报告
支持的报告格式: **simple**, **plain**, **json**, **xml**, **md**, **csv**, **html**, **sqlite**, **mysql**, **postgresql**

```
python3 dirsearch.py -e php -l URLs.txt --format plain -o report.txt
```

```
python3 dirsearch.py -e php -u https://target --format html -o target.json
```

----
### 更多示例命令
```
cat urls.txt | python3 dirsearch.py --stdin
```

```
python3 dirsearch.py -u https://target --max-time 360
```

```
python3 dirsearch.py -u https://target --auth admin:pass --auth-type basic
```

```
python3 dirsearch.py -u https://target --header-list rate-limit-bypasses.txt
```

**还有更多要发现的，自己试试吧！**


Docker支持
---------------
### Linux安装Docker
安装Docker

```sh
curl -fsSL https://get.docker.com | bash
```

> 要使用docker您需要超级用户权限

### 构建dirsearch镜像
创建镜像

```sh
docker build -t "dirsearch:v0.4.3" .
```

> **dirsearch** 是镜像名称，**v0.4.3** 是版本

### 使用dirsearch
使用
```sh
docker run -it --rm "dirsearch:v0.4.3" -u target -e php,html,js,zip
```


参考资料
---------------
- [Dirsearch综合指南](https://www.hackingarticles.in/comprehensive-guide-on-dirsearch/) 作者: Shubham Sharma
- [Dirsearch综合指南第2部分](https://www.hackingarticles.in/comprehensive-guide-on-dirsearch-part-2/) 作者: Shubham Sharma
- [如何使用Dirsearch查找隐藏的Web目录](https://www.geeksforgeeks.org/how-to-find-hidden-web-directories-with-dirsearch/) 作者: GeeksforGeeks
- [DIRSEARCH使用完整指南](https://esgeeks.com/guia-completa-uso-dirsearch/?feed_id=5703&_unique_id=6076249cc271f) 作者: ESGEEKS
- [如何使用Dirsearch检测Web目录](https://www.ehacking.net/2020/01/how-to-find-hidden-web-directories-using-dirsearch.html) 作者: EHacking
- [dirsearch使用方法](https://vk9-sec.com/dirsearch-how-to/) 作者: VK9 Security
- [使用Dirsearch查找隐藏的Web目录](https://null-byte.wonderhowto.com/how-to/find-hidden-web-directories-with-dirsearch-0201615/) 作者: Wonder How To
- [使用dirsearch暴力破解Web服务器中的目录和文件](https://upadhyayraj.medium.com/brute-force-directories-and-files-in-webservers-using-dirsearch-613e4a7fa8d5) 作者: Raj Upadhyay
- [Yahoo上的实时Bug Bounty侦察会话 (Amass, crts.sh, dirsearch) w/ @TheDawgyg](https://www.youtube.com/watch?v=u4dUnJ1U0T4) 作者: Nahamsec
- [使用Dirsearch查找隐藏的Web目录](https://medium.com/@irfaanshakeel/dirsearch-to-find-hidden-web-directories-d0357fbe47b0) 作者: Irfan Shakeel
- [获取25000名员工详细信息](https://medium.com/@ehsahil/getting-access-to-25k-employees-details-c085d18b73f0) 作者: Sahil Ahamad
- [目录暴力破解最佳工具](https://secnhack.in/multiple-ways-to-find-hidden-directory-on-web-server/) 作者: Shubham Goyal
- [在Web服务器上发现隐藏文件和目录 - dirsearch完整教程](https://www.youtube.com/watch?v=jVxs5at0gxg) 作者: CYBER BYTES


技巧
---------------
- 服务器有请求限制？这很糟糕，但请随意通过 `--proxy-list` 随机化代理来绕过它
- 想找出配置文件或备份？尝试 `--suffixes ~` 和 `--prefixes .`
- 只想查找文件夹/目录？为什么不结合 `--remove-extensions` 和 `--suffixes /`！
- `--cidr`、`-F`、`-q` 的组合将减少使用CIDR暴力破解时的大部分噪音和假阴性
- 扫描URL列表，但不想看到429洪水？`--skip-on-status 429` 将帮助您在目标返回429时跳过它
- 服务器包含大文件导致扫描变慢？您*可能*想使用 `HEAD` HTTP方法而不是 `GET`
- 暴力破解CIDR很慢？可能您忘记了减少请求超时和请求重试。建议: `--timeout 3 --retries 1`


贡献
---------------
我们一直在从世界各地的人们那里获得很多帮助来改进这个工具。非常感谢到目前为止帮助过我们的每个人！
查看 [CONTRIBUTORS.md](https://github.com/maurosoria/dirsearch/blob/master/CONTRIBUTORS.md) 了解他们是谁。

#### 欢迎Pull requests和功能请求


许可证
---------------
版权所有 (C) Mauro Soria (maurosoria@gmail.com)

许可证: GNU通用公共许可证，版本2