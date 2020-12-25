Clash for Windows 文档
链接 https://docs.cfw.lbyczf.com/

TAP 模式
对于 0.13.8 及以后版本，更推荐使用TUN 模式

安装 TAP 网卡
点击General页面中TAP Device选项的Manage按钮，在弹出对话框中选择Install将会安装 TAP 网卡，此网卡用于接管系统流量，安装完成可在系统网络连接中看到名为cfw-tap的网卡

启动 TAP 模式
使用的 Profile 中包含 listen 设置：

dns:
  enable: true
  enhanced-mode: redir-host # 或 fake-ip
  listen: 0.0.0.0:53
  nameserver:
    - 223.5.5.5
工作原理
此版本可以通过设置 Interface Name (自动识别) 属性避免回环，并且支持了 UDP 及 IP 类请求，请在Settings页面Interface Name选项中选择出站网卡（通常为本机物理网卡）

注意事项
当enhanced-mode设置为fake-ip时，会出现系统检测到网卡无法联网，微软系 APP 无法登陆使用等问题，可以通过添加fake-ip-filter解决：

dns:
  enable: true
  enhanced-mode: fake-ip
  listen: 0.0.0.0:53
  nameserver:
    - 223.5.5.5
  fake-ip-filter:
    - "dns.msftncsi.com"
    - "www.msftncsi.com"
    - "www.msftconnecttest.com"
TAP 模式更推荐使用 redir-host 模式


TUN 模式
对于不遵循系统代理的软件，TUN 模式可以接管其流量并交由 CFW 处理，在 Windows 中，TUN 模式性能比 TAP 模式好

Windows
启动 TUN 模式需要进行如下操作：

进入网站Wintun，点解界面中Download Wintun xxx下载压缩包，根据系统版本将对应目录中wintun.dll复制至Home Directory目录中
以管理员权限启动 CFW
在使用的配置文件中加入如下内容：
dns:
  enable: true
  enhanced-mode: redir-host
  nameserver:
    - 1.1.1.1 # 真实请求DNS，可多设置几个
# interface-name: en0 # 出口网卡名称，或者使用下方的自动检测
tun:
  enable: true
  stack: gvisor
  dns-hijack:
    - 198.18.0.2:53
  macOS-auto-route: true
  macOS-auto-detect-interface: false # 自动检测出口网卡
macOS
启动 TUN 模式需要进行如下操作：

进入General中，点击Root Enable下方Authorize小字，弹出授权输入密码（当前版本下，每次更新 CFW，都需要重新授权）
在使用的配置文件中加入如下内容：
dns:
  enable: true
  enhanced-mode: redir-host
  nameserver:
    - 1.1.1.1 # 真实请求DNS，可多设置几个
# interface-name: en0 # 出口网卡名称，或者使用下方的自动检测
tun:
  enable: true
  stack: system # 或 gvisor
  dns-hijack: # DNS劫持设置为系统DNS
    - 114.114.114.114 # 可任意设置，但为了保证CFW关闭后能不影响联网，建议设置真实能访问的DNS服务器
  macOS-auto-route: true
  macOS-auto-detect-interface: false # 自动检测出口网卡

# dns-hijack 不可以劫持局域网地址的 DNS，如 192.168.0.0/16，请务必手动设置系统 DNS。

# 你可以使用mixin将上面内容合并至所有配置文件中，并使用 Mixin 开关 TUN 模式。


Mixin
版本要求
0.9.5版本更新后，支持向所有配置文件中注入公共属性设置

配置文件
例如：在配置文件中统一添加dns字段，操作如下：

进入Settings页面
滚动至Profile Mixin栏
点击YAML右边Edit小字打开编辑界面
在修改编辑界面内容为：
 mixin: 
   dns:
     enable: true
     listen: :53
     nameserver:
       - 8.8.8.8
点击编辑器右下角保存
在启动或切换配置时，上面内容将会替换到原有配置文件中进行覆盖

# 配置文件内容不会被修改，混合行为只会发生在内存中。

# 可以通过任务栏图标菜单开关这个行为。