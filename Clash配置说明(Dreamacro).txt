# port of HTTP
port: 7890

# port of SOCKS5
socks-port: 7891

# (HTTP and SOCKS5 in one port)
# mixed-port: 7890

# redir port for Linux and macOS
# redir-port: 7892

# authentication of local SOCKS5/HTTP(S) server
# authentication:
#  - "user1:pass1"
#  - "user2:pass2

allow-lan: false

# This is only applicable when `allow-lan` is `true`
# '*': bind all IP addresses
# 192.168.122.11: bind a single IPv4 address
# "[aaaa::a8aa:ff:fe09:57d8]": bind a single IPv6 address
bind-address: '*'

# Clash router working mode
# rule: rule-based packet routing
# global: all packets will be forwarded to a single endpoint
# direct: directly forward the packets to the Internet
mode: rule

# Clash by default prints logs to STDOUT
# info / warning / error / debug / silent
log-level: info

# When set to false, resolver won't translate hostnames to IPv6 addresses
ipv6: true

# RESTful web API listening address
external-controller: 127.0.0.1:9090

# A relative path to the configuration directory or an absolute path to a
# directory in which you put some static web resource. Clash core will then
# serve it at `${API}/ui`.
# external-ui: folder

# Secret for the RESTful API (optional)
# Authenticate by spedifying HTTP header `Authorization: Bearer ${secret}`
# ALWAYS set a secret if RESTful API is listening on 0.0.0.0
# secret: ""

# Outbound interface name
interface-name: en0

# Static hosts for DNS server and connection establishment, only works
# when `dns.enhanced-mode` is `redir-host`.
#
# Wildcard hostnames are supported (e.g. *.clash.dev, *.foo.*.example.com)
# Non-wildcard domain names has a higher priority than wildcard domain names
# e.g. foo.example.com > *.example.com > .example.com
# P.S. +.foo.com equals to .foo.com and foo.com
hosts:
  'mtalk.google.com': 108.177.125.188
  # '*.clash.dev': 127.0.0.1
  # '.dev': 127.0.0.1
  # 'alpha.clash.dev': '::1'

# DNS server settings
# This section is optional. When not present, DNS server will be disabled.
dns:
  enable: false
  listen: 0.0.0.0:53
  # ipv6: false # when false, response to AAAA questions will be empty

  # These nameservers are used to resolve the DNS nameserver hostnames below.
  # Specify IP addresses only
  default-nameserver:
    - 114.114.114.114
    - 8.8.8.8
  enhanced-mode: redir-host # or fake-ip
  fake-ip-range: 198.18.0.1/16 # Fake IP addresses pool CIDR
  
  # Hostnames in this list will not be resolved with fake IPs
  # i.e. questions to these domain names will always be answered with their
  # real IP addresses
  # fake-ip-filter:
  #   - '*.lan'
  #   - localhost.ptlogin2.qq.com
  
  # Supports UDP, TCP, DoT, DoH. You can specify the port to connect to.
  # All DNS questions are sent directly to the nameserver, without proxies
  # involved. Clash answers the DNS question with the first result gathered.
  nameserver:
    - 114.114.114.114 # default value
    - 8.8.8.8 # default value
    - tls://dns.rubyfish.cn:853 # DNS over TLS
    - https://1.1.1.1/dns-query # DNS over HTTPS

  # When `fallback` is present, the DNS server will send concurrent requests
  # to the servers in this section along with servers in `nameservers`.
  # The answers from fallback servers are used when the GEOIP country
  # is not `CN`.
  # fallback:
  #   - tcp://1.1.1.1

  # If IP addresses resolved with servers in `nameservers` are in the specified
  # subnets below, they are considered invalid and results from `fallback`
  # servers are used instead.
  #
  # IP address resolved with servers in `nameserver` is used when
  # `fallback-filter.geoip` is true and when GEOIP of the IP address is `CN`.
  #
  # If `fallback-filter.geoip` is false, results from `fallback` nameservers
  # are always used, and answers from `nameservers` are discarded.
  #
  # This is a countermeasure against DNS pollution attacks.
  fallback-filter:
    geoip: true
    ipcidr:
      # - 240.0.0.0/4

proxies:
  # shadowsocks
  # The supported ciphers(encrypt methods):
  #   aes-128-gcm aes-192-gcm aes-256-gcm
  #   aes-128-cfb aes-192-cfb aes-256-cfb
  #   aes-128-ctr aes-192-ctr aes-256-ctr
  #   rc4-md5 chacha20-ietf xchacha20
  #   chacha20-ietf-poly1305 xchacha20-ietf-poly1305
  - name: "ss1"
    type: ss
    server: server
    port: 443
    cipher: chacha20-ietf-poly1305
    password: "password"
    # udp: true

  # old obfs configuration format remove after prerelease
  - name: "ss2"
    type: ss
    server: server
    port: 443
    cipher: chacha20-ietf-poly1305
    password: "password"
    plugin: obfs
    plugin-opts:
      mode: tls # or http
      # host: bing.com

  - name: "ss3"
    type: ss
    server: server
    port: 443
    cipher: chacha20-ietf-poly1305
    password: "password"
    plugin: v2ray-plugin
    plugin-opts:
      mode: websocket # no QUIC now
      # tls: true # wss
      # skip-cert-verify: true
      # host: bing.com
      # path: "/"
      # mux: true
      # headers:
      #   custom: value
      
# ssr
  - name: "ssr"
    type: ssr
    server: server
    port: 443
    cipher: chacha20-ietf
    password:  "password"
    protocol: auth_aes128_sha1
    protocolparam: 
    obfs: tls1.2_ticket_auth
    obfsparam: ssr.server.com
    #udp: true
# 多个节点将一个节点参数写一行，参数用符号隔开。
# ,后不加空格也可以,:与-后有空格；每行末尾不要有空格！
# 名称等参数不加""也可以。
# 各组规则注意对齐。
# 如果导入配置提示失败：查看是否符合如上要求；配置文件中emoji显示问号，点击它配合空格和删除键让它正常显示。
# vmess 服务器地址与host地址由于加密和伪装协议参数，如none加密需要二者地址参数相同！
# - {name: "俄罗斯", type: vmess, server: v5.v2ray.xyz, port: 443, uuid: bf0db402-c863-45c6-a7b2-5f872118ab31, alterId: 233, cipher: auto, udp: true, tls: true, network: ws, ws-path: /, ws-headers: {Host: v5.ssrsub.xyz}}
# - {name: "🇸🇬 新加坡", type: vmess, server: xinjiapo.803.com, port: 443, uuid: 20ad9004-8823-3b4f-9f65-a4f07d2bb970, alterId: 4, cipher: auto, tls: false, udp: false, network: ws, ws-path: /ray, ws-headers: {Host: xinjiapo.803.com}}
# - {name: "🇨🇳 香港", type: vmess, server: 45.54.2.38, port: 6120, uuid: 641ffa9f-60ec-4afe-81ce-f22daf3cbff9, alterId: 8, cipher: auto, tls: false}
# - {name: "🇨🇳 台湾", type: "ssr", "server": "tw08.freexyz.best", "port": 20008, "password": "gaLgrz", "cipher": "chacha20-ietf", "obfs": "tls1.2_ticket_auth", "obfsparam": "35a1011210.microsoft.com", "protocol": "auth_aes128_sha1", "protocolparam": "11210:wBucUw"}      

  # vmess
  # cipher support auto/aes-128-gcm/chacha20-poly1305/none
  - name: "vmess"
    type: vmess
    server: server
    port: 443
    uuid: uuid
    alterId: 32
    cipher: auto
    # udp: true
    # tls: true
    # skip-cert-verify: true
    # network: ws
    # ws-path: /path
    # ws-headers:
    #   Host: v2ray.com

  # socks5
  - name: "socks"
    type: socks5
    server: server
    port: 443
    # username: username
    # password: password
    # tls: true
    # skip-cert-verify: true
    # udp: true

  # http
  - name: "http"
    type: http
    server: server
    port: 443
    # username: username
    # password: password
    # tls: true # https
    # skip-cert-verify: true

  # snell
  - name: "snell"
    type: snell
    server: server
    port: 44046
    psk: yourpsk
    # obfs-opts:
      # mode: http # or tls
      # host: bing.com

  # trojan
  - name: "trojan"
    type: trojan
    server: server
    port: 443
    password: yourpsk
    # udp: true
    # sni: example.com # aka server name
    # alpn:
    #   - h2
    #   - http/1.1
    # skip-cert-verify: true

proxy-groups:
  # relay chains the proxies. proxies shall not contain a proxy-group. No UDP support.
  # Traffic: clash <-> http <-> vmess <-> ss1 <-> ss2 <-> Internet
  - name: "relay"
    type: relay
    proxies:
      - http
      - vmess
      - ss1
      - ss2

  # url-test select which proxy will be used by benchmarking speed to a URL.
  - name: "auto"
    type: url-test
    proxies:
      - ss1
      - ss2
      - vmess1
    # tolerance: 150
    url: 'http://www.gstatic.com/generate_204'
    interval: 300

  # fallback select an available policy by priority. The availability is tested by accessing an URL, just like an auto url-test group.
  - name: "fallback-auto"
    type: fallback
    proxies:
      - ss1
      - ss2
      - vmess1
    url: 'http://www.gstatic.com/generate_204'
    interval: 300

  # load-balance: The request of the same eTLD will be dial on the same proxy.
  - name: "load-balance"
    type: load-balance
    proxies:
      - ss1
      - ss2
      - vmess1
    url: 'http://www.gstatic.com/generate_204'
    interval: 300

  # select is used for selecting proxy or proxy group
  # you can use RESTful API to switch proxy, is recommended for use in GUI.
  - name: Proxy
    type: select
    proxies:
      - ss1
      - ss2
      - vmess1
      - auto
  
  - name: UseProvider
    type: select
    use:
      - provider1
    proxies:
      - Proxy
      - DIRECT

proxy-providers:
  provider1:
    type: http
    url: "url"
    interval: 3600
    path: ./hk.yaml
    health-check:
      enable: true
      interval: 600
      url: http://www.gstatic.com/generate_204
  test:
    type: file
    path: /test.yaml
    health-check:
      enable: true
      interval: 36000
      url: http://www.gstatic.com/generate_204

rules:
  - DOMAIN-SUFFIX,google.com,auto
  - DOMAIN-KEYWORD,google,auto
  - DOMAIN,google.com,auto
  - DOMAIN-SUFFIX,ad.com,REJECT
  # rename SOURCE-IP-CIDR and would remove after prerelease
  - SRC-IP-CIDR,192.168.1.201/32,DIRECT
  # optional param "no-resolve" for IP rules (GEOIP IP-CIDR)
  - IP-CIDR,127.0.0.0/8,DIRECT
  - GEOIP,CN,DIRECT
  - DST-PORT,80,DIRECT
  - SRC-PORT,7777,DIRECT
  # FINAL would remove after prerelease
  # you also can use `FINAL,Proxy` or `FINAL,,Proxy` now
  - MATCH,auto

# 使用示例rule-providers
# 服务器节点订阅
proxy-providers:
  # name: # Provider 名称
  #   type: http # http 或 file
  #   path: # 文件路径
  #   url: # 只有当类型为 HTTP 时才可用，您不需要在本地空间中创建新文件。
  #   interval: # 自动更新间隔，仅在类型为 HTTP 时可用
  #   health-check: # 健康检查选项从此处开始
  #     enable:
  #     url: 
  #     interval: 

  #
  # 「url」参数填写托管订阅链接加参数list=true
  #「url」参数也可以填写仅包含节点的yaml文件链接(需要符合proxy-providers格式)
  #
  # 订阅链接可以使用 API 进行转换，如：https://sub.dler.io/
  #
  # 1.模式选择「进阶模式」 2.填写订阅链接 3.勾选「输出为 Node List」 4.「生成订阅链接」

  #
  机场订阅: # 机场订阅链接
    type: http
    url: "https://gfwsb.114514.best/sub?target=clashr&new_name=true&url=https%3A%2F%2Fjiang.netlify.app%2F&insert=false&config=https%3A%2F%2Fraw.githubusercontent.com%2FDaibogit%2FMySub%2Fmaster%2FTest.ini&new_name=true"
    interval: 3600
    path: ./MyList.yaml # 不同机场不同命名
    health-check:
      enable: true
      interval: 600
      url: http://www.gstatic.com/generate_204

proxy-groups:
# 策略组示例请查阅 Clash 项目 README 以使用最新格式：https://github.com/Dreamacro/clash/blob/master/README.md

#
# 策略组说明
#
# 「MATCH」类似 Surge 的「Final」，此处用于选择白名单模式(PROXY 策略)和黑名单模式(DIRECT 策略)
#
# 「Streaming」和「StreamingSE」比较好理解，有专用于流媒体的节点就设置到其中，如果没有「StreamingSE」的需求可以连带 Rule 部分一起删掉，「Streaming」需至少保留 Rule，用「PROXY」即可。
#
# 「PROXY」是代理规则策略，它可以指定为某个节点或嵌套一个其他策略组，如：「自动测试」、「Fallback」或「负载均衡」的策略组，关于这 3 个策略组的具体示例可以看官方示例：https://github.com/Dreamacro/clash
#

  # Fallback 比较实用的策略组类型，用于测试服务器节点的可用性，当第一个节点不可用时切换到第二个，以此类推。

  # 代理节点选择
  - name: "节点选择"
    type: select
    proxies:
      - 自动选择
      - 故障切换
      - DIRECT
      - HK
      - US
      - SG
      - 手动选择

  # 手动选择机场订阅节点
  - name: "手动选择"
    type: select # 亦可使用 fallback 或 load-balance
    use:
      - 机场订阅

  # 白名单模式 PROXY, 黑名单模式 DIRECT, 不知道别动

  - name: "自动选择"
    type: url-test
    use:
      - 机场订阅
    url: 'http://www.gstatic.com/generate_204'
    interval: 300

  - name: "故障切换"
    type: fallback
    proxies:
      - HK
      - US
      - SG
    url: 'http://www.gstatic.com/generate_204'
    interval: 300

  # 国际流媒体服务
  - name: "国外媒体"
    type: select
    proxies:
      - 节点选择
      - DIRECT
      - HK
      - US
      - SG
      - 手动选择

  # 中国流媒体服务（面向海外版本）
  - name: "国内媒体"
    type: select
    proxies:
      - DIRECT
      - 节点选择
      - HK
      - 手动选择

  - name: "全球直连"
    type: select
    proxies:
      - DIRECT
      - 自动选择
      - 故障切换
      - 手动选择

  - name: "漏网之鱼"
    type: select
    proxies:
      - 节点选择
      - DIRECT
      - 自动选择
      - 故障切换
      - 手动选择

# 关于 Rule Provider 请查阅：https://lancellc.gitbook.io/clash/clash-config-file/rule-provider

# 由于相关规则.yaml文件在github上，clash中导入该模式完全配置.yaml文件需要挂梯子！

rule-providers:
# name: # Provider 名称
#   type: http # http 或 file
#   behavior: classical # 或 ipcidr、domain
#   path: # 文件路径
#   url: # 只有当类型为 HTTP 时才可用，您不需要在本地空间中创建新文件。
#   interval: # 自动更新间隔，仅在类型为 HTTP 时可用

  Unbreak:
    type: http
    behavior: classical
    path: ./RuleSet/Unbreak.yaml
    url: https://raw.githubusercontent.com/DivineEngine/Profiles/master/Clash/RuleSet/Unbreak.yaml
    interval: 86400

  Streaming:
    type: http
    behavior: classical
    path: ./RuleSet/StreamingMedia/Streaming.yaml
    url: https://raw.githubusercontent.com/DivineEngine/Profiles/master/Clash/RuleSet/StreamingMedia/Streaming.yaml
    interval: 86400

  StreamingSE:
    type: http
    behavior: classical
    path: ./RuleSet/StreamingMedia/StreamingSE.yaml
    url: https://raw.githubusercontent.com/DivineEngine/Profiles/master/Clash/RuleSet/StreamingMedia/StreamingSE.yaml
    interval: 86400

  Global:
    type: http
    behavior: classical
    path: ./RuleSet/Global.yaml
    url: https://raw.githubusercontent.com/DivineEngine/Profiles/master/Clash/RuleSet/Global.yaml
    interval: 86400

  China:
    type: http
    behavior: classical
    path: ./RuleSet/China.yaml
    url: https://raw.githubusercontent.com/DivineEngine/Profiles/master/Clash/RuleSet/China.yaml
    interval: 86400

  ChinaIP:
    type: http
    behavior: ipcidr
    path: ./RuleSet/Extra/ChinaIP.yaml
    url: https://raw.githubusercontent.com/DivineEngine/Profiles/master/Clash/RuleSet/Extra/ChinaIP.yaml
    interval: 86400

# 规则
rules:
  # Unbreak
  - RULE-SET,Unbreak,全球直连

  # Global Area Network

  # (Streaming Media)
  - RULE-SET,Streaming,国外媒体

  # (StreamingSE)
  - RULE-SET,StreamingSE,国内媒体

  # (DNS Cache Pollution) / (IP Blackhole) / (Region-Restricted Access Denied) / (Network Jitter)
  - RULE-SET,Global,节点选择

  # China Area Network
  - RULE-SET,China,全球直连

  # Local Area Network
  - IP-CIDR,192.168.0.0/16,全球直连
  - IP-CIDR,10.0.0.0/8,全球直连
  - IP-CIDR,172.16.0.0/12,全球直连
  - IP-CIDR,127.0.0.0/8,全球直连
  - IP-CIDR,100.64.0.0/10,全球直连
  - IP-CIDR,224.0.0.0/4,全球直连

  # （可选）使用来自 ipipdotnet 的 ChinaIP 以解决数据不准确的问题，使用 ChinaIP.yaml 时可禁用下列直至（包括）「GEOIP,CN」规则
  # - RULE-SET,ChinaIP,全球直连
  # Tencent
  - IP-CIDR,119.28.28.28/32,全球直连
  - IP-CIDR,182.254.116.0/24,全球直连
  # GeoIP China
  - GEOIP,CN,全球直连

  - MATCH,漏网之鱼
