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
  resolve_process_path: (metadata: Metadata) => string
  geoip: (ip: string) => string // country code
  log: (log: string) => void
  proxy_providers: Record<string, Array<{ name: string, alive: boolean, delay: number }>>
  rule_providers: Record<string, { match: (metadata: Metadata) => boolean }>
}
```

# Script Shortcut

This feature enables the use of script in `rules` mode. By default, DNS resolution takes place for SCRIPT rules. `no-resolve` can be appended to the rule to prevent the resolution. (i.e.: `SCRIPT,quic,DIRECT,no-resolve`)

**NOTE: ****`src_port`**** and ****`dst_port`**** are number**

```yaml
script:
  shortcuts:
    quic: network == 'udp' and dst_port == 443
    curl: resolve_process_name() == 'curl'
    # curl: resolve_process_path() == '/usr/bin/curl'

rules:
  - SCRIPT,quic,REJECT
```

## Functions

```ts
type resolve_ip = (host: string) => string // ip string
type in_cidr = (ip: string, cidr: string) => boolean // ip in cidr
type geoip = (ip: string) => string // country code
type match_provider = (name: string) => boolean // in rule provider
type resolve_process_name = () => string // find process name (curl .e.g)
type resolve_process_path = () => string // find process path (/usr/bin/curl .e.g)
```

