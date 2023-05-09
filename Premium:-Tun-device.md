Simply add the following to the main configuration:

**TIPS:**

> TUN device also works on Android, but its control device is `/dev/tun` instead of `/dev/net/tun`, you need to create a symbolic link first. eg. `ln -sf /dev/tun /dev/net/tun`
>
>
**NOTE:**

> `auto-route` and `auto-detect-interface` only available on macOS, Windows, Linux and Android, receive IPv4 traffic. if `system dns` is a private ip may lead to dns-hijack failure. Because `auto-route` will not capture private network traffics.
>
>
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

```undefined
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

