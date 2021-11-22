---
title: 如何使用Bedrock Dedicated Server
date: 2021-11-21 13:55:56
categories:
  - 开发日记
---

注：本页面翻译自微软所发行的Bedrock Dedicated Server压缩包内的`bedrock_server_how_to.html`，若有翻译不准确您可以在此留言

# 如何使用基岩版专用服务端

## 免责声明

这是一个早期版本（alpha），我们尚不完全支持。它可能包含严重问题，我们可能随时停止支持它。

## 推荐的硬件

我们建议在具有至少 2 个内核和 1 Gb RAM 的 64 位英特尔或 AMD 处理器计算机上运行基岩版专用服务端。

## 平台

### Linux

基岩专用服务端的Linux版本需要Ubuntu 18或更高版本。

不支持其他发行版。将容器文件解压缩到空文件夹中。使用以下命令启动服务器：
``` sh
LD_LIBRARY_PATH=../bedrock_server
```

### Windows

基岩服务器的Windows版本需要：

 - Windows 10 版本 1703 或更高版本
 - Windows Server 2016 或更高版本

将压缩文件解压缩到空文件夹中。通过执行`bedrock_server.exe`启动服务器。

在某些系统上，当您希望使用与服务器运行在同一台计算机上运行的客户端连接到服务器时，您需要将 Minecraft 客户端从 UWP 环回限制中免除：

```
CheckNetIsolation.exe LoopbackExempt –a –p=S-1-15-2-1958404141-86561845-1752920682-3514627264-368642714-62675701-733520436
```

## 配置
服务器将尝试读取名为`server.properties`的文件。

其中一些选项仅在创建新世界时读取，而其他一些选项则在每次启动时读取。

该文件应包含一个列表，其中的键和值用等号分隔，每行一个。

以下选项可用。


|选项名称|可用的值|默认值|何时使用|注意事项|
|:-:|:-:|:-:|:-:|:-:|
|gamemode(默认游戏模式)|生存模式(0),创造模式(1),冒险模式(2)|生存模式(0)|仅对于新玩家生效||
|force-gamemode(强制游戏模式)|开启(true),关闭(false)|false|一直生效|当把此值设置为false(或未设置此值时)可防止服务器在创建世界期间向客户端发送服务器保存的游戏模式值以外的游戏模式值，即使这些值是在创建世界后在`server.properties`文件中设置的。将此值设置为true会强制服务器向客户端发送除服务器在创建世界期间保存的游戏模式值以外的游戏模式值（如果在创建世界后在`server.properties`文件中设置了这些值）|
|difficulty(难度)|和平(0),容易(1),正常(2),困难(3)|容易(1)|一直生效||
|level-type(世界类型)|平坦(FLAT), 旧版(LEGACY),默认(DEFAULT)|默认(DEFAULT)|生创建世界时||
|server-name(服务器名称)|任何值|Dedicated Server|一直生效|这是游戏内服务器列表中显示的服务器名称|
|max-players(最大游玩人数)|任何整数|10|一直生效|应该能够在服务器上玩游戏的最大玩家数。<br>**较高的值可能会影响性能**。|
|server-port(ipv4端口)|0~65535间的任何整数|19132|一直生效||
|server-portv6(ipv6端口)|0~65535间的任何整数|19133|一直生效||
|level-name(存档名称)|任何值|Bedrock level|一直生效|此项代表要使用/生成世界的名称，他们存放于`/worlds`文件夹|
|level-seed(存档种子)|任何值|留空|世界生成时|用于随机化世界的种子。如果留空，将随机选择种子|
|online-mode(正版验证)|开启(true),关闭(false)|开启(true)|一直生效|如果开启(true)，则所有连接的玩家都必须通过 Xbox Live 的身份验证。无论此设置如何，连接到远程（非 LAN）服务器的客户端将始终需要 Xbox Live 身份验证。如果服务器接受来自公共网络的连接，则强烈建议启用联正版验证。|
|white-list(白名单)|开启(true),关闭(false)|关闭(false)|一直生效|如果开启(true)，则所有连接的玩家都必须列在单独的`whitelist.json`文件中。<br>请参阅[白名单](#白名单)部分。|
|allow-cheats(允许作弊)|开启(true),关闭(false)|关闭(false)|一直生效||
|view-distance(视距)|任何数字|10|一直生效|允许玩家的最大视距。<br>**较高的值可能会影响性能**。|
|player-idle-timeout(挂机超时时间)|任何整数|30|一直生效|在玩家挂机了这么多分钟后，他们将被踢出。<br>如果设置为 0，则玩家可以无限期地挂机。|
|max-threads(最大线程数)|任何数字|8|一直生效|服务器将尝试使用的最大线程数|
|tick-distance(随机刻度)|4~12间的任何整数|4|一直生效|每个游戏刻都会对特定的区块依次执行区块刻/区块遍历。<br>**较高的值可能会影响性能**|
|default-player-permission-level(默认玩家权限)|访客(visitor),成员(member),管理员(operator)|成员(member)|一直生效|此项将决定新玩家首次加入时将拥有哪个权限级别。|
|texturepack-required(强制加载服务器材质包)|开启(true),关闭(false)|关闭(false)|一直生效|如果世界使用任何特定的纹理包，则此设置将强制客户端使用它。|
|content-log-file-enabled(是否写入日志)|开启(true),关闭(false)|关闭(false)|一直生效|启用后会将内容错误记录到文件中。|
|compression-threshold(网络数据包压缩数值)|0~65536间的任何整数|1|一直生效|确定要压缩的原始网络负载的最小大小。可用于试验 CPU 带宽权衡。|
|server-authoritative-movement(服务端验证玩家移动)|开启(true),关闭(false)|开启(true)|一直生效|如果开启(true)，服务端将在服务器上矫对本地用户移动，并在客户端的位置与服务器的位置不匹配时向下发送更正。仅当`correct-player-movement`设置为开启(true)时，才会进行更正。|
|player-movement-score-threshold(玩家异常行为次数阈值)|任何整数|20|一直生效|报告异常行为之前所需的不协调时间间隔数。换句话说，玩家在我们采取行动之前做了多少次可疑的事情。仅与`server-authoritative-movement`相关。|
|player-movement-distance-threshold(玩家移动距离阈值)|任何正浮点数|0.3|一直生效|在注册异常行为之前需要超过服务器和客户端位置之间的差异。仅与`server-authoritative-movement`相关。|
|player-movement-duration-threshold-in-ms(玩家异常移动阈值毫秒)|任何正整数|500|一直生效|在异常移动判定之前，服务器和客户端位置可能不同步（由玩家移动距离阈值定义）的持续时间。此值以毫秒为单位定义。仅与`server-authoritative-movement`相关。|
|correct-player-movement(矫正玩家移动)|关闭(false),开启(true)|关闭(false)|一直生效|如果为 true，则当移动次数超过阈值时，客户端位置将被更正为服务器位置。仅与`server-authoritative-movement`相关。我们尚不建议启用此功能。功能仍在开放中。|

## 文件夹

解压缩时，您将看到一些文件夹和二进制可执行文件。

首次启动服务器时，将创建一堆新的（空）文件夹。您应该关心的文件夹如下：

|文件夹名称|用处|
|:-:|:-:|
|behavior_packs|这是可以安装新行为包的位置。目前还不能直接从世界中激活他们。|
|resource_packs|这是可以安装新资源包的位置。目前还不能直接从世界中激活他们。|
|worlds|如果此文件夹尚不存在，则将在启动时创建。创建的每个世界都会存放在`server.properties`中设置的`level-name`文件夹下|

## 白名单

如果在`server.properties`启用了`white-list`，则服务器将仅允许选定的用户进行连接。

若要允许用户连接，你需要知道其Xbox Live玩家代号。

将用户添加到白名单的最简单方法是使用控制台命令。
```
whitelist add <xboxid>
```
注意：如果玩家代号中有空格，则需要用双引号将其括起来：
例：
```
whitelist add notch

whitelist add "Jeb Bergensten"
```

如果以后要从列表中删除某人，可以使用如下命令
```
whielist remove <xboxid>
```
例：
```
whitelist remove notch

whitelist remove "Jeb Bergensten"
```

白名单将保存在名为`whitelist.json`的文件中。

如果要自动执行从中添加或删除玩家的过程，则可以执行此操作。

修改文件后，需要运行`whitelist reload`命令以确保服务器知道您的新更改。

该文件包含一个 JSON 数组，其中包含包含以下键/值的对象。

|键|类型|值|
|:-:|:-:|:-:|
|name|字符串|用户的xboxid|
|xuid|字符串|用户的 XUID。如果未设置，则当具有匹配名称的人连接时，服务端将补全它。|
|ignoresPlayerLimit|布尔|如果此用户不应计入最大玩家限制，则为true。这样做的目的是让一些玩家即使服务器已满也能够加入。|

示例文件：`whitelist.json`

``` json
[
    {
        "ignoresPlayerLimit": false,
        "name": "MyPlayer"
    },
    {
        "ignoresPlayerLimit": false,
        "name": "AnotherPlayer",
        "xuid": "274817248"
    }
]
```

## 权限

您可以通过在与服务器可执行文件位于同一目录中`permissions.json`来调整玩家特定的权限。

该文件包是一个具有XUID和权限的简单JSON对象。

有效权限为：`visitor`、`member`、`operator`。

与这些帐户连接的每个玩家都将根据设置的预授权进行处理。

如果在服务器运行时更改此文件，请运行`permission reload`命令以确保服务器知道您的新更改。

您还可以使用`permission list`列出当前权限。

请注意，需要启用`online-mode`功能才能正常工作，因为xuid需要对用户帐户进行在线验证。

如果此列表中未包含的新玩家连接，将会使用`server.properties`中设置`default-player-permission-level`权限。

示例文件：`permissions.json`

``` json
[
    {
        "permission": "operator",
        "xuid": "451298348"
    },
    {
        "permission": "member",
        "xuid": "52819329"
    },
    {
        "permission": "visitor",
        "xuid": "234114123"
    }
]
```

## 崩溃报告

如果服务器崩溃，它将自动向我们发送各种信息，以帮助我们将来解决问题。

## 命令

您可以通过在控制台中键入内容向服务器发出命令。以下命令可用。< >表示参数是必需的，[ ] 表示它是可选的，并且|表示不同的允许值。如果字符串包含空格，请将他们括在双引号"中。

|命令语法|描述|
|:-:|:-:|
|kick <xboxid 或者 xuid> [原因]| 立即踢出一个玩家，并在玩家屏幕上显示踢出原因。|
|stop|正常关闭服务器。|
|save <hold &#124; resume  &#124; query>|用于在服务器运行时进行自动备份。有关详细信息，请参阅[备份](#备份)备份。|
|whitelist <on  &#124; off  &#124; list  &#124; reload>|`on`和`off`打开和关闭白名单。请注意，这不会更改`server.properties`中的值！<br>`list`打印服务器使用的当前白名单。<br>`reload`使服务器从文件重新加载白名单。<br>参阅[白名单](#白名单)部分了解更多。|
|whitelist <add  &#124; remove> &#60;name&#62;|在白名单文件中添加或删除玩家name参数应是要添加或删除的玩家的XboxID。您无需在此处指定XUID，它将在玩家首次连接时解析。参阅[白名单](#白名单)部分了解更多。|
|permission <list &#124; reload>|`list`打印当前使用的权限列表。<br>`reload`使服务器从`permissions.json`文件重新加载操作员列表。<br>有关详细信息，请参阅[权限](#权限)部分。|
|op &#60;name&#62;|将玩家提升为服务器操作员(`operator`)。如果缺少`permission.json`，将创建它。如果玩家未通过正版验证，则会为当前服务器会话升级该玩家，但是不会写入文件。服务器重新启动后，将向玩家分配服务器权限级别。|
|deop &#60;name&#62; | 将玩家降级为成员(`member`)，如果缺少`permission.json`，将创建它。|
|changesetting &#60;setting&#62; &#60;value&#62;|更改服务器设置，而无需重新启动服务器。目前仅能更改`allow-cheats`(true,false)和`difficult`(0,1,2,3)，它们不会修改`server.properties`中指定的值。|

# 备份

服务器支持在服务器运行时备份`world`文件。

它对于进行手动备份不是特别友好，但在自动化时效果更好。

备份（从服务器的角度来看）由三个命令组成。


|命令|描述|
|:-:|:-:|
|save hold|这将要求服务器准备备份。它是异步的，将立即返回。|
|save query|调用后`save hold`，应反复调用此命令，以查看准备工作是否已完成。当它返回成功时，它将返回您需要复制的文件的文件列表（每个文件的长度）。发生这种情况时，服务器不会暂停，因此可以在备份时修改某些文件。只要您只复制给定文件列表中的文件并将复制的文件截断到指定的长度，那么备份就应该有效。|
|save resume|制完文件后，您应该调用它来告诉服务器可以再次删除旧文件。|