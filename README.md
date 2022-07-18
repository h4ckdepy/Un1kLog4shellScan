#PS

发现设计思路有问题，无法实现深度的漏洞探测。

正在重构和优化代码,可能需要过几天放源码了。

# Un1kLog4shellScan

如果您觉得本插件对您有所帮助,欢迎star,万分感谢!

作者: [@depy](https://github.com/h4ckdepy)

博客: [blog](https://rce.ink)


## Testing

对存在漏洞的环境使用插件测试。

1.开启存在log4shell漏洞的spring环境

![image](https://user-images.githubusercontent.com/42985524/179281562-99b87b88-3ec1-4d6f-bb9d-0ed5d42a3f76.png)

![image](https://user-images.githubusercontent.com/42985524/179281661-6438f36a-02c8-423e-a90e-ec432262909d.png)

2.spring部分环境下 对accpet插入恶意payload会导致log4shell漏洞产生

我们在api配置参数 insertaccept 为 true 代表每一个被动payload 都会对accept的header头插入payload

启动server.py 的流量捕获脚本 在api后台配置 探测接口

![image](https://user-images.githubusercontent.com/42985524/179282496-e73a6ea3-505e-484f-bb53-2d74dba1a41b.png)

![image](https://user-images.githubusercontent.com/42985524/179282266-f2d5766e-a2d8-4905-be8b-d08cdf15641d.png)

3.开启插件

![image](https://user-images.githubusercontent.com/42985524/179282397-17bcc429-451f-4aa9-be5e-dc0ab923f9cd.png)

4.切换到漏洞界面 开启burp代理 后随便点击

![image](https://user-images.githubusercontent.com/42985524/179288890-aa77dd4b-b469-4a6c-87f0-34d087c5a493.png)

发现webhook 触发

![image](https://user-images.githubusercontent.com/42985524/179288948-48d19077-04c4-4d72-9b89-33ba7b48d619.png)

5.进入后台观察流量

![image](https://user-images.githubusercontent.com/42985524/179289129-8605aaa7-640e-43d0-828f-2f0ef429af4b.png)

发现确实是请求包的accept头部被插入了恶意payload 触发了远端webhook。证明此来源地址疑似存在log4shell漏洞。

## Update log

...

## Introduce

**Un1kLog4shellScan**是基于 `BurpSuite` 插件 `JavaAPI` 开发的`log4shell漏洞被动扫描`的辅助型插件。

设计本插件的想法是因为在某些攻防演练时,由于需要测试过多漏洞以及需要花费时间在资产收集、口令爆破、代码审计、漏洞利用上,忽视了log4shell的漏洞探测。对每一个请求包进行漏洞探测是不现实的,这需要花费大量的时间,并且网上的插件功能比较单一,基本就是无脑插payload不能够远端配置。

插件通过污染报文中的参数值,被动进行log4shell漏洞探测,已经实现了对CONTENT_TYPE_URL_ENCODED 、CONTENT_TYPE_JSON与CONTENT_TYPE_NONE的数据格式的参数污染,避免因繁琐的渗透测试遗漏对此漏洞的探测挖掘。

同时插件对接本地(或者远程)的api接口,可以实时记录污染报文以及相应场景情况,并可以通过配置设置被动扫描白名单与功能使用范围。插件也可以配置一些参数来控制测试速率与测试payload。

如果使用的是自启动的流量探测服务脚本(server.py),您还可以接入webhook实时告警。

## Installation

1.下载插件jar包文件,或者自行编译源代码。

2.插件装载: `Extender - Extensions - Add - Select File - Next`

3.流量捕获服务端使用nohup或者screen等后台运行方式运行 server.py脚本即可,请提前装好相应依赖。

## Advantages

1. 全参数污染,被动扫描,不会遗漏任何一个请求包的测试参数。
2. 配套web后台管理,方便更新管理。可通过api实现对插件的热加载,与配置切换。
3. 对接Webhook流量补获服务端,可以实时补捉可疑的漏洞场景。

## Details

污染原始请求:

![image](https://user-images.githubusercontent.com/42985524/179278659-5854c9c7-b017-464e-80c5-99bf12d61127.png)

发现漏洞后 webhook通知:

![image](https://user-images.githubusercontent.com/42985524/179278809-674752f3-285b-485d-bfa8-472bf9ee289e.png)

设置白名单域名 防止无脑乱插:

![image](https://user-images.githubusercontent.com/42985524/179278843-b5b903de-89bc-4e93-a5a9-764efe2d2fc1.png)

记录请求详情 用于控制测试间隔、观察污染报文:

![image](https://user-images.githubusercontent.com/42985524/179278886-1ae42530-4624-42ac-9b41-fe8ef1cef661.png)

控制台输出:

![image](https://user-images.githubusercontent.com/42985524/179278915-38adebbd-2971-4ac1-b4b2-cc8642d0f192.png)

污染报文查看:

![image](https://user-images.githubusercontent.com/42985524/179278958-7e08a18f-0d57-413e-96e6-836b4ae72244.png)

