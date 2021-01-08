# Preface
Clash is meant to be run in the background, there's currently no way to implement daemons elegantly with Golang. We can daemonize Clash with third-party tools.

# systemd
Copy Clash binary to `/usr/local/bin` and configuration files to `/etc/clash`:
```
$ cp clash /usr/local/bin
$ cp config.yaml /etc/clash/
$ cp Country.mmdb /etc/clash/
```

Create the systemd configuration file at `/etc/systemd/system/clash.service`:
```
[Unit]
Description=Clash Daemon

[Service]
ExecStart=/usr/local/bin/clash -d /etc/clash/

[Install]
WantedBy=multi-user.target
```

Launch clashd on system startup with:
```
$ systemctl enable clash
```

Launch clashd immediately with:
```
$ systemctl start clash
```

Check the health and logs of Clash with:
```
$ systemctl status clash
$ journalctl -xe
```

Credits to [ktechmidas](https://github.com/ktechmidas) for this guide. ([#754](https://github.com/Dreamacro/clash/issues/754))

# Docker
We recommend deploying Clash with [Docker Compose](https://docs.docker.com/compose/) if you're on Linux. On macOS, it's recommended to use the third-party Clash GUI [ClashX Pro](https://install.appcenter.ms/users/clashx/apps/clashx-pro/distribution_groups/public). ([#770](https://github.com/Dreamacro/clash/issues/770#issuecomment-650951876))

```yaml
version: '3'
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
    restart: unless-stopped
    network_mode: "bridge" # or "host" on Linux
```

Save as `docker-compose.yaml`, create `config.yaml` in the same directory, and run the below commands to get Clash up:

```
$ docker-compose up -d
```
 
You can view the logs with:

```
$ docker-compose logs
```

Stop Clash with:

```
$ docker-compose stop
```

# PM2
```
$ wget -qO- https://getpm2.com/install.sh | bash
$ pm2 start clash
```
