It requires [kernel support](https://github.com/iovisor/bcc/blob/master/INSTALL.md#kernel-configuration), only hook traffic of the egress NIC and conflict with `auto-route`

```yaml
ebpf:
  redirect-to-tun:
    - eth0
```

