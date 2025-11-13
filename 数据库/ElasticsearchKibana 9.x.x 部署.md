# Elasticsearch/Kibana 9.x.x 部署

使用 Docker Compose 安装和配置以下开发容器：

- elasticsearch: 分布式搜索和分析引擎，用于全文检索和数据分析
- kibana: elasticsearch 的可视化管理界面，用于数据展示和集群管理

这些容器将通过 docker-compose 编排工具进行统一管理，并配置相应的持久化存储和网络连接。

## 1. 创建必要 elasticsearch 与 kibana 的挂载文件夹

```bash
# elasticsearch 配置目录
$ mkdir -p /data/ai-workspace/docker/elastic/elasticsearch/config
# elasticsearch 数据目录
$ mkdir -p /data/ai-workspace/docker/elastic/elasticsearch/data
# elasticsearch 插件目录
$ mkdir -p /data/ai-workspace/docker/elastic/elasticsearch/plugins
# kibana 配置目录
$ mkdir -p /data/ai-workspace/docker/elastic/kibana/config

# 将上述目录权限设置为 777
$ chmod -R 777 /data/ai-workspace/docker/elastic/elasticsearch/config
$ chmod -R 777 /data/ai-workspace/docker/elastic/elasticsearch/data
$ chmod -R 777 /data/ai-workspace/docker/elastic/elasticsearch/plugins
$ chmod -R 777 /data/ai-workspace/docker/elastic/kibana/config
```

## 2. 创建并写入文件 elasticsearch.yml

```yaml
vim /data/ai-workspace/docker/elastic/elasticsearch/config/elasticsearch.yml
```

文件内容：

```yaml
cluster.name: "docker-cluster"

network.host: 0.0.0.0
# 开启es跨域
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-headers: Authorization

# 开启安全控制
xpack.security.enabled: true

# 暂不开启 http ssl
xpack.security.http.ssl:
  enabled: false

# 暂不开启 transport ssl
xpack.security.transport.ssl:
  enabled: false
```



## 3. 创建并写入文件 kibana.yml

```yaml
vim /data/ai-workspace/docker/elastic/kibana/kibana.yml
```

文件内容：

```yaml
server.host: "0.0.0.0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
monitoring.ui.container.elasticsearch.enabled: true
elasticsearch.username: "kibana_system"
elasticsearch.password: "xxxx"
```



## 4. 创建并写入文件 docker-compose.yml

```bash
vim /data/ai-workspace/docker/docker-compose.yml
```

文件内容：

```yaml
# 定义服务
services:
  elasticsearch: # Elasticsearch 服务
    image: elasticsearch:9.0.1 # 使用的 Elasticsearch 镜像版本
    container_name: elasticsearch # 容器名称
    # 端口映射
    ports:
      - "9200:9200" # 将主机的 9200 端口映射到容器的 9200 端口 (HTTP)
      - "9300:9300" # 将主机的 9300 端口映射到容器的 9300 端口 (Transport)
    environment:
      - "cluster.name=elasticsearch" # 设置 Elasticsearch 集群名称
      - discovery.type=single-node # 设置 Elasticsearch 为单节点模式
      - "ES_JAVA_OPTS=-Xms512m -Xmx2048m"  # 设置 Elasticsearch 的 JVM 内存参数
      - "ELASTIC_PASSWORD=821206" # 设置 Elasticsearch 的初始超级用户密码
    restart: always # 容器退出时总是重启
    # 数据卷挂载
    volumes:
      - /data/ai-workspace/docker/elastic/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml # 挂载 Elasticsearch 配置文件
      - /data/ai-workspace/docker/elastic/elasticsearch/config/elasticsearch-plugins.yml:/usr/share/elasticsearch/config/elasticsearch-plugins.yml
      - /data/ai-workspace/docker/elastic/elasticsearch/data:/var/lib/elasticsearch/data # 挂载 Elasticsearch 数据目录
      - /data/ai-workspace/docker/elastic/elasticsearch/plugins:/usr/share/elasticsearch/plugins # 挂载 Elasticsearch 插件目录
    # 网络配置
    networks:
      - development # 连接到名为 development 的网络
  kibana: # Kibana 服务
    image: kibana:9.0.1 # 使用的 Kibana 镜像版本
    container_name: kibana # 容器名称
    # 端口映射
    ports:
      - "5601:5601" # 将主机的 5601 端口映射到容器的 5601 端口
    # 数据卷挂载
    volumes:
      - /data/ai-workspace/docker/elastic/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml # 挂载 Kibana 配置文件
    # 网络配置
    networks:
      - development # 连接到名为 development 的网络
    # 依赖关系
    depends_on:
      - elasticsearch # 依赖 Elasticsearch 服务，确保 Elasticsearch 先启动
    privileged: true
    restart: always # 容器退出时总是重启

# 定义网络
networks:
  # 自定义网络
  development:
    driver: bridge # 使用 bridge 驱动创建网络

```

## 5. 启动 Elasticsearch 服务并设置密码

```bash
# 启动 Elasticsearch 服务
$ docker compose up -d elasticsearch
# 生成 elastic 超级用户密码
$ docker exec -it elasticsearch /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
# 生成 kibana 用户密码
$ docker exec -it elasticsearch /usr/share/elasticsearch/bin/elasticsearch-reset-password -u kibana_system
```

## 6. 修改 kibana 配置文件中 elasticsearch.password

```bash
vim /data/ai-workspace/docker/elastic/kibana/kibana.yml
```

修改内容：

```yml
server.host: "0.0.0.0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
monitoring.ui.container.elasticsearch.enabled: true
elasticsearch.username: "kibana_system"
elasticsearch.password: "修改为上文命令重置的 kibana_sytem 用户密码"
```

## 7. 启动 Kibana 服务并生成 API key

启动服务:

```bash
docker compose up -d kibana
```

访问 `http://localhost:5601`：

![kibana 登录界面](https://nondirectional.oss-cn-heyuan.aliyuncs.com/TIL/Kibana%E7%99%BB%E5%BD%95%E7%95%8C%E9%9D%A2.png)

使用 elastic 登入到 kibana 中，进入 **Management** --> **Stack Management**

![Elastic API key 生成指引 01](https://nondirectional.oss-cn-heyuan.aliyuncs.com/TIL/Elasticsearch%20API%20key%20%E7%94%9F%E6%88%90%E6%8C%87%E5%BC%95%2001.png)

进入 **Security** --> **API keys**

![Elasticsearch API key 生成指引 02](https://nondirectional.oss-cn-heyuan.aliyuncs.com/TIL/Elasticsearch%20API%20key%20%E7%94%9F%E6%88%90%E6%8C%87%E5%BC%95%2002.png)

进入 API key 创建界面：

![Elasticsearch API key 生成指引 03](https://nondirectional.oss-cn-heyuan.aliyuncs.com/TIL/Elasticsearch%20API%20key%20%E7%94%9F%E6%88%90%E6%8C%87%E5%BC%95%2003.png)

创建 API key：

![Elasticsearch API key 生成指引 04](https://nondirectional.oss-cn-heyuan.aliyuncs.com/TIL/Elasticsearch%20API%20key%20%E7%94%9F%E6%88%90%E6%8C%87%E5%BC%95%2004.png)

获得 API key：

![Elasticsearch API key 生成指引 05](https://nondirectional.oss-cn-heyuan.aliyuncs.com/TIL/Elasticsearch%20API%20key%20%E7%94%9F%E6%88%90%E6%8C%87%E5%BC%95%2005.png)
