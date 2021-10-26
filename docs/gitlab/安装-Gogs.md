## docker 安装

```yaml
version: '3.7'
services:
  gogs:
    image: gogs/gogs
    container_name: gogs
    restart: always
    ports:
      - "3000:3000"
      - "10022:22"
    volumes:
      - ./gogs_data:/data
      - /etc/localtime:/etc/localtime:ro
    networks:
      - service

networks:
  service:
    external:
      name: service
```

