# 免费打造安全稳定超高速的科学上网环境

这篇文章将会介绍如何注册 Google 提供的一年 $300 美金的试用，然后使用 HyperApp 搭建科学上网服务。本文不需要读者有较深的技术背景，全部过程都可视化、自动化完成。

下文用 GCP 代表 Google Cloud Platform，指 Google 的整个云平台。GCE 是指 Google Cloud Engine，是 GCP 产品线里的一个主机产品。

### 本文前提条件

1. 有一个 Google 账号，没有的话可以注册一个。
2. 注册 GCP 免费试用需要用信用卡进行身份验证（只做验证，不会收费），所以你必须有一个 Visa/MasterCard 的信用卡才行。如果你是一位学生还没有信用卡，你可以看文末的其它厂商提供的一些免费几个月的试用。

## GCP 注册以及设置服务器

要访问 Google 首先需要你能科学上网，但是这篇文章又是介绍如何科学上网的，有点鸡生蛋的矛盾… 其实你可以去App Store搜索 V屁恩，找一个免费的下载后，通过签到等方式暂时领取一天或者几个小时的试用来完成下面的教程。

### 注册 GCP 免费使用

[图Free首页][图确认页面]

1. 登录Google账号后使用这个链接来注册 GCP https://cloud.google.com/free/ ，在打开的页面中点击“Try it Free"。
2. 接受条款，并点击”同意并继续“
3. 在页面中填入你的信息：
    * 账号类型：个人
    * 名称和地址：填写你的地址、电话等
    * 付款方式：添加一个信用卡。这个信用卡将会用来验证身份，防止GCP被滥用。
    * 点击 ”开始免费使用“ 完成注册

### 新建主机
[图1]

按照上图所示，在左侧菜单中导航到 [计算引擎 → VM 实例](https://console.cloud.google.com/compute/instances)


[图2]
点击加号按钮，创建一个 VM 实例。

[图3]

* 名称：随意填入一个易记得名字
* 地区：建议选择 `asia-east1-*` 三个中的任意一个，这个机房是在台湾，国内的延迟只有 50~70ms，简直快到掉渣。
* 机器类型：选择 “小型” (1.7G 内存) 就可以了。默认选中的 3.75G 的内存其实用不完。
* 启动磁盘：默认选项就可以，但是为了防止将来磁盘不够用，你可以点击右下角的更改，大小里使用 20G 或者 30G。
* 防火墙：选中 “允许HTTP流量” 和 ”允许HTTPS流量“

点击”创建“，稍等几分钟就会创建完毕，现在打开 HyperApp 开始配置这台服务器吧。


## HyperApp

本段介绍如何使用 HyperApp 自动开启 BBR 加速，并且按照科学上网应用

### 添加服务器

[图2-1]
在 ”服务器“ 页面点击右下角的加号，然后选择第二个”自动配置“

[图2-2]
点击“开始”，应用会自动生成一对密钥，但这个过程在某些手机上会耗时较长，请耐心等到 十几秒~几分钟 左右。

[图2-3]
等到出现“一切就绪”时，点击“复制“将代码复制到剪贴板里，如果你用电脑操作的话可以点击”发送“将代码通过任意一种方式发送到电脑上。

[图2-4]
在 GCE [计算引擎 → VM 实例](https://console.cloud.google.com/compute/instances) 页面，选择刚刚创建的实例 `SSH ▽ 在浏览器窗口中打开`

[图2-5]
在浏览器的SSH中粘贴并运行刚刚复制的代码。等到二维码出现时，使用 HyperApp 扫描该二维码便会自动添加该服务器。添加后 HyperApp 会显示刚刚添加的服务器的基本运行状况。

### 永久添加公钥

注意：你可以暂时跳过本小段，进行下面的操作，如果 HyperApp 出现了认证错误的提示，可以回到这段进行操作。

刚刚的自动配置的功能在几乎所有主机上都可以用，但是 GCE 是个例外，它会清空用户自行添加的公钥，所以几分钟后可能就会出现密钥认证失败的错误。你可以通过以下步骤永久性的设置密钥。

1. 打开 `更多` → `SSH Keys` → `点击唯一的一个 Key` → `复制公钥`。
2. 打开 [计算引擎 → 元数据 → SSH 密钥](https://console.cloud.google.com/compute/metadata/sshKeys) 点击修改后，复制并添加刚刚的公钥。
3. 添加后页面会出现一行新记录，主要有两列：`用户名` 和 `密钥`。这个用户名应该是 HyperApp，然后打开 HyperApp → 编辑服务器 → 修改 `用户名` 为刚刚显示的用户名，保存后就可以了。

---

### 开启 BBR 加速，跑满带宽

这一步并不影响科学上网，但是会极大的提高上网质量。BBR是Google 开发的TCP拥塞控制技术，并且已经合并到较新的Linux内核中。它的主要作用是让你可以跑满服务器的带宽。

比如说没有开启的情况下你观看 Youtube 720P的视频都可能会卡，但是开启了BBR后 1080P 的视频完全无卡顿，就连 4K 视频也可以流畅观看（有些网络特别卡的除外）。

要开启 BBR 需要升级 Linux 内核，不过不要急，在 HyperApp 中开启非常简单：

[图3-1]
1. 在服务器卡片中点击右上角的 Terminal 图标，进入 SSH 终端
2. 选择最下面的一行工具栏的第一个图标，然后点击 "teddy/BBR" 一键脚本的链接，点击后会让你确认是否下载并执行外部脚本。
3. 点击确认后会自动下载并执行，执行过程中需要点击键盘上任意一个键确认继续。如果想中断执行，那么点击 `ctrl` 后按 `c` 终止执行。
4. 稍等几分钟，等待系统内核升级到最新版后会自动重启。

注：这是系统层面的升级，执行完毕后不必对其它应用做任何配置。客户端也不需要做任何配置。

---


### 部署科学上网应用

[图4-1]
在 `商店` 页面 `网络` 分组下选择任意一个应用，这里我们选择占用资源最少的 `Shadowsocks-libev`，（下文用ss-libev代理）点击该应用

[图4-2]
在弹出的对话框中选择刚刚添加的服务器，点击`创建应用`

[图4-3]
在出现的 ss-libev 配置页面中输入一个密码，加密方式推荐 `chacha20`
* Port: 可以填入 80 或者 443（因为用其它端口需要设置防火墙）
* 密码：随便填入一个密码
* 加密：
* OBFS: 使用 OBFS 可以将 SS 流量伪装成正常的网页访问从而达到欺骗效果，可以防止被墙或者运营商干扰。另外还有人利用此功能实现“免流”效果，运营商会对一些域名免收流量费，于是可以将所有SS流量伪装成对某个域名的访问从而达到免流效果，具体不表。

如果你使用其它端口，请参考文末的 [设置GCE防火墙](#设置-gce-防火墙) 如何开启防火墙。


---

## 客户端设置

iOS 上有很多 SS 的客户端可供选择，收费的有 小火箭、土豆丝、Surge、Cross 等。免费的可以使用 `Wingy`。基本上所有的应用配置方法都是一样的，这里以 `Wingy` 为例。

添加服务器，填入你的服务器IP、端口、密码即可。


### 设置 GCE 防火墙

1. 点击 [GCP的网络设置页面](https://xx) 进入页面后添加

