# clash ❤️ docker

Below is a simple demo docker-compose file:

**NOTE: config.ini must exist before `docker-compose up -d` and `allow-lan` must be true**

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
    restart: always
    # When your system is Linux, you can use `network_mode: "host"` directly.
    network_mode: "bridge"
    container_name: clash
```