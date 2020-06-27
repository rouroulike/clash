# Preface
Clash is meant to be run in the background. Unfortunately, there's currently no elegant way to implement daemons with Golang. We can daemonize Clash with third-party tools.

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
We recommend deploying Clash with [Docker Compose](https://docs.docker.com/compose/).

```yaml
version: '3'
services:
  clash:
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

Save as `docker-compose.yaml`, create Clash `config.yaml` in the same directory and run:

```
$ docker-compose up -d
```
 
To get Clash up. You can view the logs with:

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
