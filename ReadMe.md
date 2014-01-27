### 安装方法（**需要以管理员身份进行**）
* 访问 http://www.winpcap.org/install/default.htm 下载并安装WinPcap（**第2步中的ZIP也含有安装包**）<br />
    * 安装时自启动选项对工具的运行没有影响，因为本工具是直接调用WinPcap的API，不需要经过服务器程序
* 访问 https://github.com/chengr28/pcap_dnsproxy/branches 选择合适的Release版本，并使用GitHub的 Download ZIP（[Release_x86](https://github.com/chengr28/pcap_dnsproxy/archive/Release_x86.zip) 或 [Release_x64](https://github.com/chengr28/pcap_dnsproxy/archive/Release_x64.zip)） 功能将所有文件下载到本地
    * [Release_x86](https://github.com/chengr28/pcap_dnsproxy/tree/Release_x86) 为32位版本，可在任何版本操作系统上使用
    * **[Release_x64](https://github.com/chengr28/pcap_dnsproxy/tree/Release_x64) 为原生64位版本，强烈建议64位操作系统用户使用**
* 打开下载回来的ZIP文件，将 Release_x86/Release_x64 目录解压到磁盘的任意位置
    * 文件夹的名称可以随意更改
* 确定工具文件夹的名称和路径后进入文件夹内，运行 ServiceInstall.bat
    * 此批处理作用为将程序注册系统服务，并进行Windows防火墙测试
    * 进行此步后到第6步之前有错误报告生成为正常现象，请先按照步骤继续进行
* **此时Windows系统会询问是否同意程序访问网络，请将 “专用网络” 以及 “公用网络” 都勾上并确认**
* **运行 ServiceStart.bat 启动服务**
    * 以后每次开机服务都将自动启动
* 打开 网络和共享中心 - 更改适配器设置 选择 本地连接 或 宽带连接（取决于实际使用的网络适配器）
    * 右击 属性 - Internet协议版本4(IPv4) - 属性 - 勾选 “使用下面的DNS服务器地址”
    * **在 首选DNS服务器 内填入 127.0.0.1 确定保存并退出即可**
    * 如果需要使用IPv6协议的本地服务器
        * 请先编辑 Config.ini 的 IPv6 DNS Address 一栏，参见下文 [Config.ini 详细参数说明](https://github.com/chengr28/pcap_dnsproxy/wiki/ReadMe#configini-%E8%AF%A6%E7%BB%86%E5%8F%82%E6%95%B0%E8%AF%B4%E6%98%8E) 一节
        * 右击 属性 - Internet协议版本6(IPv6) - 属性 - 勾选 “使用下面的DNS服务器地址”
        * 在 首选DNS服务器 内填入 ::1 确定保存并退出即可

### 卸载方法（**需要以管理员身份进行**）
* 运行 ServiceUninstall.bat 即可，批处理将直接停止服务并卸载服务
    * 注意Windows防火墙可能会留有允许程序访问网络的信息，故卸载后可能需要使用注册表清理工具清理
    * 转移工具文件夹路径不需要卸载服务
* 按照安装方法中第7步还原网络配置

### 正常工作查看方法
* 打开命令提示符
    * 在开始菜单或直接 Win + R 调出 运行 ，输入 cmd 并回车
    * 开始菜单 - 程序/所有程序 - 附件 - 命令提示符
* 输入 nslookup www.google.com 并回车
* 运行结果应类似：

    >nslookup www.google.com<br />
    服务器:  pcap_dnsproxy.localhost.server（注意：此处由 Config.ini 设置的值确定，参见下文 Config.ini 详细参数说明 一节）
    Address:  127.0.0.1<br />
<br />
    非权威应答:<br />
    &nbsp;&nbsp;&nbsp;&nbsp;名称:    www.google.com<br />
    &nbsp;&nbsp;&nbsp;&nbsp;Addresses: ……（IP地址或地址列表）

* 如非以上结果，请移步 [FAQ 文档](https://github.com/chengr28/pcap_dnsproxy/wiki/FAQ) 中 [运行结果分析](https://github.com/chengr28/pcap_dnsproxy/wiki/FAQ#%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C%E5%88%86%E6%9E%90) 一节

### 注意事项
* 请务必设置一个正确的、有效的、可以正常使用的**境外**DNS服务器！
* 配置文件和错误报告所在的目录以上文 安装方法 一节中第4步注册的服务信息为准，注意填写时一行不要超过2048字节/2KB
* 服务启动前请先**确认没有其它本地DNS服务器运行或本工具多个拷贝在运行中**，否则可能会导致监听冲突无法正常工作
    * 监听冲突会生成错误报告，可留意 Windows socket 相关的错误（参见 [FAQ 文档](https://github.com/chengr28/pcap_dnsproxy/wiki/FAQ) 中 [Error.log 详细错误报告](https://github.com/chengr28/pcap_dnsproxy/wiki/FAQ#errorlog-%E8%AF%A6%E7%BB%86%E9%94%99%E8%AF%AF%E6%8A%A5%E5%91%8A%E9%94%99%E8%AF%AF%E6%8A%A5%E5%91%8A%E4%B8%80%E5%85%B1%E6%9C%895%E5%A4%A7%E7%B1%BB%E5%9E%8B) 一节）
* 杀毒软件/第三方防火墙可能会阻止本程序的操作，请将行为全部允许或将本程序加入到白名单中
* 文件夹内批处理的作用（运行结束会有运行结果，可留意屏幕上的提示）：
    * ServiceInstall - 将程序注册系统服务，并初次启动程序进行Windows防火墙测试
    * ServiceStart - 启动工具服务
    * ServiceQuery - 适用于 Windows XP 以及更旧版本Windows的测试批处理，能测试工具服务是否安装成功
    * ServiceStop - 即时停止工具服务，重启服务时需要先停止服务
    * ServiceUninstall - 停止并卸载工具服务
* **重启服务**
    * 首先运行 ServiceStop.bat
    * 然后运行 ServiceStart.bat 即可
* **更新程序**
    * 如非更新日志有提示，否则更新程序均仅需更新可执行文件即可
    * 先停止服务，并将旧版本的可执行文件删除
    * 将新版本可执行文件放入相同的位置，启动服务即可
* 文件夹和程序的名称可以随意更改，**但请务必在进行安装方法第4步前完成。如果服务注册后需移动工具文件夹的路径，则需要**:
    * 必须先停止工具服务
    * 移动工具文件夹
    * 重复安装方法中的第4步-第6步操作
* 关于请求域名解析的优先级
    * 使用系统API函数进行域名解析（大部分程序）：**系统 Hosts > Pcap_DNSProxy 的 Hosts.ini > 远程DNS服务器**
    * 直接使用网络适配器设置进行域名解析（小部分程序）：Pcap_DNSProxy 的 Hosts.ini > 远程DNS服务器
* 本工具不具有也不会加入域名解析缓存功能，本工具的核心是“代理”也就是转发，缓存功能应由请求者自行解决
* 本工具不具有将请求指向其它主机域名的功能，因其含有缓存的性质，参见上文
* 由于本人水平有限，程序编写难免会出现差错疏漏，如有问题可至项目页面提出，望谅解 v_v

### Config.ini 详细参数说明
有效参数格式为 **选项名称 = 数值/数据**（注意空格和等号的位置）<br />
**注意：Config.ini 只会在工具服务开始时读取，修改本文件的参数后请重启服务（参见上文 [注意事项](https://github.com/chengr28/pcap_dnsproxy/wiki/ReadMe#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9) 一节中的 重启服务）**

* Base - 基本区域
    * Print Error - 错误报告功能：开启为1/关闭为0，默认为1
    * Hosts - Hosts.ini 文件刷新间隔时间：单位为秒，最短间隔时间为5秒，默认为10秒
    * **IPv4 DNS Address - IPv4的DNS服务器地址：只能输入一个地址，默认为 8.8.4.4(Google Public DNS No.2)**
    * IPv6 DNS Address - IPv4的DNS服务器地址：**只能输入一个地址**，默认为空
    * Operation Mode - 程序的工作模式，分 Server/服务器模式 和 Proxy/代理模式：默认为 Server
        * Server/服务器模式：打开DNS通用端口（TCP/UDP同时打开）可为其它设备提供代理域名解析请求服务
        * Proxy/代理模式：只打开回环地址的DNS端口（TCP/UDP同时打开），只能为本机提供代理域名解析请求服务
    * Protocol - 发送请求所使用的协议，分UDP和TCP：默认为UDP
        * **注意：此处所指的协议指的是程序请求远程DNS服务器所使用的协议，而向本程序请求域名解析时可随意使用UDP或者TCP**

* Extend Test - 扩展检测区域
    * IPv4 TTL - 接受请求的远程DNS服务器数据报的TTL值：0为自动获取，取值为1-255之间：默认为0
    * IPv6 Hop Limits - 接受请求的远程DNS服务器数据报的 Hop Limits 值：0为自动获取，取值为1-255之间，默认为0
    * Hop Limits/TTL Fluctuation - IPv4 TTL/IPv6 Hop Limits 可接受范围，即 IPv4 TTL/IPv6 Hop Limits 的值±数值的范围内的数据包均可被接受，用于避免网络环境短暂变化造成解析失败的问题：取值为1-255之间，默认为2
    * IPv4 Options Filter - IPv4数据包头检测：开启为1/关闭为0，默认为1
    * ICMP Test - ICMP/Ping测试间隔时间：单位为秒，最短间隔时间为5秒，默认为900秒/15分钟
    * ICMP ID - ICMP/Ping数据包头部ID的值：格式为 0x**** 的十六进制字符，如果留空则获取线程的ID作为请求用ID，默认为空
    * ICMP Sequence - ICMP/Ping数据包头部Sequence/序列号的值：格式为 0x**** 的十六进制字符，如果留空则为 0x0001 ，默认为空
    * TCP Options Filter - TCP数据包头检测；开启为1/关闭为0，默认为0
        * 注意：此选项只能在程序工作模式为TCP下才能使用，否则会报错；而程序工作模式为TCP时则强烈建议开启
    * DNS Options Filter - DNS数据包头检测：开启为1/关闭为0，默认为1
    * Blacklist Filter - 解析结果黑名单过滤：开启为1/关闭为0，默认为1

* Data - 数据区域
    * Domain Test - DNS服务器解析域名测试：请输入正确、确认不会被投毒污染的域名并且不要超过253字节ASCII数据；留空则会随机生成一个域名进行测试，默认为空
    * Domain Test ID - DNS数据包头部ID的值：格式为 0x**** 的十六进制字符，如果留空则为 0x0001 ，默认为空
    * Domain Test Speed - DNS服务器解析域名测试间隔时间：单位为秒，最短间隔时间为5秒，默认为900秒/15分钟
    * ICMP PaddingData - ICMP附加数据，Ping程序发送请求时为补足数据使其达到Ethernet类型网络最低的可发送长度时添加的数据：长度介乎于18字节-64字节ASCII数据之间，留空则使用 Microsoft Windows Ping 程序的ICMP附加数据，默认为空
    * Localhost Server Name - 本地DNS服务器名称：请输入正确的域名并且不要超过253字节ASCII数据；留空则使用 **pcap_dnsproxy.localhost.server** 作为本地服务器名称，默认为空

### Hosts.ini 格式说明
有效参数格式为 **地址(|地址A|地址B) 正则表达式**（括号内为可选项目，注意间隔所在的位置）

* 需要注释请在条目开头添加 #/井号
* **地址与正则表达式之间的间隔字符可为Space/半角空格或者HT/水平定位符号，间隔长度不限，但切勿输入全角空格**
* 一条条目的总长度切勿超过2048字节/2KB
* **一条条目只能接受一种地址类型（IPv4/IPv6），如有同一个域名需要同时进行IPv4/IPv6的Hosts，请分为两个条目输入**
* **优先级别自上而下递减，条目越前优先级越高**
* **平行地址原理为一次返回多个记录，而具体使用哪个记录则由请求者决定，一般为第1个**

##### 例如有一个 Hosts.ini 的有效数据区域是这样的：<br />
    127.0.0.1|127.0.0.2|127.0.0.3 0*.test.localhost
    127.0.0.4|127.0.0.5|127.0.0.6 0*.localhost
    ::1|::2|::3	0*.test.localhost
    ::4|::5|::6	0*.localhost

##### 虽然 `0*.localhost` 包含了 `0*.test.localhost` 但是由于优先级别自上而下递减，故先命中 `0*.test.localhost` 并直接返回，不会再进行其它检查
    请求解析 xxx.localhost 的A记录（IPv4）会返回 127.0.0.4、127.0.0.5和127.0.0.6
    请求解析 xxx.localhost 的AAAA记录（IPv6）会返回 ::4、::5和::6
    请求解析 xxx.test.localhost 的A记录（IPv4）会返回 127.0.0.1、127.0.0.2和127.0.0.3
    请求解析 xxx.test.localhost 的AAAA记录（IPv6）会返回 ::1、::2和::3