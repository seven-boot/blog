# docker 应用合集

## docker & docker-compose 安装

- 更新数据源

  ```sh
  apt-get update
  ```

- 安装所需依赖

  ```sh
  apt-get -y install apt-transport-https ca-certificates curl software-properties-common
  ```

- 安装 GPG 证书

  ```sh
  curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
  ```

- 新增数据源

  ```sh
  add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
  ```

- 更新并安装 Docker CE

  ```sh
  apt-get update && apt-get install -y docker-ce
  ```

- 验证

  ```sh
  docker version
  ```

- 配置加速器

  ```sh
  tee /etc/docker/daemon.json <<-'EOF'
  {
    "registry-mirrors": ["https://g67vxk7w.mirror.aliyuncs.com"]
  }
  EOF
  ```

- 重启docker，加载配置

  ```sh
  systemctl daemon-reload && systemctl restart docker
  ```

- 验证

  ```sh
  docker info
  ```

- 安装docker-compose

  ```sh
  curl -L https://github.com/docker/compose/releases/download/1.24.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
  ```

- 增加执行权限

  ```sh
  chmod +x /usr/local/bin/docker-compose
  ```

- 验证

  ```sh
  docker-compose version
  ```

## MongoDB

```yaml
version: '3.1'
services:
  mongo:
    image: mongo
    container_name: mongo-db
    restart: always
    ports:
      - 27017:27017
    volumes:
      - ./data:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: 19960206

  mongo-express:
    image: mongo-express
    container_name: mongo-express
    restart: always
    ports:
      - 8081:8081
    depends_on:
      - mongo
    environment:
      ME_CONFIG_BASICAUTH_USERNAME: express
      ME_CONFIG_BASICAUTH_PASSWORD: express
      ME_CONFIG_MONGODB_ADMINUSERNAME: root
      ME_CONFIG_MONGODB_ADMINPASSWORD: 19960206
```

## Redis

```yaml
version: '3.1'
services:
  redis:
    image: redis
    container_name: redis
    restart: always
    command: redis-server --requirepass 1234567890
    volumes:
      - ./data:/data
    ports:
      - 6380:6379
```

## Mysql

```yaml
version: '3.1'
services:
  db:
    # 目前 latest 版本为 MySQL8.x
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql
  # MySQL 的 Web 客户端
  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```

## ELK

```yaml
version: "3.7"
services:
  elasticsearch:
    image: "docker.elastic.co/elasticsearch/elasticsearch:7.8.0"
    container_name: elasticsearch
    restart: always
    volumes:
      - "elasticsearch:/usr/share/elasticsearch"
    environment:
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - "discovery.type=single-node"
    ports:
      - "9200:9200"

  kibana:
    image: "docker.elastic.co/kibana/kibana:7.8.0"
    container_name: kibana
    restart: "always"
    environment:
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
    ports:
      - "5601:5601"


volumes:
  elasticsearch:
    external: true
```

## Nacos

```yaml
version: "3.9"
services:
  nacos:
    image: nacos/nacos-server:1.4.2
    container_name: nacos-1.4.2
    restart: always
    env_file:
      - ./nacos-standlone-mysql.env
    volumes:
      - ./logs/:/home/nacos/logs
    ports:
      - "8848:8848"
```

