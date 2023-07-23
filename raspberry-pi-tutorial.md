# 树莓派折腾指南

## 一、装系统

直接前往树莓派官网`https://www.raspberrypi.com/software/`下载官方镜像和工具安装raspberry pi os即可

## 二、安装clash

在这里我们使用 Clash 作为科学上网的代理框架，为什么使用 Clash 呢，那是因为 Clash 内核支持各种 Unix 平台的各个架构；还支持各种代理协议，像 Trojan、Vmess、Shadowsocks 等等；以及强大的分流规则，可以非常方便地自定义各种规则。

### 1. 下载 Clash 内核

你可以到 GitHub 下载 [Clash 内核](https://github.com/Dreamacro/clash/releases)。如果你是**树莓派 4B 32 位操作系统** ，那么你应该下载对应 armv7 版本的 [clash-linux-armv7-v1.11.0.gz](https://github.com/Dreamacro/clash/releases/download/v1.11.0/clash-linux-armv7-v1.11.0.gz)，如果是 **树莓派 4B 64 位操作系统** ，那么你应该下载对应 armv7 版本的 [clash-linux-armv8-v1.11.0.gz](https://github.com/Dreamacro/clash/releases/download/v1.11.0/clash-linux-armv8-v1.11.0.gz)。

其他型号的树莓派可以通过以下命令查看树莓派的架构：

```bash
arch
```

[![clash-release](https://mraddict.top/images/posts/clash-on-rpi/clash-release.png)](https://mraddict.top/images/posts/clash-on-rpi/clash-release.png)

### 2. 解压移动 Clash 内核

下载完成后解压文件，建议把文件名改为 `clash`， 然后移动到 `/usr/local/bin/clash` 位置，同时给该文件以执行的权限：

```bash
wget https://github.com/Dreamacro/clash/releases/download/v1.10.6/clash-linux-armv7-v1.10.6.gz
gunzip clash-linux-armv7-v1.10.6.gz
rm -rf clash-linux-armv7-v1.10.6.gz
mv clash-linux-armv7 clash
sudo mv clash /usr/local/bin
sudo chmod a+x /usr/local/bin/clash
```

## 三、配置 Clash

### 1. 添加 Clash 配置文件

Clash 配置文件的默认路径是 `~/.config/clash`，如果你的 Home 目录不存在相应文件夹就需要你自己创建，然后把你机场提供的配置文件放到该文件下就可以了，Clash 配置文件的默认名称应该是 `config.yaml`。

```bash
mkdir ~/.config/clash
mv your/clash/config/file config.yaml
mv config.yaml ~/.config/clash
```

### 2. 添加全球 IP 库

另外 Clash 还需要一个 Country.mmdb 文件，Country.mmdb 是全球 IP 库，可以实现各个国家的 IP 信息解析和地理定位，没有这个文件 clash 无法正常启动，你可以前往 [GitHub 下载](https://github.com/SukkaW/Koolshare-Clash/blob/master/koolclash/koolclash/config/Country.mmdb)。下载完成后同样放在默认路径下就可以了 `~/.config/clash`。

## 四、配置终端代理

### 1. 设置环境变量

首先我们需要添加几个环境变量：

```bash
sudo vim /etc/environment
```

然后添加以下配置内容：

```bash
export http_proxy="http://127.0.0.1:7890"
export https_proxy="http://127.0.0.1:7890"
export no_proxy="localhost, 127.0.0.1, *edu.cn"
```

> 注意：系统变量的 https_proxy 的代理地址和 http_proxy 的代理地址是一样的，因为 Clash 使用一个地址同时代理 http 和 https。另外，no_proxy 表示其中的地址不需要代理，这一点很重要，比如我们不需要代理我们的校园网地址，因此加入要 `*edu.cn`。

### 2. 为终端应用配置代理

然后再对相应的终端应用配置代理：

- 为 sudo 配置代理
- 为 apt 配置代理
- 为 git 配置代理

进入 sudo 配置文件：

```bash
sudo visudo
```

然后添加以下内容：

```bash
Defaults env_keep+="http_proxy https_proxy no_proxy"
```

进入 apt 配置文件：

```bash
sudo vim /etc/apt/apt.conf.d/10proxy
```

然后添加以下内容：

```bash
Acquire::http::Proxy "http://127.0.0.1:7890/";
```

进入 git 配置文件：

```bash
vim ~/.gitconfig
```

然后添加以下内容：

```bash
[http]
  proxy=http://127.0.0.1:7890
[https]
  proxy=http://127.0.0.1:7890
```

- 为 pip 配置代理
- 为 npm 配置代理
- 为 cargo 配置代理

进入 pip 配置文件：

```bash
vim ~/.config/pip/pip.conf
```

然后添加以下内容：

```bash
[global]
  proxy = http://127.0.0.1:7890
  http-proxy = http://127.0.0.1:7890
  https-proxy = http://127.0.0.1:7890
  trusted-host = pypi.python.org global.trusted-host pypi.org global.trusted-host files.pythonhosted.org
```

进入 npm 配置文件：

```bash
vim ~/.npmrc
```

然后添加以下内容：

```bash
proxy=http://127.0.0.1:7890
http-proxy=http://127.0.0.1:7890
https-proxy=http://127.0.0.1:7890
```

进入 cargo 配置文件：

```bash
vim ~/.cargo/config
```

然后添加以下内容：

```bash
[http]
  proxy=http://127.0.0.1:7890
[https]
  proxy=http://127.0.0.1:7890
```

### 电脑

理论上这样一波配置后，大部分终端应用都可以正常使用了，如果你有其他的终端应用可自行参考相关文档进行配置。配置完成后需要重启树莓派让配置生效，这样配置才能生效。

## 五、使用 Clash

重启之后在终端中输入 clash，如果输出类似以下内容那么就说明 Clash 启动成功了。

```bash
INFO[0000] Start initial compatible provider 手动选择
INFO[0000] Start initial compatible provider 节点选择
INFO[0000] Start initial compatible provider 故障切换
INFO[0000] Start initial compatible provider 自动选择
INFO[0000] HTTP proxy listening at: [::]:7890
INFO[0000] RESTful API listening at: 127.0.0.1:9090
INFO[0000] SOCKS proxy listening at: [::]:7891
```

你可以更新一下系统或者打开浏览器测试一下 Google，如果可以访问你就可以愉快地玩耍了！

> 注意：在终端请不要使用 `ping google.com` 来测试，因为 ping 使用不同的协议无法被 Clash 代理，不过你可以使用 `curl google.com`。

## 六、开机自启

既然我们都已经可以使用 Clash 了，当然要让树莓派能够开机自启 Clash 对吧。在树莓派推荐使用 crontab 作为自动任务管理器。

输入以下命令可以打开 crontab：

```bash
crontab -e
```

第一次使用可能需要你选择默认的编辑器，看个人喜好选择就好，然后在打开的文件末尾添加以下内容：

```bash
@reboot /usr/local/bin/clash
```

## 七、控制面板

### 1. 下载 Clash-Dashboard

GitHub 上有很多优秀的有关 Clash Dashboard 的项目，这些项目可以非常方便地帮助你查看、设置和管理你的 Clash。

从 GitHub 上克隆 [Clash Dashbaord](https://github.com/Dreamacro/clash-dashboard.git) 到 Clash 的默认配置文件夹下。

```bash
cd ~/.config/clash
git clone https://github.com/Dreamacro/clash-dashboard.git
cd ~/.config/clash/clash-dashboard
git checkout -b gh-pages origin/gh-pages
```

### 2. 修改 Clash 配置文件

下载完成后你需要对 Clash 的配置文件稍作修改才能正常使用控制面板，在 config.yaml 的头部添加或者修改以下两项：

```bash
external-ui: clash-dashboard
external-controller: 127.0.0.1:9090
```

然后在浏览器中输入 http://127.0.0.1:9090/ui 就可以看到 Clash 的控制面板了。

## 八、问题

### 1、clash配置文件如何生成？

- 在订阅链接后面加上&flag=clash，用浏览器打开
- 自动下载html文件，重命名为yaml文件，再导入clash

### 2、运行clash后显示`WARN[0000] Failed to start Redir UDP Listener: operation not permitted`的解决方案？

- 可以看这个issue：`https://github.com/Dreamacro/clash/issues/635`

- Try this `sudo setcap cap_net_bind_service,cap_net_admin+ep /path/to/clash`

### 3、树莓派图形界面卡死时的处理

树莓派通常是通过ssh连接在字符界面使用，或者vnc连接来使用的。偶尔我们会遇到桌面进程卡死的状况，具体表现为图标和任务栏按钮无反应，但CPU等负载一切正常。这种情况，除了重启机器之外，还可以通过重启图形界面进程的方式进行修复。

首先，用ssh连接进入字符终端

```bash
pi@DEX54-PC:~ $ ps -t tty7
  PID TTY          TIME CMD
 9493 tty7     00:00:14 Xorg
```

（tty1～6是文本型控制台，tty7是X Window图形显示管理器，这里我们查找到图形界面进程对应的pid）

```bash
pi@DEX54-PC:~ $ sudo kill 9493
```

此时如果连接着vnc，会发现画面短暂黑暗，而后桌面壁纸重新出现，并出现一个登录框，输入密码后Log in，即可恢复正常。

归根结底，就是重启了X服务，因为raspberry默认用的是lightdm作为X服务的管理器，所以最后一步重启lightdm也是可以的。

```bash
pi@DEX54-PC:~ $ sudo service lightdm restart
```

 

以上方法对于强制结束文件浏览窗口（树莓派中默认文件管理器为PCManFM，因为使用的是[LXDE](https://wiki.archlinux.org/index.php/LXDE)桌面环境）导致桌面图标消失也是管用的。
