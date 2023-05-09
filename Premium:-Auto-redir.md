This feature automagically sets up nftable to handle REDIRECT

Use Linux kernel nftables feature on pure Go. It can be replaced with `redir-port` (TCP) without any network config.

It's recommended to work with TUN to handle TCP traffic. It improves the network throughput performance of some low performance devices compared to using exclusively TUN.

```yaml
interface-name: en0

tun:
  enable: true
  stack: system
  dns-hijack:
    - any:53
  auto-redir: true
  auto-route: true
```

