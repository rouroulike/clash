# clash ❤️ docker

Below is a simple demo docker-compose file:

**NOTE: config.yaml must exist before `docker-compose up -d`**

Here is a sample config.yaml
```yml
port: 7890
socks-port: 7891

# `allow-lan` must be true in your config.yaml
allow-lan: true
external-controller: 0.0.0.0:8080

# dashboard folder
# external-ui: /ui
```

and a sample `docker-compose.yml`

```yml
version: '3'
services:
  clash:
    image: dreamacro/clash
    volumes:
      - ./config.yaml:/root/.config/clash/config.yaml
      # dashboard volume
      # - ./ui:/ui
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