# Introduction
The premium core is currently close-sourced. It's compiled on Dreamacro's Macbook.

# TUN device

Simply add the following to the main configuration:

```yaml
tun:
  enable: true
  stack: system # or gvisor
  # dns-hijack:
  #   - 8.8.8.8:53
  #   - tcp://8.8.8.8:53
  # macOS-auto-route: true # auto set global route
  # macOS-auto-detect-interface: true # conflict with interface-name
```

It's recommended to use `redir-host` mode for the DNS server.

Clash needs elevated permission to create TUN device:

```
$ sudo ./clash
```

Then manually create the default route and DNS server. If your device already has some TUN device, Clash TUN might not work. In this case, `fake-ip-filter` may helpful.

Enjoy! :)


# Script

Script enables user to programmatically select policy for the packets with more flexibility.

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

# Rule Providers
Rule Providers is pretty much the same comparing to Proxy Providers. It enables users to load rules from external sources and overall cleaner configuration. This feature is currently Premium core only.

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
