# Preface
Clash is meant to be run in the background. Unfortunately, there's currently no elegant way to implement daemons with Golang. We can daemonize Clash with third-party tools.

# systemd
Copy Clash binary to `/usr/local/bin` and configuration files to `/etc/clash`:
```
$ cp clash /usr/local/bin
$ cp config.yaml /etc/clash/
$ cp Country.mmdb /etc/clash/
```

Create the systemd configuration file at `/etc/systemd/system/clashd.service`:
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

Check the health and logs of clashd with:
```
$ systemctl status clash
$ journalctl -xe # get logs/status
```

Credits to [ktechmidas](https://github.com/ktechmidas) for this guide. ([#754](https://github.com/Dreamacro/clash/issues/754))
