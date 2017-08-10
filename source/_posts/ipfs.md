---
title: IPFS介绍
date: 2017-08-08 00:19:53
tags: distributed system, blockchain
---

# 0 IPFS是什么

IPFS是一个分布式**文件系统**，目的是以**端到端（P2P）**的超媒体（hypermedia）协议替代**有缺陷的HTTP协议**。IPFS是一个**文件系统**这意味着用户可以在IPFS网络中上传和下载文件。**端到端**意味着IPFS网络中每一个节点（也就是计算机）的地位是一样的，不存在中心化的服务器，因此除非将网络中所有节点查封，否则理论上在IPFS上运行的网站、交换的文件无法被管制的。

# 1 IPSF可以用来干什么

1. 朋友之间共享文件；
2. 在IPFS网络中发布静态网站或结合以太坊等支持智能合约的区块链系统做uncensorable sns website；
3. 浏览IPFS网络中的网站，比如说[Wikipedia的镜像](https://ipfs.io/blog/24-uncensorable-wikipedia/)。

# 2 如何使用IPFS

## 2.0 安装

首先请去[https://ipfs.io/docs/install/](https://ipfs.io/docs/install/) 下载IPFS。

MAC用户按照网页上的说明操作即可；Linux用户应该不需要我教自己能搞定，否则不建议使用Linux；Windows用户需要把`ipfs.exe`所在的文件夹添加到系统环境变量中。比如说`ipfs.exe`的位置是`C:\Program Files\go-ipfs`，那么操作步骤如下：

1. 复制`C:\Program Files\go-ipfs`；
2. 右击`我的电脑`，选择`属性`，点击`高级`选项卡，选择`环境变量`，在`系统变量`中找到`Path`，双击`Path`选择新建，将`C:\Program Files\go-ipfs`粘贴进去。注意，如果`系统变量`中没有`Path`那直接点击新建，`变量名`写`Path`，`变量值`写`C:\Program Files\go-ipfs`；
3. 一路点击确定或保存将这些窗口关闭即可。

## 2.1 使用

### 2.1.0 打开命令行

MAC用户打开terminal或iterm，Linux用户打开自己的shell，windows用户打开CMD。

### 2.1.1 初始化

第一次使用前需初始化IPFS，初始化请直接输入

```shell
ipfs init
```

### 2.1.2 打开守护进程（Daemon）

注意，在使用下面所介绍的功能前请打开ipfs的守护进程，方法如下：

```shell
ipfs daemon
```

如果你希望共享你电脑上的文件，那么也请将ipfs daemon打开，否则其他人将无法下载。

### 2.1.3 上传文件或文件夹

上传文件

```shell
ipfs add /path/to/file
```

其中`/path/to/file`指的是文件的路径，比如说`"C:\Program Files\go-ipfs\ipfs.exe"`（windows的路径表示方法）或者`~/ipfs/ipfs`（MAC和Linux的表示方法）。

上传目录

```shell
ipfs add -r /path/to/directory
```

其中的`/path/to/directory`同上，只不过文件变成了目录。

注意，IPFS会将上传文件处理后存储在本地，如果觉得本地磁盘或分区太小请制定某一个大容量的磁盘，制定方法是设置`IPFS_PATH`参数。

MAC和Linux用户请在命令行里输入`export $IPFS_PATH=/path/to/ipfsrepo`，windows用户请在系统环境变量中添加`IPFS_PATH`为`/path/to/ipfsrepo`。其中`/path/to/ipfsrepo`指的是制定的大容量磁盘。

上传文件或者文件夹后ipfs返回文件/文件夹对应的哈希值（hash），哈希值可以理解成文件/文件夹的身份证，我们下载文件时输入这些哈希值就能找到对应的文件了。

### 2.1.4 下载文件

```shell
ipfs get hash
```

其中hash指的是文件或文件夹的哈希值。

使用上面的命令下载下来的文件以哈希值命名，希望下载为原文件名请使用下面的命令：

```shell
ipfs get hash -o filename.extension
```

### 2.1.5 浏览文件夹

当你需要浏览哈希值代表的文件夹下的内容时你可以使用以下命令，该命令会返回hash代表的文件夹下所有文件和文件夹的哈希值以及对应名称。

```shell
ipfs ls hash
```

### 2.1.6 浏览IPFS网络中的网页

1. 在命令行里输入`ipfs daemon`；
2. 完成第一步后在浏览器里输入`localhost:8080/ipfs/hash`。其中hash表示网页的哈希值，比如说英文Wikipedia的哈希值是QmXoypizjW3WknFiJnKLwHCnL72vedxjQkDDP1mXWo6uco，那么其在IPFS网络中的网址就是[http://localhost:8080/ipfs/QmXoypizjW3WknFiJnKLwHCnL72vedxjQkDDP1mXWo6uco](http://localhost:8080/ipfs/QmXoypizjW3WknFiJnKLwHCnL72vedxjQkDDP1mXWo6uco)。

注意，localhost是本地地址，你在通过IPFS访问Wikipedia并没有向某一个服务器请求Wikipedia的网站。没有服务器意味着封锁服务器这件事不再存在，也就意味着你可以自由且不被干扰地访问IPFS网络中的所有页面（理论上）。

# 3 一些例子

一些演示的图片，看不清图可以右键点原图。

## 3.0 上传

上传文件：

![ipfs add directory](/images/ipfs_add_file.jpg)

上传文件夹：

![ipfs add directory](/images/ipfs_add_dir.jpg)

注意有`-r`。

## 3.1 下载

下载文件：

![ipfs add directory](/images/ipfs_get_file.jpg)

下载速度看网络情况了，我这边下载700M的文件用了42秒。

下载文件夹：

![ipfs add directory](/images/ipfs_get_dir.jpg)

上图我还演示了一下如果用`ipfs ls`命令一层层打开文件夹找到文件的过程，大家找文件夹中的文件可以参考此方法。

# 4 其他

- IPFS共享文件的方式是无服务器的端到端模式，因此，如果提供此文件的电脑不在线或者关闭了IPFS，那文件将无法下载，直到对方开机或者打开`ipfs daemon`；
- 如果使用失败，可以考虑重新打开`ipfs daemon`；
- 打开daemon后运行IPFS的其他命令请打开另外一个命令行。
