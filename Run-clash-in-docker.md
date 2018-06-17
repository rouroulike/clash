# clash ❤️ docker

Below is a simple demo docker-compose file:

**NOTE: config.ini must exist before `docker-compose up -d`**

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
    network_mode: "host"
    container_name: clash
```