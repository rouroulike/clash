## Introduction
Clash uses [YAML](https://yaml.org), *YAML Ain't Markup Language*, for configuration files. YAML is designed to be easy to be read, be written and be interpreted by computers, and is commonly used for exactly configuration files. In this chapter, we'll cover the common features of Clash and how they should be used and configured.

Clash works by opening HTTP, SOCKS5 or transparent proxy server on the local end. When a request, or say packet, comes in, Clash *routes* the packet to different remote servers ("nodes") with either VMess, Shadowsocks, Snell, Trojan, SOCKS5 or HTTP protocol.

# All Configuration Options

```yaml
# Port of HTTP proxy server on the local end
port: 7890

# Port of SOCKS5 proxy server on the local end
socks-port: 7891

# Transparent proxy server port for Linux and macOS
redir-port: 7892

# (HTTP and SOCKS5 in one port)
# mixed-port: 7890

# authentication of local SOCKS5/HTTP(S) server
# authentication:
#  - "user1:pass1"
#  - "user2:pass2"

# Set to true to allow connections to local-end server from other LAN IP addresses
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

# A relative path to the configuration directory or an absolute path to a directory in which
# you put some static web resource. Clash core will then serve it at `http://{{external-controller}}/ui`.
external-ui: folder

# Secret for RESTful API (Optional)
# secret: ""

# Outbound interface name
interface-name: en0

# Hosts, wildcard supported (e.g. *.clash.dev Even *.foo.*.example.com)
# static domain names has a higher priority than wildcard domain (foo.example.com > *.example.com > .example.com)
# +.foo.com equal .foo.com and foo.com
# hosts:
#   '*.clash.dev': 127.0.0.1
#   '.dev': 127.0.0.1
#   'alpha.clash.dev': '::1'

# dns:
  # enable: true # set true to enable dns (default is false)
  # ipv6: false # when false, response to AAAA questions will be empty
  # listen: 0.0.0.0:53
  # # default-nameserver: # resolve dns nameserver host, should fill pure IP
  # #   - 114.114.114.114
  # #   - 8.8.8.8
  # enhanced-mode: redir-host # or fake-ip
  # # fake-ip-range: 198.18.0.1/16 # if you don't know what it is, don't change it
  # fake-ip-filter: # fake ip white domain list
  #   - '*.lan'
  #   - localhost.ptlogin2.qq.com
  # nameserver:
  #   - 114.114.114.114
  #   - tls://dns.rubyfish.cn:853 # dns over tls
  #   - https://1.1.1.1/dns-query # dns over https
  # fallback: # concurrent request with nameserver, fallback used when GEOIP country isn't CN
  #   - tcp://1.1.1.1
  # fallback-filter:
  #   geoip: true # default
  #   ipcidr: # ips in these subnets will be considered polluted
  #     - 240.0.0.0/4

proxies:
  # Shadowsocks
  # The supported ciphers (encryption methods):
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
    # servername: example.com # priority over wss host
    # network: ws
    # ws-path: /path
    # ws-headers:
    #   Host: v2ray.com
  
  - name: "vmess-http"
    type: vmess
    server: server
    port: 443
    uuid: uuid
    alterId: 32
    cipher: auto
    # udp: true
    # network: http
    # http-opts:
    #   # method: "GET"
    #   # path:
    #   #   - '/'
    #   #   - '/video'
    #   # headers:
    #   #   Connection:
    #   #     - keep-alive

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

  # Snell
  # Beware that there's currently no UDP support yet
  - name: "snell"
    type: snell
    server: server
    port: 44046
    psk: yourpsk
    # obfs-opts:
      # mode: http # or tls
      # host: bing.com

  # Trojan
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
  # relay chains the proxies. proxies shall not contain a relay. No UDP support.
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
  - SRC-IP-CIDR,192.168.1.201/32,DIRECT
  # optional param "no-resolve" for IP rules (GEOIP IP-CIDR)
  - IP-CIDR,127.0.0.0/8,DIRECT
  - GEOIP,CN,DIRECT
  - DST-PORT,80,DIRECT
  - SRC-PORT,7777,DIRECT
  - MATCH,auto
```

# Specifying Configuration Directory
If not otherwise specified, Clash by default reads the configuration file at `$HOME/.config/clash/config.yaml`.
If it doesn't exist, Clash will generate the default settings.  

You can use command-line option `-d` to specify a configuration directory: 

```
$ clash -d . # current directory
$ clash -d /etc/clash
```

# Syntax
* IPv6 addresses should be wrapped with `[` and `]`. For example: `[aaaa::a8aa:ff:fe09:57d8]`.
* Wildcard characters. Beware any domain with these characters should be wrapped with single-quotes `'`.
  * `*`: single-level wildcard character. `*.google.com` matches `www.google.com` but not `foo.bar.google.com`. It is possible to use `*.*.*.google.com`.
  * `+`: multi-level wildcard character. `+.google.com` matches `google.com`, `www.google.com` and `foo.bar.google.com`. This works exactly like `DOMAIN-SUFFIX`.

# DNS
The DNS server shipped with Clash aims to minimize DNS pollution attack impact and improve network performance. There are two modes for it to work: `redir-host` and `fake-ip`. The biggest difference between the two is how IP addresses are resolved and how the connections are established.

## redir-host
This is more of a traditional way of how proxies work. In this mode, the domains to connect to are resolved with the nameservers specified with `dns.nameserver` field. The first result returned will be sent to the client.

## fake-ip
When a DNS request is sent to the DNS server, Clash allocates a free *fake IP address* in the fake IP address pool (the IP CIDR can be specified with `dns.fake-ip-range`).

# Proxy Groups
TODO

# Proxy Providers
TODO

# Rules
Available keywords:
* `DOMAIN`
* `DOMAIN-KEYWORD`
* `DOMAIN-SUFFIX`
* `DOMAIN-SUFFIX`
* `DST-PORT`
* `GEOIP`
* `IP-CIDR`
* `MATCH`
* `SRC-IP-CIDR`
* `SRC-PORT`