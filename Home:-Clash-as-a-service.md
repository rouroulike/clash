While Clash is meant to be run in the background, there's currently no elegant way to implement daemons with Golang, hence we recommend you to daemonize Clash with third-party tools.

# systemd

Copy Clash binary to `/usr/local/bin` and configuration files to `/etc/clash`:

```undefined
$ cp clash /usr/local/bin
$ cp config.yaml /etc/clash/
$ cp Country.mmdb /etc/clash/
```

Create the systemd configuration file at `/etc/systemd/system/clash.service`:

```ini
[Unit]
Description=Clash daemon, A rule-based proxy in Go.
After=network.target

[Service]
Type=simple
Restart=always
ExecStart=/usr/local/bin/clash -d /etc/clash

[Install]
WantedBy=multi-user.target
```

After that you're supposed to reload systemd:

```undefined
$ systemctl daemon-reload
```

Launch clashd on system startup with:

```undefined
$ systemctl enable clash
```

Launch clashd immediately with:

```undefined
$ systemctl start clash
```

Check the health and logs of Clash with:

```undefined
$ systemctl status clash
$ journalctl -xe
```

Credits to [ktechmidas](https://github.com/ktechmidas) for this guide. ([#754](https://github.com/Dreamacro/clash/issues/754))

# Docker

We recommend deploying Clash with [Docker Compose](https://docs.docker.com/compose/) if you're on Linux.

On macOS, it's recommended to use the third-party Clash GUI [ClashX Pro](https://install.appcenter.ms/users/clashx/apps/clashx-pro/distribution_groups/public). ([#770](https://github.com/Dreamacro/clash/issues/770#issuecomment-650951876))

Be advised that it's not recommended to run Clash Premium in a container. ([#2249](https://github.com/Dreamacro/clash/issues/2249#issuecomment-1203494599))

```yaml
services:
  clash:
    # ghcr.io/dreamacro/clash
    # ghcr.io/dreamacro/clash-premium
    # dreamacro/clash
    # dreamacro/clash-premium
    image: dreamacro/clash
    container_name: clash
    volumes:
      - ./config.yaml:/root/.config/clash/config.yaml
      # - ./ui:/ui # dashboard volume
    ports:
      - "7890:7890"
      - "7891:7891"
      # - "8080:8080" # external controller (Restful API)
    # TUN
    # cap_add:
    #   - NET_ADMIN
    # devices:
    #   - /dev/net/tun
    restart: unless-stopped
    network_mode: "bridge" # or "host" on Linux
```

Save as `docker-compose.yaml`, create `config.yaml` in the same directory, and run the below commands to get Clash up:

```undefined
$ docker-compose up -d
```

You can view the logs with:

```undefined
$ docker-compose logs
```

Stop Clash with:

```undefined
$ docker-compose stop
```

