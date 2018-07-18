# clash ❤️ docker

Below is a simple demo docker-compose file:

**NOTE: config.ini must exist before `docker-compose up -d`**

Here is a sample config.ini
```ini
[General]
port = 7890
socks-port = 7891

# `allow-lan` must be true in your config.ini
allow-lan = true
external-controller = 0.0.0.0:8080
```

and a sample `docker-compose.yml`

```yml
version: '3'
services:
  clash:
    image: dreamacro/clash
    volumes:
      - ./config.ini:/root/.config/clash/config.ini
    ports:
      - "7890:7890"
      - "7891:7891"
      # If you need external controller, you can export this port.
      # - "8080:8080"
    restart: always
    # When your system is Linux, you can use `network_mode: "host"` directly.
    network_mode: "bridge"
    container_name: clash
```