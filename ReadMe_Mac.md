### 安装方法

* 访问 https://github.com/chengr28/pcap_dnsproxy/branches 随便选择一个Release版本，并使用GitHub的 Download ZIP 功能将所有文件下载到本地
    * Mac的Release版本为 Intel x86/x64 Universal 格式，32位和64位系统均可直接使用
* 打开下载回来的ZIP文件，将 Mac 目录解压到磁盘的任意位置
    * 目录和程序的名称可以随意更改
* 打开 `PcapDNSProxyService.plist`
    * 在 `<key>Program</key>` 下面的 `<string> </string>` 标签内填入程序所在的完整路径+程序名称（不含空格）
    * 在 `<key>WorkingDirectory</key>` 下面的 `<string> </string>` 标签内填入程序所在的完整路径（不含空格）
* 将 `PcapDNSProxyService.plist` 复制到 `/Library/LaunchDaemons` 目录内（需要 root 权限）
* 打开 `终端` ：
    * 使用 `sudo -i` 获得 root 权限，然后使用 `cd /Library/LaunchDaemons` 进入自启动守护进程目录
    * 使用 `chown root PcapDNSProxyService.plist` 将其所有者更改为 root
    * 使用 `launchctl load PcapDNSProxyService.plist` 装载并启动守护进程
    * 以后每次开机在登录前守护进程都将自动启动
* 打开 `系统偏好设置` 窗口
    * 进入 `网络` 部分
    * 选中使用的网络适配器，点击 `高级` 按钮
    * 切换到 `DNS` 选项卡，并点击 `DNS服务器` 下的 `+` 号
    * 输入 `127.0.0.1(IPv4)/::1(IPv6)`
    * 按 `好` 再按 `应用` 即可

### 卸载方法（**整个过程均需以 root 权限进行**）

* 还原系统网络配置
* 打开终端
* 使用 `sudo -i` 命令获得 root 权限，然后使用 `cd /Library/LaunchDaemons` 进入自启动守护进程目录
* 使用 `launchctl unload PcapDNSProxyService.plist` 停止并卸载守护进程
* 使用 `rm PcapDNSProxyService.plist` 命令或直接将 `PcapDNSProxyService.plist` 拖入废纸篓删除（需要 root 权限）
* 删除所有 Pcap_DNSProxy(Mac) 相关文件

### 正常工作查看方法
* 打开终端
* 输入 `dig www.google.com` 并回车
* 运行结果应类似：<br />
<br />
    >dig www.google.com<br />
    ; (1 server found)<br />
    ;; global options: +cmd<br />
    ;; Got answer:<br />
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: ...<br />
    ;; flags: ...; QUERY: ..., ANSWER: ..., AUTHORITY: ..., ADDITIONAL: ...<br />
<br />
    ;; QUESTION SECTION:<br />
    ;www.google.com.            IN    A<br />
<br />
    ;; ANSWER SECTION:<br />
    ...<br />
<br />
    ;; Query time: ... msec<br />
    ;; SERVER: 127.0.0.1#53(127.0.0.1)（IPv4，IPv6下为 ::1 ）<br />
    ;; WHEN: ...<br />
    ;; MSG SIZE  rcvd: ...<br />
<br />
* 如非以上结果，请移步 [[Mac 版 FAQ 文档|https://github.com/chengr28/pcap_dnsproxy/wiki/FAQ_Mac]]中 运行结果分析 一节

### 注意事项
* 请务必设置一个正确的、有效的、可以正常使用的境外DNS服务器！
* 配置文件和错误报告所在的目录以上文 安装方法 一节中第3步注册的服务信息为准，注意填写时一行不要超过2048字节/2KB
* 服务启动前请先确认没有其它本地DNS服务器运行或本工具多个拷贝在运行中，否则可能会导致监听冲突无法正常工作
    * 监听冲突会生成错误报告，可留意 Socket 相关的错误（参见 [[Linux 版 FAQ 文档|https://github.com/chengr28/pcap_dnsproxy/wiki/FAQ_Linux]]中 Error.log 详细错误报告 一节）
* 杀毒软件/第三方防火墙可能会阻止本程序的操作，请将行为全部允许或将本程序加入到白名单中
* 重启服务
    * 打开终端，使用 `sudo -i` 命令获得 root 权限
    * 使用 `cd /Library/LaunchDaemons` 进入自启动守护进程目录
    * 使用 `launchctl unload PcapDNSProxyService.plist` 停止守护进程
    * 使用 `launchctl load PcapDNSProxyService.plist` 启动守护进程
* 更新程序
    * 如非更新日志有需要更新配置文件/Hosts文件的提示，否则更新程序均仅需重新编译可执行程序
        * 如更新日志有需要更新配置文件/Hosts文件的提示，请将这2个文件内的运行参数/Hosts记录下来并填入新的配置文件/Hosts文件内
        * 注意切勿直接覆盖，否则可能会造成错误
    * 先停止服务，并将旧版可执行文件删除
        * 打开终端，使用 `sudo -i` 命令获得 root 权限
        * 使用 `cd /Library/LaunchDaemons` 进入自启动守护进程目录
        * 使用 `launchctl unload PcapDNSProxyService.plist` 停止守护进程
    * 将新版本的 可执行文件 放入相同的位置
    * 启动服务
        * 打开终端，使用 `sudo -i 命令获得 root 权限
        * 使用 `cd /Library/LaunchDaemons` 进入自启动守护进程目录
        * 使用 `launchctl load PcapDNSProxyService.plist` 启动守护进程
* 文件夹和程序的名称可以随意更改，但请务必在进行安装方法第3步前完成。如果服务注册后需移动工具文件夹的路径，则需要:
    * 停止服务
    * 移动工具文件夹
    * 重复 安装方法 中的第3步操作
* 安装/卸载某些软件可能会导致网络设配器离线使LibPcap模块返回，网络设配器离线又重新启动后请重启服务
* 关于请求域名解析的优先级
    * 使用系统API函数进行域名解析（大部分）：系统 Hosts > Pcap_DNSProxy 的 Hosts.ini（Whitelist/白名单条目 > Local Hosts/境内DNS解析域名列表 > Hosts/主要Hosts列表） > 远程DNS服务器
    * 直接使用网络适配器设置进行域名解析（小部分）：Pcap_DNSProxy 的 Hosts.ini（Whitelist/白名单条目 > Local Hosts/境内DNS解析域名列表 > Hosts/主要Hosts列表） > 远程DNS服务器
* **Config.conf 详细参数 和 Hosts.conf 格式说明 和Windows版一样**，参见主 [ReadMe](https://github.com/chengr28/pcap_dnsproxy/wiki/ReadMe) 文档