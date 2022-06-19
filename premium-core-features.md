# Introduction
The premium core is currently close-sourced. It's compiled on Dreamacro's Macbook.

# TUN device

Simply add the following to the main configuration:

**TIPS:**
> TUN device also works on Android, but its control device is `/dev/tun` instead of `/dev/net/tun`, you need to create a symbolic link first. eg. `ln -sf /dev/tun /dev/net/tun` 

**NOTE:**
> `auto-route` and `auto-detect-interface` only available on macOS, Windows, Linux and Android, receive IPv4 traffic  

```yaml
tun:
  enable: true
  stack: system # or gvisor
  # dns-hijack:
  #   - 8.8.8.8:53
  #   - tcp://8.8.8.8:53
  #   - any:53
  #   - tcp://any:53
  auto-route: true # auto set global route
  auto-detect-interface: true # conflict with interface-name
```

or

```yaml
interface-name: en0

tun:
  enable: true
  stack: system # or gvisor
  # dns-hijack:
  #   - 8.8.8.8:53
  #   - tcp://8.8.8.8:53
  auto-route: true # auto set global route
```

It's recommended to use `fake-ip` mode for the DNS server.

Clash needs elevated permission to create TUN device:

```
$ sudo ./clash
```

Then manually create the default route and DNS server. If your device already has some TUN device, Clash TUN might not work. In this case, `fake-ip-filter` may helpful.

Enjoy! :)

## Windows

go to https://www.wintun.net and download the latest release, copy the right `wintun.dll` into Clash home directory

```yaml
tun:
  enable: true
  stack: gvisor # or system
  dns-hijack:
    - 198.18.0.2:53 # when `fake-ip-range` is 198.18.0.1/16, should hijack 198.18.0.2:53
  auto-route: true # auto set global route for Windows
  # It is recommended to use `interface-name`
  auto-detect-interface: true # auto detect interface, conflict with `interface-name`
```

Finally, open clash

# Script

Script enables users to programmatically select a policy for the packets with more flexibility.

```yaml
mode: Script

# https://lancellc.gitbook.io/clash/clash-config-file/script
script:
  code: |
    def main(ctx, metadata):
      ip = metadata["dst_ip"] = ctx.resolve_ip(metadata["host"])
      if ip == "":
        return "DIRECT"

      code = ctx.geoip(ip)
      if code == "LAN" or code == "CN":
        return "DIRECT"

      return "Proxy" # default policy for requests which are not matched by any other script
```

**NOTE: If you want to use ip rules(IP-CIDR GEOIP), you need to manually resolve ip and assign it to metadata first**
```python
def main(ctx, metadata):
    # ctx.rule_providers["geoip"].match(metadata) return false

    ip = ctx.resolve_ip(metadata["host"])
    if ip == "":
        return "DIRECT"
    metadata["dst_ip"] = ip

    # ctx.rule_providers["iprule"].match(metadata) return true

    return "Proxy"
```

the context and metadata
```ts
interface Metadata {
  type: string // socks5、http
  network: string // tcp
  host: string
  src_ip: string
  src_port: string
  dst_ip: string
  dst_port: string
}

interface Context {
  resolve_ip: (host: string) => string // ip string
  resolve_process_name: (metadata: Metadata) => string
  geoip: (ip: string) => string // country code
  log: (log: string) => void
  proxy_providers: Record<string, Array<{ name: string, alive: boolean, delay: number }>>
  rule_providers: Record<string, { match: (metadata: Metadata) => boolean }>
}
```

# Script Shortcut

use script on `rules`

**NOTE: `src_port` and `dst_port` are number**

```yaml
script:
  shortcuts:
    quic: network == 'udp' and dst_port == 443

rules:
  - SCRIPT,quic,REJECT
```

### Functions

```ts
type resolve_ip = (host: string) => string // ip string
type in_cidr = (ip: string, cidr: string) => boolean // ip in cidr
type geoip = (ip: string) => string // country code
type match_provider = (name: string) => boolean // in rule provider
```

# Rule Providers
Rule Providers are pretty much the same compared to Proxy Providers. It enables users to load rules from external sources and overall cleaner configuration. This feature is currently Premium core only.

To define a Rule Provider, add the `rule-providers` field to the main configuration:

```yaml
rule-providers:
  apple:
    behavior: "domain"
    type: http
    url: "url"
    interval: 3600
    path: ./apple.yaml
  microsoft:
    behavior: "domain"
    type: file
    path: /microsoft.yaml
```

There are three behavior types available:

## `domain`

```yaml
payload:
  - '.blogger.com'
  - '*.*.microsoft.com'
  - 'books.itunes.apple.com'
```

## `ipcidr`

```yaml
payload:
  - '192.168.1.0/24'
  - '10.0.0.0.1/32'
```

## `classical`

```yaml
payload:
  - DOMAIN-SUFFIX,google.com
  - DOMAIN-KEYWORD,google
  - DOMAIN,ad.com
  - SRC-IP-CIDR,192.168.1.201/32
  - IP-CIDR,127.0.0.0/8
  - GEOIP,CN
  - DST-PORT,80
  - SRC-PORT,7777
  # MATCH is not necessary here
```

```yaml
# Premium only
rule-providers:
  apple:
    behavior: "domain" # domain, ipcidr or classical (premium core only)
    type: http
    url: "url"
    interval: 3600
    path: ./apple.yaml
  microsoft:
    behavior: "domain"
    type: file
    path: /microsoft.yaml

rules:
  - RULE-SET,apple,REJECT
  - RULE-SET,microsoft,policy
```

# Tracing

https://github.com/Dreamacro/clash-tracing

```yaml
profile:
    tracing: true
```

# eBPF

It requires [kernel support](https://github.com/iovisor/bcc/blob/master/INSTALL.md#kernel-configuration), only hook traffic of the egress NIC and conflict with `auto-route`

```yaml
ebpf:
  redirect-to-tun:
    - eth0
```

# Auto redir

Use Linux kernel nftables feature on pure Go. It can be replaced with `redir-port` (TCP) without any network config.

It's recommended to work with TUN to handle UDP traffic. It improves the network throughput performance of some low performance devices compared to using exclusively TUN.

```yaml
interface-name: en0
auto-redir:
  enable: true
  auto-route: true
tun:
  enable: true
  stack: system
  dns-hijack:
    - any:53
  auto-route: true
```