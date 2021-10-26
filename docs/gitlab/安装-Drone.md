## Docker-Compose 安装

```yaml
version: '3.7'
services:
  drone-server:
    image: drone/drone:latest
    container_name: drone-server
    ports:
      - 10081:80
    volumes:
      - ./drone:/var/lib/drone/
      - /var/run/docker.sock:/var/run/docker.sock
    restart: always
    environment:
      - DRONE_OPEN=true
      - DRONE_SERVER_HOST=drone-server
      - DRONE_SERVER_PROTO=http
      - DRONE_LOGS_TRACE=true
      - DRONE_LOGS_DEBUG=true
      - DRONE_GIT_ALWAYS_AUTH=false
      - DRONE_GOGS=true
      - DRONE_GOGS_SERVER=http://gogs:3000
      - DRONE_PROVIDER=gogs
      - DRONE_DATABASE_DATASOURCE=/var/lib/drone/drone.sqlite
      - DRONE_DATABASE_DRIVER=sqlite3
      - DRONE_RPC_SECRET=MWckgvhjqg4E3eQ0psgZX4iNCxoQiyU4LLvO4eXFFuHtrTkIy8vwcAc3erB5f9reM
    networks:
      - service
  drone-agent:
    image: drone/agent:latest
    container_name: drone-agent
    depends_on:
      - drone-server
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    restart: always
    environment:
      - DRONE_RPC_SERVER=http://drone-server
      - DRONE_RPC_SECRET=MWckgvhjqg4E3eQ0psgZX4iNCxoQiyU4LLvO4eXFFuHtrTkIy8vwcAc3erB5f9reM
      - DRONE_LOGS_TRACE=true
      - DRONE_LOGS_DEBUG=true
    networks:
      - service

networks:
  service:
    external:
      name: service
```

