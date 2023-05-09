## Introduction

The Premium core has out-of-the-box support of TUN device. Being a Network layer device, it can be used to handle TCP, UDP, ICMP traffic. It has been extensively tested and used in production environments - you can even play competitive games with the use of Clash TUN.

Clash TUN has built-in support of the automatic management of the route table, rules and nftables, you can enable it with the options `tun.auto-route` and `tun.auto-redir`. However, they are only available on macOS, Windows, Linux and Android, and only receives IPv4 traffic currently.

There are two options of TCP/IP stack available: `system` or `gvisor`. We recommend that you always use `system` stack unless you have a specific reason to use `gvisor`, to get the best performance.

## Technical Limitations

* For Android, the control device is at `/dev/tun` instead of `/dev/net/tun`, you will need to create a symbolic link first (i.e. `ln -sf /dev/tun /dev/net/tun`)

* DNS hijacking might result in a failure, if the system DNS is at a private IP address (since `auto-route` does not capture private network traffic).

## Linux, macOS or Android

This is an example configuration of the TUN feature:

```yaml
interface-name: en0 # conflict with `tun.auto-detect-interface`

tun:
  enable: true
  stack: system # or gvisor
  # dns-hijack:
  #   - 8.8.8.8:53
  #   - tcp://8.8.8.8:53
  #   - any:53
  #   - tcp://any:53
  auto-route: true # manage `ip route` and `ip rules`
  auto-redir: true # manage nftable REDIRECT
  auto-detect-interface: true # conflict with `interface-name`
```

Be advised, since the use of TUN device and manipulation of system route/nft settings, Clash will need superuser privileges to run.

```shell
sudo ./clash
```

If your device already has some TUN device, Clash TUN might not work - you will have to check the route table and routing rules manually. In this case, `fake-ip-filter` may helpful as well.

## Windows

You will need to visit the [WinTUN website](https://www.wintun.net) and download the latest release. After that, copy `wintun.dll` into Clash home directory. Example configuration:

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
