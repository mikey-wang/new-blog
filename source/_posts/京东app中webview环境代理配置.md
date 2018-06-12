---
title: 京东app中webview环境代理配置
author: Mickey
date: 2018-01-26
tags:
 - forJD
categories:
 - 技术博客
thumbnail: images/others/jd.jpg
---


## 前言

本文是一篇配置教程。适用于在京东app中的webview环境中测试待上线的H5应用。**Powered by 冯旭东(fengxudong1)**。

需要用到的软件物料地址如下：

 1. [charles 4.2.1][3] 
 2. [charles科学使用工具][4]
 3. [360免费WIFI][5]

## 软件准备

按照上述，下载Charles，并进行科学配置。如若PC或Mac及移动设备已处于同一wifi环境，可忽略安装360免费WIFI。

## 软件及环境配置

如果你还对Charles不是很熟悉，这里提供一篇完备详尽的[Charles 从入门到精通][6]，在时间充裕的情况下请参考查阅。下面对主要配置及接入京东app的调试步骤做以说明。

### Charles配置

正常安装Charles后便可截获本机Http封包，如若需要过滤请求方便查看，请移步[Charles 从入门到精通][7]查看。为截获移动设备封包，在 Charles 的菜单栏上选择 `“Proxy”->”Proxy Settings”`，填入代理端口 8888，并且勾上 `“Enable transparent HTTP proxying”` 就完成了在 Charles 上的设置。

<center>![捕获.PNG-26.5kB][8]</center>

### 配置移动设备

此处以iPhone为例做介绍。首先我们需要获取 Charles 运行所在电脑的 IP 地址，Charles 的顶部菜单的 `“Help”->”Local IP Address”`，即可在弹出的对话框中看到 IP 地址，如下图所示：

<center>![捕获.PNG-40.7kB][9]</center>
 
 在 iPhone 的 “ 设置 “->” 无线局域网 “ 中，可以看到当前连接的 wifi 名，通过点击右边的详情键，可以看到当前连接上的 wifi 的详细信息，包括 IP 地址，子网掩码等信息。在其最底部有「HTTP 代理」一项，我们将其切换成手动，然后填上 Charles 运行所在的电脑的 IP，以及端口号 8888。
 
 设置好之后，我们打开 iPhone 上的任意需要网络通讯的程序，就可以看到 Charles 弹出 iPhone 请求连接的确认菜单，点击 “Allow” 即可完成设置。
 
### 配置截取 Https

点击 Charles 的顶部菜单，选择 `“Help” -> “SSL Proxying” -> “Install Charles Root Certificate”`，顺序执行安装。

如果我们需要在 iOS 或 Android 机器上截取 Https 协议的通讯内容，还需要在手机上安装相应的证书。点击 Charles 的顶部菜单，选择 `“Help” -> “SSL Proxying” -> “Install Charles Root Certificate on a Mobile Device or Remote Browser”`，然后就可以看到 Charles 弹出的简单的安装教程。如下图所示：

<center>![捕获.PNG-27.4kB][10]</center>

如图显示，浏览器访问`chls.pro/ssl`，顺序执行安装。**注意此时移动设备一定已安装前述步骤做过代理配置**。

### 针对京东app的配置

针对app的配置，总的思路是截获`http://api.m.jd.com/client.action?functionId=appCenter`的封包，然后利用Charles的Map或Rewrite功能改写响应报文以展示想要配置的icon。

<center>![捕获.PNG-84.2kB][11]</center>

 - 首先在app设置中清空缓存并退出，准备在Charles中截获封包；
 - 重新进入app，点击app中的**全部icon**，找到下图中的1，右键点击`SSL Proxying：Enable`;
 - 清空app缓存并退出，准备重新截获封包；
 - 再次进入app，点击app中的**全部icon**，找到如下图中2所示的前置图标开头的`http`请求；
 - 点击下图中3，复制一份响应数据4，并保存到本地文件，后缀为`.json`；
 - 修改其中一项的name和url；
 
 ```json 
  {
		"id": 126,
		"icon": "https://m.360buyimg.com/mobilecms/s80x80_jfs/t5830/118/179491490/8188/42c7ec47/591dab29N24f970a9.png",
		"slogan": "",
		"order": 4,
		"name": "酒店试用",// 已改为自定义
		"appCode": "jdsy",
		"jump": {
			"des": "m",
			"params": {
				"needLogin": "0",
				"url": "http://trip.hotel.m.jd.com/"// 已改为自定义
			},
			"srv": "4_京东试用_购京东_126_jdsy_-100"
		}
	}
 ```

<center>![捕获.PNG-249.5kB][12]</center>

 - 右击上图3，选择`Map Local`，关联上步中的json文件，如下图；
 
 <center>![捕获.PNG-48.3kB][13]</center>
 - 配置好后，再次清空app缓存并退出；
 - 再次重新进入时，点击app中的全部icon，便可看到刚才配置的icon，点击跳转访问H5应用；

<center>![捕获.PNG-186.2kB][14]</center>
 
如若有任何问题，请咚咚联系以获取支持，谢谢。

  [1]: http://mickeywang.com
  [2]: https://www.zhihu.com/people/laughingHome
  [3]: https://www.charlesproxy.com/latest-release/download.do
  [4]: https://www.zzzmode.com/mytools/charles/
  [5]: http://wifi.360.cn/easy/pc
  [6]: http://blog.devtang.com/2015/11/14/charles-introduction/
  [7]: http://blog.devtang.com/2015/11/14/charles-introduction/
  [8]: http://static.zybuluo.com/MickeyWang/ass6d4cbd9xw9t4fup7fvjrf/%E6%8D%95%E8%8E%B7.PNG
  [9]: http://static.zybuluo.com/MickeyWang/geecver46yky9b6zwnzrlej9/%E6%8D%95%E8%8E%B7.PNG
  [10]: http://static.zybuluo.com/MickeyWang/b91trmynw9huyzk76dnecwq6/%E6%8D%95%E8%8E%B7.PNG
  [11]: http://static.zybuluo.com/MickeyWang/9ucpi82g4zf96uzx15h2xc0q/%E6%8D%95%E8%8E%B7.PNG
  [12]: http://static.zybuluo.com/MickeyWang/o9in5i6y5fddolo49rguang2/%E6%8D%95%E8%8E%B7.PNG
  [13]: http://static.zybuluo.com/MickeyWang/20aal1bw4paz2cfyruaa4iw5/%E6%8D%95%E8%8E%B7.PNG
  [14]: http://static.zybuluo.com/MickeyWang/tmis1sna0qwpb8s4nmos3sgh/%E6%8D%95%E8%8E%B7.PNG