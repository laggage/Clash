# 自用整合 Clash for Windows 配置

### Clash for Windows 下载地址

[Clash for Windows](https://github.com/Fndroid/clash_for_windows_pkg/releases)

### 懒人配置参考！

[config.yaml](https://raw.githubusercontent.com/Semporia/Clash/master/Calsh%20for%20Windows/config.yaml)

### 远程引用分流规则

```yaml

# Port of HTTP(S) proxy server on the local end
port: 7890
 
# Port of SOCKS5 proxy server on the local end
socks-port: 7891
 
# Transparent proxy server port for Linux and macOS
# redir-port: 7892
 
# HTTP(S) and SOCKS5 server on the same port
mixed-port: 7890
 
# authentication of local SOCKS5/HTTP(S) server
# authentication:
#  - "user1:pass1"
#  - "user2:pass2"
 
# Set to true to allow connections to local-end server from
# other LAN IP addresses
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
mode: Rule
 
# Clash by default prints logs to STDOUT
# info / warning / error / debug / silent
log-level: info
 
# When set to false, resolver won't translate hostnames to IPv6 addresses
ipv6: true
 
# RESTful web API listening address
external-controller: 127.0.0.1:59446
 
# A relative path to the configuration directory or an absolute path to a
# directory in which you put some static web resource. Clash core will then
# serve it at `${API}/ui`.
# external-ui: folder
 
# Secret for the RESTful API (optional)
# Authenticate by spedifying HTTP header `Authorization: Bearer ${secret}`
# ALWAYS set a secret if RESTful API is listening on 0.0.0.0
# secret: 357e4d16-90c4-4afe-bb32-2e7b30e051f8
 
# Outbound interface name
# interface-name: en0
 
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
    - 223.5.5.5
    - 119.29.29.29
  enhanced-mode: fake-ip # redir-host or fake-ip
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
    - https://doh.pub/dns-query
    - https://dns.alidns.com/dns-query
 
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
      - 240.0.0.0/4
      - 0.0.0.0/32

proxy-providers:
  Airport:
    type: http
    # url: "订阅链接"
    path: ./Server/Airport.yaml
    interval: 3600
    # filter: 'Hong Kong'
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 86400
      
  Hong Kong:
    type: file
    path: ./Server/Airport.yaml
    filter: '香港|Hong Kong'
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 86400
  Singapore :
    type: file
    path: ./Server/Airport.yaml
    filter: 'Singapore|新加坡'
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 86400
  Japan:
    type: file
    path: ./Server/Airport.yaml
    filter: 'Japan|日本'
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 86400
  United States:
    type: file
    path: ./Server/Airport.yaml
    filter: 'United States|美国'
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 86400

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
  - name: "Proxy"
    type: url-test
    use: 
      - Airport
 
  # 白名单模式 PROXY, 黑名单模式 DIRECT, 不知道别动
  - name: "MATCH"
    type: select
    proxies:
      - DIRECT
      - Proxy

  - name: "China"
    type: select
    proxies: 
      - DIRECT
  
  - name: "WeChat"
    type: url-test
    use: 
      - Japan 
  
  - name: "Tencent"
    type: url-test
    use: 
      - Japan

  - name: "TencentVideo"
    type: select
    proxies: 
      - DIRECT

  - name: "Speedtest"
    type: select
    proxies: 
      - DIRECT

  - name: "Apple"
    type: url-test
    use: 
      - United States
  
  - name: "Microsoft"
    type: url-test
    use: 
      - United States
  
  - name: "Adobe"
    type: url-test
    use: 
      - United States
  
  - name: "Amazon"
    type: url-test
    use: 
      - United States

  - name: "paypal"
    type: url-test
    use: 
      - United States

  - name: "GitHub"
    type: url-test
    use: 
      - Japan

  - name: "Google"
    type: url-test
    use: 
      - United States

  - name: "YouTube"
    type: url-test
    use: 
      - United States

  - name: "Netflix"
    type: url-test
    use: 
      - United States
  - name: "Steam"
    type: url-test
    use: 
      - United States

  - name: "Spotify"
    type: url-test
    use: 
      - United States

  - name: "Telegram"
    type: url-test
    use: 
      - Japan

  - name: "Twitter"
    type: url-test
    use: 
      - Hong Kong
 
  - name: "Facebook"
    type: url-test
    use: 
      - United States

  - name: "Dler"
    type: url-test
    use: 
      - United States

rule-providers:

  Apple: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Apple.yaml
    path: ./Rule/Apple.yaml
    interval: 86400
  Adobe: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Adobe.yaml
    path: ./Rule/Adobe.yaml
    interval: 3600
  
  Amazon: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Amazon.yaml
    path: ./Rule/Amazon.yaml
    interval: 3600

  China: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/China.yaml
    path: ./Rule/China.yaml
    interval: 3600

  Dler: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Dler.yaml
    path: ./Rule/Dler.yaml
    interval: 3600

  Facebook: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Facebook.yaml
    path: ./Rule/Facebook.yaml
    interval: 3600

  GitHub: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/GitHub.yaml
    path: ./Rule/GitHub.yaml
    interval: 3600
  Google: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Google.yaml
    path: ./Rule/Google.yaml
    interval: 3600
  
  Microsoft: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Microsoft.yaml
    path: ./Rule/Microsoft.yaml
    interval: 3600
  
  Netflix: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Netflix.yaml
    path: ./Rule/Netflix.yaml
    interval: 3600
  
  Spotify: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Spotify.yaml
    path: ./Rule/Spotify.yaml
    interval: 3600
  Speedtest: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Speedtest.yaml
    path: ./Rule/Speedtest.yaml
    interval: 3600
  Steam: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Steam.yaml
    path: ./Rule/Steam.yaml
    interval: 3600

  Telegram: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Telegram.yaml
    path: ./Rule/Telegram.yaml
    interval: 3600
  Twitter: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Twitter.yaml
    path: ./Rule/Twitter.yaml
    interval: 3600
  Tencent: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Tencent.yaml
    path: ./Rule/Tencent.yaml
    interval: 3600
  TencentVideo: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/TencentVideo.yaml
    path: ./Rule/TencentVideo.yaml
    interval: 3600

  YouTube: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/YouTube.yaml
    path: ./Rule/YouTube.yaml
    interval: 3600

  PayPal: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/PayPal.yaml
    path: ./Rule/PayPal.yaml
    interval: 3600
  Proxy: 
    type: http
    behavior: classical
    url: https://cdn.jsdelivr.net/gh/Semporia/Clash@master/Rule/Proxy.yaml
    path: ./Rule/Proxy.yaml
    interval: 3600
 
     

# 规则
rules:
  # - RULE-SET,AdBlock,REJECT
  - RULE-SET,Proxy,Proxy
  - RULE-SET,Apple,Apple
  - RULE-SET,Adobe,Adobe
  - RULE-SET,Amazon,Amazon
  - RULE-SET,Dler,Dler
  - RULE-SET,Facebook,Facebook 
  - RULE-SET,GitHub,GitHub
  - RULE-SET,Google,Google
  - RULE-SET,Microsoft,Microsoft
  - RULE-SET,Netflix,Netflix 
  - RULE-SET,Speedtest,Speedtest
  - RULE-SET,Steam,Steam
  - RULE-SET,Spotify,Spotify
  - RULE-SET,Telegram,Telegram 
  - RULE-SET,Twitter,Twitter 
  - RULE-SET,Tencent,Tencent
  - RULE-SET,TencentVideo,TencentVideo
  - RULE-SET,YouTube,YouTube 
  - RULE-SET,PayPal,paypal
  - DOMAIN-SUFFIX,live.cn,China

  - PROCESS-NAME,v2ray,DIRECT
  - PROCESS-NAME,xray,DIRECT
  - PROCESS-NAME,naive,DIRECT
  - PROCESS-NAME,trojan,DIRECT
  - PROCESS-NAME,trojan-go,DIRECT
  - PROCESS-NAME,ss-local,DIRECT
  - PROCESS-NAME,privoxy,DIRECT
  - PROCESS-NAME,leaf,DIRECT
  - PROCESS-NAME,v2ray.exe,DIRECT
  - PROCESS-NAME,xray.exe,DIRECT
  - PROCESS-NAME,naive.exe,DIRECT
  - PROCESS-NAME,trojan.exe,DIRECT
  - PROCESS-NAME,trojan-go.exe,DIRECT
  - PROCESS-NAME,ss-local.exe,DIRECT
  - PROCESS-NAME,privoxy.exe,DIRECT
  - PROCESS-NAME,leaf.exe,DIRECT
  - PROCESS-NAME,Surge,DIRECT
  - PROCESS-NAME,Surge 2,DIRECT
  - PROCESS-NAME,Surge 3,DIRECT
  - PROCESS-NAME,Surge 4,DIRECT
  - PROCESS-NAME,Surge%202,DIRECT
  - PROCESS-NAME,Surge%203,DIRECT
  - PROCESS-NAME,Surge%204,DIRECT
  - PROCESS-NAME,Thunder,DIRECT
  - PROCESS-NAME,DownloadService,DIRECT
  - PROCESS-NAME,qBittorrent,DIRECT
  - PROCESS-NAME,Transmission,DIRECT
  - PROCESS-NAME,fdm,DIRECT
  - PROCESS-NAME,aria2c,DIRECT
  - PROCESS-NAME,Folx,DIRECT
  - PROCESS-NAME,NetTransport,DIRECT
  - PROCESS-NAME,uTorrent,DIRECT
  - PROCESS-NAME,WebTorrent,DIRECT
  - PROCESS-NAME,aria2c.exe,DIRECT
  - PROCESS-NAME,BitComet.exe,DIRECT
  - PROCESS-NAME,fdm.exe,DIRECT
  - PROCESS-NAME,NetTransport.exe,DIRECT
  - PROCESS-NAME,qbittorrent.exe,DIRECT
  - PROCESS-NAME,Thunder.exe,DIRECT
  - PROCESS-NAME,ThunderVIP.exe,DIRECT
  - PROCESS-NAME,transmission-daemon.exe,DIRECT
  - PROCESS-NAME,transmission-qt.exe,DIRECT
  - PROCESS-NAME,uTorrent.exe,DIRECT
  - PROCESS-NAME,WebTorrent.exe,DIRECT
  - DOMAIN,clash.razord.top,DIRECT
  - DOMAIN,yacd.haishan.me,DIRECT

  # Local Area Network
  - IP-CIDR,192.168.0.0/16,DIRECT
  - IP-CIDR,10.0.0.0/8,DIRECT
  - IP-CIDR,172.16.0.0/12,DIRECT
  - IP-CIDR,127.0.0.0/8,DIRECT
  - IP-CIDR,100.64.0.0/10,DIRECT
  - IP-CIDR,224.0.0.0/4,DIRECT
 
  # （可选）使用来自 ipipdotnet 的 ChinaIP 以解决数据不准确的问题，使用 ChinaIP.yaml 时可禁用下列直至（包括）「GEOIP,CN」规则
  - IP-CIDR,119.28.28.28/32,DIRECT
  - IP-CIDR,182.254.116.0/24,DIRECT
  - GEOIP,CN,DIRECT

  - MATCH,MATCH

```
