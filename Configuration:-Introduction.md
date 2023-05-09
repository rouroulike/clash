Clash uses [YAML](https://yaml.org), _YAML Ain't Markup Language_, for configuration files. YAML is designed to be easy to be read, be written, and be interpreted by computers, and is commonly used for exact configuration files. In this chapter, we'll cover the common features of Clash and how they should be used and configured.

Clash works by opening HTTP, SOCKS5, or the transparent proxy server on the local end. When a packet comes in, Clash _routes_ the packet to different remote servers ("nodes") with either VMess, Shadowsocks, Snell, Trojan, SOCKS5 or HTTP protocol.

# Specifying Configuration Directory

By default, Clash reads the configuration files at `$HOME/.config/clash`. If it doesn't exist, Clash will generate a minimal configuration file.

The main config file is `config.yaml`

You can use command-line option `-d` to specify a configuration directory:

```undefined
$ clash -d . # current directory
$ clash -d /etc/clash
```

You can use command-line option `-f` to specify a configuration:

```undefined
$ clash -f ./config.yaml # current directory
$ clash -f /etc/clash/config.yaml
```

# Syntax

- IPv6 addresses should be wrapped with `[` and `]`. For example: `[aaaa::a8aa:ff:fe09:57d8]`.
- Wildcard characters. Beware any domain with these characters should be wrapped with single-quotes `'`.
  - `*`: single-level wildcard character. `*.google.com` matches `www.google.com` but not `foo.bar.google.com`. It is possible to use `*.*.*.google.com`.
  - `+`: multi-level wildcard character. `+.google.com` matches `google.com`, `www.google.com` and `foo.bar.google.com`. This works exactly like `DOMAIN-SUFFIX`.


# Proxy Groups

Proxy Groups are groups of proxies that you can utilize some special features of Clash to manage and make use of.

- `relay`: The request sent to this proxy group will be relayed through the specified proxy servers sequently. There's currently no UDP support on this. The specified proxy servers should not contain another relay.
- `url-test`: Clash benchmarks each proxy servers in the list, by sending HTTP HEAD requests to a specified URL through these servers periodically. It's possible to set a maximum tolerance value, benchmarking interval, and the target URL.
- `fallback`: Clash periodically tests the availability of servers in the list with the same mechanism of `url-test`. The first available server will be used.
- `load-balance`: The request to the same eTLD+1 will be dialed with the same proxy.
- `select`: The first server is by default used when Clash starts up. Users can choose the server to use with the RESTful API. In this mode, you can hardcode servers in the config or use [Proxy Providers](https://github.com/Dreamacro/clash/wiki/Configuration#proxy-providers).

# Proxy Providers

Proxy Providers give users the power to load proxy server lists dynamically, instead of hardcoding them in the configuration file. There are currently two sources for a proxy provider to load server list from:

- `http`: Clash loads the server list from a specified URL on startup. Clash periodically pulls the server list from remote if the `interval` option is set.
- `file`: Clash loads the server list from a specified location on the filesystem on startup.

Health check is available for both modes, and works exactly like `fallback` in Proxy Groups. The configuration format for the server list files is also exactly the same in the main configuration file:

```yaml
# config.yaml
proxy-providers:
  provider1:
    type: http
    url: "url"
    interval: 3600
    path: ./provider1.yaml
    # filter: 'a|b' # golang regex string
    health-check:
      enable: true
      interval: 600
      # lazy: true
      url: http://www.gstatic.com/generate_204
  test:
    type: file
    path: /test.yaml
    health-check:
      enable: true
      interval: 36000
      url: http://www.gstatic.com/generate_204
```

```yaml
# test.yaml
proxies:
  - name: "ss1"
    type: ss
    server: server
    port: 443
    cipher: chacha20-ietf-poly1305
    password: "password"

  - name: "ss2"
    type: ss
    server: server
    port: 443
    cipher: chacha20-ietf-poly1305
    password: "password"
    plugin: obfs
    plugin-opts:
      mode: tls

  # ……
```

# Rules

Available keywords:

- `DOMAIN`: `DOMAIN,www.google.com,policy` routes only `www.google.com` to `policy`.
- `DOMAIN-SUFFIX`: `DOMAIN-SUFFIX,youtube.com,policy` routes any FQDN that ends with `youtube.com`, for example, `www.youtube.com` or `foo.bar.youtube.com`, to `policy`. This works like the wildcard character `+`.
- `DOMAIN-KEYWORD`: `DOMAIN-KEYWORD,google,policy` routes any FQDN that contains `google`, for example, `www.google.com` or `googleapis.com`, to `policy`.
- `GEOIP`: `GEOIP,CN,policy` routes any requests to a China IP address to `policy`.
- `IP-CIDR`: `IP-CIDR,127.0.0.0/8,DIRECT` routes any packets to `127.0.0.0/8` to the `DIRECT` policy.
- `IP-CIDR6`: `IP-CIDR6,2620:0:2d0:200::7/32,policy` routes any packets to `2620:0:2d0:200::7/32` to `policy`.
- `SRC-IP-CIDR`: `SRC-IP-CIDR,192.168.1.201/32,DIRECT` routes any packets **from** `192.168.1.201/32` to the `DIRECT` policy.
- `SRC-PORT`: `SRC-PORT,80,policy` routes any packets **from** the port 80 to `policy`.
- `DST-PORT`: `DST-PORT,80,policy` routes any packets **to** the port 80 to `policy`.
- `PROCESS-NAME`: `PROCESS-NAME,nc,DIRECT` routes the process `nc` to `DIRECT`. (support macOS、Linux、FreeBSD and Windows)
- `MATCH`: `MATCH,policy` routes the rest of the packets to `policy`. This rule is **required**.

There are two additional special policies:

- `DIRECT`: directly connects to the target without any proxies involved
- `REJECT`: a black hole for packets. Clash will not process any I/O to this policy.

A policy can be either `DIRECT`, `REJECT`, a proxy group or a proxy server.

## no-resolve

`no-resolve` is an additional option for `GEOIP`, `IP-CIDR`, or `IP-CIDR6` rules. Append `,no-resolve` to these rules to enable. Clash by default translates the domain names to IP addresses when encountering IP rules. Clash skips the IP rules with this option enabled when encountering packets that have an FQDN target.

