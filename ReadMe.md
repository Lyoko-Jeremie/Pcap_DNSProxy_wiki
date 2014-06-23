### 安装方法（**需要以管理员身份进行**）
* 访问 http://www.winpcap.org/install/default.htm 下载并安装WinPcap（ **第2步中的ZIP也含有安装包** ）<br />
    * 安装时自启动选项对工具的运行没有影响，因为本工具直接调用 WinPcap API，不需要经过服务器程序
* 访问 https://github.com/chengr28/pcap_dnsproxy/branches **随便选择一个Release版本**，并使用GitHub的 `Download ZIP`（[Release_x86](https://github.com/chengr28/pcap_dnsproxy/archive/Release_x86.zip) 或 [Release_x64](https://github.com/chengr28/pcap_dnsproxy/archive/Release_x64.zip)） 功能将所有文件下载到本地
    * Windows 下批处理会自动判断 x64 和 x86 版本
* 打开下载回来的ZIP文件，将 Main 目录解压到磁盘的任意位置
    * 目录和程序的名称可以随意更改
* 确定工具文件夹的名称和路径后进入文件夹内，运行 `ServiceInstall.bat`
    * 此批处理作用为将程序注册系统服务，并进行Windows防火墙测试
    * 以后每次开机服务都将自动启动
* **此时Windows系统会询问是否同意程序访问网络，请将 “专用网络” 以及 “公用网络” 都勾上并确认**
* 打开 `网络和共享中心` - `更改适配器设置` 选择 `本地连接` 或 `宽带连接`（取决于实际使用的网络适配器）
    * 右击 `属性` - `Internet协议版本4(IPv4)` - 属性 - 勾选 `使用下面的DNS服务器地址`
    * **在 `首选DNS服务器` 内填入 `127.0.0.1` 确定保存并退出即可**
    * 如果需要使用IPv6协议的本地服务器
        * 请先编辑配置文件的 `IPv6 DNS Address` 一栏，参见下文 配置文件详细参数说明 一节
        * 右击 `属性` - `Internet协议版本6(IPv6)` - `属性` - 勾选 `使用下面的DNS服务器地址`
        * 在 `首选DNS服务器` 内填入 `::1` 确定保存并退出即可

### 卸载方法（**需要以管理员身份进行**）
* 按照安装方法中第6步还原网络配置
* 运行 `ServiceUninstall.bat` 即可，批处理将直接停止服务并卸载服务
    * 注意Windows防火墙可能会留有允许程序访问网络的信息，故卸载后可能需要使用注册表清理工具清理
    * 转移工具文件夹路径不需要卸载服务

### 正常工作查看方法
* 打开命令提示符
    * 在开始菜单或直接 `Win + R` 调出 `运行` ，输入 `cmd` 并回车
    * 开始菜单 - `程序/所有程序` - `附件` - `命令提示符`
* 输入 `nslookup www.google.com` 并回车
* 运行结果应类似：

    >nslookup www.google.com<br />
    服务器:  pcap-dnsproxy.localhost.server（注意：此处由配置文件设置的值确定，参见下文 配置文件详细参数说明 一节）
    Address:  127.0.0.1<br />
<br />
    非权威应答:<br />
    &nbsp;&nbsp;&nbsp;&nbsp;名称:    www.google.com<br />
    &nbsp;&nbsp;&nbsp;&nbsp;Addresses: ……（IP地址或地址列表）

* 如非以上结果，请移步 [FAQ 文档](https://github.com/chengr28/pcap_dnsproxy/wiki/FAQ) 中 运行结果分析 一节

### 注意事项
* 如果程序启动提示丢失 wpcap.dll 请重新安装WinPcap或者将其更新到最新版本
* 请务必设置一个正确的、有效的、可以正常使用的 **境外** DNS服务器！
* 配置文件和错误报告所在的目录以上文 安装方法 一节中第4步注册的服务信息为准，注意填写时一行不要超过4096字节/4KB
* 服务启动前请 **先确认没有其它本地DNS服务器运行或本工具多个拷贝运行中**，否则可能会导致监听冲突无法正常工作
    * 监听冲突会生成错误报告，可留意 Windows Socket 相关的错误（参见 [FAQ 文档](https://github.com/chengr28/pcap_dnsproxy/wiki/FAQ)中 Error.log 详细错误报告 一节）
* 杀毒软件/第三方防火墙可能会阻止本程序的操作，请将行为全部允许或将本程序加入到白名单中
* 关于后台服务
    * 文件夹内批处理的作用（运行结束会有运行结果）：
        * ServiceInstall - 将程序注册系统服务，并初次启动程序进行Windows防火墙测试
            * 运行结束时会显示 `Done. Please confirm the PcapDNSProxyService service had been installed.` 但具体是否成功需要留意屏幕上的提示
        * ServiceStart - 启动工具服务
            * 运行结束时会显示 `Done. Please confirm the PcapDNSProxyService service had been started.` 但具体是否成功需要留意屏幕上的提示
        * ServiceQuery - 适用于 Windows XP 以及更旧版本Windows的测试批处理，能测试工具服务是否安装成功
        * ServiceStop - 即时停止工具服务，重启服务时需要先停止服务
            * 运行结束时会显示 `Done. Please confirm the PcapDNSProxyService service had been stopped.` 但具体是否成功需要留意屏幕上的提示
        * ServiceUninstall - 停止并卸载工具服务
            * 运行结束时会显示 `Done. Please confirm the PcapDNSProxyService service had been deleted.` 但具体是否成功需要留意屏幕上的提示
    * 重启服务
        * 首先运行 `ServiceStop.bat`
        * 然后运行 `ServiceStart.bat` 即可
* 更新程序
    * 切勿直接覆盖，否则可能会造成错误！
    * 备份好所有配置文件的自定义内容
    * 运行 ServiceStop.bat 停止服务
    * 将整个 Pcap_DNSProxy 目录删除
    * 将新版本的 Pcap_DNSProxy 目录放入相同的位置
    * 将配置文件的自定义内容加回新版本配置文件里相应的区域内
    * 运行 ServiceStart.bat 启动服务
* 文件夹和程序的名称可以随意更改，**但请务必在进行安装方法第4步前完成。如果服务注册后需移动工具文件夹的路径，则需要**:
    * 必须先停止工具服务
    * 移动工具文件夹
    * 重复安装方法中的第4步-第6步操作
* 关于请求域名解析的优先级
  * 使用系统API函数进行域名解析（大部分）：系统 Hosts > Pcap_DNSProxy 的 Hosts 条目（Whitelist/白名单条目 > Local Hosts/境内DNS解析域名列表 > Hosts/主要Hosts列表） > 远程DNS服务器
  * 直接使用网络适配器设置进行域名解析（小部分）：Pcap_DNSProxy 的 Hosts.ini（Whitelist/白名单条目 > Local Hosts/境内DNS解析域名列表 > Hosts/主要Hosts列表） > 远程DNS服务器
  * 请求远程DNS服务器的优先级：Hosts Only 模式 > TCP模式的DNSCurve 加密/非加密模式（如有） > UDP模式的DNSCurve 加密/非加密模式（如有） > TCP模式普通请求（如有） > UDP模式普通请求
* 由于本人水平有限，程序编写难免会出现差错疏漏，如有问题可至项目页面提出，望谅解 v_v

### 配置文件详细参数说明
有效参数格式为 **`选项名称 = 数值/数据`**（注意空格和等号的位置）<br />
**注意：配置文件只会在工具服务开始时读取，修改本文件的参数后请重启服务（参见上文 注意事项 一节中的 重启服务）**

有效参数格式为 "选项名称 = 数值/数据"（不含引号，注意空格和等号的位置）
注意：配置文件只会在工具服务开始时读取，修改本文件的参数后请重启服务（参见上文 注意事项 一节中的 重启服务）

* `Base` - 基本参数区域
  * `Version` - 配置文件的版本，用于正确识别配置文件：本参数与程序版本号不相关，切勿修改，默认为发布时的最新配置文件版本
  * `Print Error` - 错误报告功能：开启为1/关闭为0，默认为1
  * `File Refresh Time` - 文件刷新间隔时间：单位为秒，最短间隔时间为5秒，默认为10秒
  * `File Hash` - 文件 Hash 功能，开启此功能能降低刷新文件时的CPU占用：开启为1/关闭为0，默认为1

* `DNS` - 域名解析参数区域
  * `Protocol` - **发送请求所使用的协议，分 `UDP` 和 `TCP`**：默认为 UDP
    * 注意：此处所指的协议指的是程序请求远程DNS服务器所使用的协议，而向本程序请求域名解析时可随意使用 UDP 或 TCP
  * `Hosts Only` - Hosts Only 模式，启用后将不进行任何数据包过滤，只适用本工具的 Hosts 功能：开启为1/关闭为0，默认为0
  * `Cache Type` - DNS缓存的类型：分 `Timer`/计时型以及 `Queue`/队列型
  * `Cache Parameter` - DNS缓存的参数：`Timer`/计时型 时为时间长度，`Queue`/队列型 时为队列长度
  * `Operation Mode` - 程序的监听工作模式，分 `Server`/服务器模式、`Private`/私有网络模式 和 `Proxy`/代理模式：默认为 Private
    * `Server`/服务器模式：打开DNS通用端口（TCP/UDP同时打开），可为所有其它设备提供代理域名解析请求服务
	* `Private`/私有网络模式：打开DNS通用端口（TCP/UDP同时打开），可为仅限于私有网络地址的设备提供代理域名解析请求服务
	* `Proxy`/代理模式：只打开回环地址的DNS端口（TCP/UDP同时打开），只能为本机提供代理域名解析请求服务
	* `Custom`/自定义模式：打开DNS通用端口（TCP/UDP同时打开），可用的地址由 IPFilter 参数决定
  * `Listen Port` - 监听端口，本地监听请求的端口：可填入 1-65535 之间的端口，如果留空则为53，默认为空
  * `IPFilter Type` - IPFilter 参数的类型：分为 `Deny` 禁止和 `Permit` 允许，对应 IPFilter 参数应用为黑名单或白名单，默认为 Deny
  * `IPFilter Level` - IPFilter 参数的过滤级别，级别越高过滤越严格，与 IPFilter 条目相对应：0为不启用过滤，如果留空则为0，默认为空
  
* `Addresses` - 普通模式地址区域
  * `IPv4 DNS Address` - **IPv4主要DNS服务器地址：需要输入一个带端口格式的地址**，默认为 `8.8.4.4:53`(Google Public DNS No.2)
  * `IPv4 Alternate DNS Address` - IPv4备用DNS服务器地址：需要输入一个带端口格式的地址，默认为 `8.8.8.8:53`(Google Public DNS No.1)
  * `IPv4 Local DNS Address` - IPv4主要境内DNS服务器地址，用于境内域名解析：需要输入一个带端口格式的地址，默认为 `114.114.115.115:53`(114 DNS No.2)
  * `IPv4 Local Alternate DNS Address` - IPv4备用境内DNS服务器地址，用于境内域名解析：需要输入一个带端口格式的地址，默认为 `114.114.114.114:53`(114 DNS No.1)
  * `IPv6 DNS Address` - IPv6主要DNS服务器地址：需要输入一个带端口格式的地址，留空为不启用，默认为空
    * IPv6的格式为 "[地址]:端口"（不含引号）
  * `IPv6 Alternate DNS Address` - IPv6备用DNS服务器地址：需要输入一个带端口格式的地址，留空为不启用，默认为空
     * IPv6的格式为 "[地址]:端口"（不含引号）
  * `IPv6 Local DNS Address` - IPv6主要境内DNS服务器地址，用于境内域名解析：需要输入一个带端口格式的地址，留空为不启用，默认为空
     * IPv6的格式为 "[地址]:端口"（不含引号）
  * `IPv6 Local Alternate DNS Address` - IPv6备用境内DNS服务器地址，用于境内域名解析：需要输入一个带端口格式的地址，留空为不启用，默认为空
     * IPv6的格式为 "[地址]:端口"（不含引号）

* `Values` - 扩展参数值区域
  * `EDNS0 Payload Size` - EDNS0 标签附带使用的最大载荷长度：最小为DNS协议实现要求的512(bytes)，留空则使用 EDNS0 标签要求最短的1220(bytes)，默认为留空
  * `IPv4 TTL` - IPv4主要DNS服务器接受请求的远程DNS服务器数据包的TTL值：0为自动获取，取值为 1-255 之间：默认为0
  * `IPv6 Hop Limits` - IPv6主要DNS服务器接受请求的远程DNS服务器数据包的 Hop Limits 值：0为自动获取，取值为 1-255 之间，默认为0
  * `IPv4 Alternate TTL` - IPv4备用DNS服务器接受请求的远程DNS服务器数据包的TTL值：0为自动获取，取值为 1-255 之间：默认为0
  * `IPv6 Alternate Hop Limits` - IPv6备用DNS服务器接受请求的远程DNS服务器数据包的 Hop Limits 值：0为自动获取，取值为 1-255 之间，默认为0
  * `Hop Limits Fluctuation` - IPv4 TTL/IPv6 Hop Limits 可接受范围，即 IPv4 TTL/IPv6 Hop Limits 的值±数值的范围内的数据包均可被接受，用于避免网络环境短暂变化造成解析失败的问题：取值为 1-255 之间，默认为2
  * `Alternate Times` - 备用服务器失败次数阈值，一定周期内如超出阈值会触发服务器切换：最小为10次，默认为10次
  * `Alternate Time Range` - 备用服务器失败次数阈值计算周期：单位为秒，默认为60秒/1分钟
  * `Alternate Reset Time` - 备用服务器重置切换时间，切换产生后经过次事件会切换回主要服务器：单位为秒，默认为300秒/5分钟
  * `ICMP Test` - ICMP/Ping测试间隔时间：单位为秒，最短间隔时间为5秒，最长为5位数，默认为900秒/15分钟
  * `Domain Test` - DNS服务器解析域名测试间隔时间：单位为秒，最短间隔时间为5秒，最长为5位数，默认为900秒/15分钟

* `Switches` - 控制开关区域
  * `EDNS0 Label` - EDNS0 标签支持，开启后将为所有请求添加 EDNS0 标签：开启为1/关闭为0，默认为0
  * `DNSSEC Request` - DNSSEC 请求，开启后将尝试为所有请求添加 DNSSEC 请求：开启为1/关闭为0，默认为0
    * 注意：此功能为实验性质，本程序不具备任何验证 DNSSEC 回复的能力，单独开启此功能并不能避免DNS投毒污染的问题
  * `IPv4 Data Filter` - IPv4数据包头检测：开启为1/关闭为0，默认为1
  * `TCP Data Filter` - TCP数据包头检测；开启为1/关闭为0，默认为1
    * 注意：此选项只能在程序工作模式为TCP下才能使用，非TCP模式时此参数无效
  * `DNS Data Filter` - DNS数据包头检测：开启为1/关闭为0，默认为1
  * `Blacklist Filter` - 解析结果黑名单过滤：开启为1/关闭为0，默认为1
  
* `Data` - 数据区域
  * `ICMP ID` - ICMP/Ping数据包头部ID的值：格式为 `0x***` 的十六进制字符，如果留空则获取线程的ID作为请求用ID，默认为空
  * `ICMP Sequence` - ICMP/Ping数据包头部Sequence/序列号的值：格式为 `0x****` 的十六进制字符，如果留空则为 `0x0001` ，默认为空
  * `Domain Test Data` - DNS服务器解析域名测试：请输入正确、确认不会被投毒污染的域名并且不要超过253字节ASCII数据，留空则会随机生成一个域名进行测试，默认为空
  * `Domain Test ID` - DNS数据包头部ID的值：格式为 `0x****` 的十六进制字符，如果留空则为 `0x0001` ，默认为空
  * `ICMP PaddingData` - ICMP附加数据，Ping程序发送请求时为补足数据使其达到Ethernet类型网络最低的可发送长度时添加的数据：长度介乎于 18字节 - 1512字节 ASCII数据之间，留空则使用 Microsoft Windows Ping 程序的ICMP附加数据，默认为空
  * `Localhost Server Name` - 本地DNS服务器名称：请输入正确的域名并且不要超过253字节ASCII数据，留空则使用 pcap-dnsproxy.localhost.server 作为本地服务器名称，默认为空

* `DNSCurve` - DNSCurve 协议基本参数区域
  * `DNSCurve` - **DNSCurve 协议总开关，控制所有和 DNSCurve 协议有关的选项**：开启为1/关闭为0，默认为0
  * `DNSCurve Protocol` - 发送请求所使用的协议，分 UDP 和 TCP：默认为 UDP
  * `DNSCurve Payload Size` - DNSCurve EDNS0 标签附带使用的最大载荷长度，同时亦为发送请求的总长度，并决定请求的填充长度：最小为DNS协议实现要求的512(bytes)，留空则为512(bytes)，默认为留空
  * `Encryption` - 启用加密，DNSCurve 协议支持加密和非加密模式：开启为1/关闭为0，默认为1
  * `Encryption Only` - 只使用加密模式：开启为1/关闭为0，默认为1
    * 注意：使用 只使用加密模式 时必须提供服务器的魔数和指纹
  * `Key Recheck Time` - DNSCurve 协议DNS服务器连接信息检查间隔：单位为秒，最短为10秒，默认为3600秒/1小时
  
* `DNSCurve Addresses` - DNSCurve 协议地址区域
  * `DNSCurve IPv4 DNS Address` - DNSCurve 协议IPv4主要DNS服务器地址：需要输入一个带端口格式的地址，默认为 `208.67.220.220:443`(OpenDNS No.2)
  * `DNSCurve IPv4 Alternate DNS Address` - DNSCurve 协议IPv4备用DNS服务器地址：需要输入一个带端口格式的地址，默认为 `208.67.222.222:443`(OpenDNS No.1)
  * `DNSCurve IPv6 DNS Address` - DNSCurve 协议IPv6主要DNS服务器地址：需要输入一个带端口格式的地址，默认为空
  * `DNSCurve IPv6 Alternate DNS Address` - DNSCurve 协议IPv6备用DNS服务器地址：需要输入一个带端口格式的地址，默认为空
  * `DNSCurve IPv4 Provider Name` - DNSCurve 协议IPv4主要DNS服务器提供者：请输入正确的域名并且不要超过253字节ASCII数据，默认为 `2.dnscrypt-cert.opendns.com`(OpenDNS)
    * 注意：自动获取 DNSCurve 服务器连接信息时必须输入提供者的域名，不能留空
  * `DNSCurve IPv4 Alternate Provider Name` - DNSCurve 协议IPv4备用DNS服务器提供者：请输入正确的域名并且不要超过253字节ASCII数据，默认为 `2.dnscrypt-cert.opendns.com`(OpenDNS)
    * 注意：自动获取 DNSCurve 服务器连接信息时必须输入提供者的域名，不能留空
  * `DNSCurve IPv6 Provider Name` - DNSCurve 协议IPv6主要DNS服务器提供者：输入正确的域名并且不要超过253字节ASCII数据，默认为空
    * 注意：自动获取 DNSCurve 服务器连接信息时必须输入提供者的域名，不能留空
  * `DNSCurve IPv6 Provider Name` - DNSCurve 协议IPv6备用DNS服务器提供者：请输入正确的域名并且不要超过253字节ASCII数据，默认为空
    * 注意：自动获取 DNSCurve 服务器连接信息时必须输入提供者的域名，不能留空

* `DNSCurve Keys` - DNSCurve 协议密钥区域
  * `Client Public Key` - 自定义客户端公钥：可使用 KeyPairGenerator 生成，留空则每次启动时自动生成，默认为空
  * `Client Secret Key` - 自定义客户端私钥：可使用 KeyPairGenerator 生成，留空则每次启动时自动生成，默认为空
  * `IPv4 DNS Public Key` - DNSCurve 协议IPv4主要DNS服务器验证用公钥，默认为 `B735:1140:206F:225D:3E2B:D822:D7FD:691E:A1C3:3CC8:D666:8D0C:BE04:BFAB:CA43:FB79`(OpenDNS)
  * `IPv4 Alternate DNS Public Key` - DNSCurve 协议IPv4备用DNS服务器验证用公钥，默认为 `B735:1140:206F:225D:3E2B:D822:D7FD:691E:A1C3:3CC8:D666:8D0C:BE04:BFAB:CA43:FB79`(OpenDNS)
  * `IPv6 DNS Public Key` - DNSCurve 协议IPv6主要DNS服务器验证用公钥，默认为空
  * `IPv6 Alternate DNS Public Key` - DNSCurve 协议IPv6备用DNS服务器验证用公钥，默认为空
  * `IPv4 DNS Fingerprint` - DNSCurve 协议IPv4主要DNS服务器传输用指纹，留空则自动通过服务器提供者和公钥获取，默认为空
  * `IPv4 Alternate DNS Fingerprint` - DNSCurve 协议IPv4备用DNS服务器传输用指纹，留空则自动通过服务器提供者和公钥获取，默认为空
  * `IPv6 DNS Fingerprint` - DNSCurve 协议IPv6备用DNS服务器传输用指纹，留空则自动通过服务器提供者和公钥获取，默认为空
  * `IPv6 Alternate DNS Fingerprint` - DNSCurve 协议IPv6备用DNS服务器传输用指纹，留空则自动通过服务器提供者和公钥获取，默认为空
  
* `DNSCurve Magic Number` - DNSCurve 协议魔数区域
  * `IPv4 Receive Magic Number` - DNSCurve 协议IPv4主要DNS服务器接收魔数：长度必须为8字节，留空则使用程序内置的接收魔数，默认留空
  * `IPv4 Alternate Receive Magic Number` - DNSCurve 协议IPv4备用DNS服务器接收魔数：长度必须为8字节，留空则使用程序内置的接收魔数，默认留空
  * `IPv6 Receive Magic Number` - DNSCurve 协议IPv6主要DNS服务器接收魔数：长度必须为8字节，留空则使用程序内置的接收魔数，默认留空
  * `IPv6 Alternate Receive Magic Number` - DNSCurve 协议IPv6备用DNS服务器接收魔数：长度必须为8字节，留空则使用程序内置的接收魔数，默认留空
  * `IPv4 DNS Magic Number` - DNSCurve 协议IPv4主要DNS服务器发送魔数：长度必须为8字节，留空则自动获取，默认留空
  * `IPv4 Alternate DNS Magic Number - DNSCurve 协议IPv4备用DNS服务器发送魔数：长度必须为8字节，留空则自动获取，默认留空
  * `IPv6 DNS Magic Number` - 协议IPv6主要DNS服务器发送魔数：长度必须为8字节，留空则自动获取，默认留空
  * `IPv6 Alternate DNS Magic Number` - DNSCurve 协议IPv6备用DNS服务器发送魔数：长度必须为8字节，留空则自动获取，默认留空

### Hosts 文件格式说明
**Hosts配置文件分为`Base`/基本区域、`Hosts`/主要Hosts列表 和 `Local Hosts`/境内DNS解析域名列表 三个区域**<br />
* 部分区域通过标签识别，切勿将其删除
* Local Hosts/境内DNS解析域名列表 的优先级比 Hosts/主要Hosts列表 高，Whitelist/白名单条目 的优先级由位置决定，参见下文
* 一条条目的总长度切勿超过4096字节/4KB
* 需要注释请在条目开头添加 #/井号
* 优先级别自上而下递减，条目越前优先级越高

#### `Base` - 基本参数区域
  * `Version` - 配置文件的版本，用于正确识别 Hosts 文件：本参数与程序版本号不相关，切勿修改，默认为发布时的最新配置文件版本
  * `Default TTL` - Hosts 条目默认生存时间：单位为秒，留空则为900秒/15分钟，默认为空

#### `Whitelist` - 白名单条目
**此类型的条目列出的符合要求的域名会直接绕过Hosts，不会使用Hosts功能**<br />
**直接在条目前添加 `NULL` 即可，有效参数格式为 `NULL 正则表达式`**<br />
###### 注意优先级的问题，例如有一片含白名单条目的区域：<br />
    NULL .*\.test\.localhost
    127.0.0.1|127.0.0.2|127.0.0.3 .*\.localhost

###### 虽然 `.*\.localhost` 包含了 `.*\.test\.localhost` 但由于优先级别自上而下递减，故先命中 `.*\.test\.localhost` 并返回使用远程服务器解析，从而绕过了下面的条目不使用Hosts的功能

#### `Hosts` - 主要Hosts列表
**有效参数格式为 `地址(|地址A|地址B) 域名的正则表达式`（括号内为可选项目，注意间隔所在的位置）**<br />
* 地址与正则表达式之间的间隔字符可为Space/半角空格或者HT/水平定位符号，间隔长度不限，但切勿输入全角空格
* 一条条目只能接受一种地址类型（IPv4/IPv6），如有同一个域名需要同时进行IPv4/IPv6的Hosts，请分为两个条目输入
* 平行地址原理为一次返回多个记录，而具体使用哪个记录则由请求者决定，一般为第1个<br />

###### 例如有一个 [Hosts] 下有效数据区域：
    127.0.0.1|127.0.0.2|127.0.0.3 .*\.test\.localhost
    127.0.0.4|127.0.0.5|127.0.0.6 .*\.localhost
    ::1|::2|::3	.*\.test.localhost
    ::4|::5|::6	.*\.localhost

###### 虽然 `.*\.localhost` 包含了 `.*\.test\.localhost` 但是由于优先级别自上而下递减，故先命中 `.*\.test\.localhost` 并直接返回，不会再进行其它检查
    请求解析 xxx.localhost 的A记录（IPv4）会返回 127.0.0.4、127.0.0.5和127.0.0.6
    请求解析 xxx.localhost 的AAAA记录（IPv6）会返回 ::4、::5和::6
    请求解析 xxx.test.localhost 的A记录（IPv4）会返回 127.0.0.1、127.0.0.2和127.0.0.3
    请求解析 xxx.test.localhost 的AAAA记录（IPv6）会返回 ::1、::2和::3

#### `Local Hosts` - 境内DNS解析域名列表
本区域数据用于为域名使用境内DNS服务器解析提高访问速度，使用时请确认境内DNS服务器地址不为空（参见上文 配置文件详细参数说明 一节）<br />
**有效参数格式为 `域名的正则表达式`**<br />
* 本功能不会对境内DNS服务器回复进行任何过滤的措施，请确认本区域填入的数据不会受到DNS投毒污染干扰<br />

###### 例如有一个 [Local Hosts] 下有效数据区域：<br />

    .*\.test\.localhost
    .*\.localhost

###### 即所有符合以上正则表达式的域名请求都将使用境内DNS服务器解析