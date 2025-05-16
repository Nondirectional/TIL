# WSL 环境搭建

在WSL环境下使用Docker Compose安装和配置以下开发容器：

- postgres: 用于数据持久化存储的关系型数据库，使用pgvector扩展支持向量检索
- elasticsearch: 分布式搜索和分析引擎，用于全文检索和数据分析
- kibana: elasticsearch的可视化管理界面，用于数据展示和集群管理

这些容器将通过docker-compose编排工具进行统一管理，并配置相应的持久化存储和网络连接。



## 1. 创建必要elasticsearch与kibana的挂载文件夹

```bash
$ mkdir -p /data/development_containers/elastic/elasticsearch/config
$ mkdir -p /data/development_containers/elastic/elasticsearch/data
$ mkdir -p /data/development_containers/elastic/elasticsearch/plugins
$ chmod -R 777 /data/development_containers/elastic/elasticsearch/config
$ chmod -R 777 /data/development_containers/elastic/elasticsearch/data
$ chmod -R 777 /data/development_containers/elastic/elasticsearch/data

$ mkdir -p /data/development_containers/elastic/elasticsearch/plugins/ik
$ cd /data/development_containers/elastic/elasticsearch/plugins/ik
$ wget https://release.infinilabs.com/analysis-ik/stable/elasticsearch-analysis-ik-7.17.28.zip
$ unzip elasticsearch-analysis-ik-7.17.28.zip
```



## 2. 创建并写入文件 elasticsearch.yml 

```yaml
$ vim /data/development_containers/elastic/elasticsearch/config/elasticsearch.yml
```

文件内容：

```yaml
cluster.name: "elasticsearch"
network.host: 0.0.0.0
# 开启es跨域
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-headers: Authorization
# 开启安全控制
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
```



## 3. 创建并写入文件 kibana.yml 

```yaml
$ vim /data/development_containers/elastic/kibana/kibana.yml
```

文件内容：

```yaml
server.host: 0.0.0.0
server.name: kibana

# 这边设置自己es的地址，
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
elasticsearch.username: "elastic"
elasticsearch.password: "821206"
# # 显示登陆页面
xpack.monitoring.ui.container.elasticsearch.enabled: true
```



4. 创建并写入文件`vim /data/development_containers/docker-compose.yml`

```yaml
version: '3' # Docker Compose 文件版本

# 定义服务
services:
  # PostgreSQL 服务
  postgres:
    image: pgvector/pgvector:pg16 # 使用的镜像，包含 pgvector 扩展 
    container_name: postgres # 容器名称
    # 环境变量
    environment:
      POSTGRES_USER: root # PostgreSQL 用户名 
      POSTGRES_PASSWORD: 821206 # PostgreSQL 密码
      POSTGRES_DB: ai_database # PostgreSQL 数据库名称
    # 端口映射
    ports:
      - "5432:5432" # 将主机的 5432 端口映射到容器的 5432 端口 
    restart: always # 容器退出时总是重启 
    # 数据卷挂载
    volumes:
      - /data/development_containers/postgres/data:/var/lib/postgresql/data # 将主机目录挂载到容器内 PostgreSQL 数据目录，用于持久化数据
    # 网络配置
    networks:
      - development # 连接到名为 development 的网络
  elasticsearch: # Elasticsearch 服务
    image: elasticsearch:7.17.28 # 使用的 Elasticsearch 镜像版本
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
      - /data/development_containers/elastic/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml # 挂载 Elasticsearch 配置文件
      - /data/development_containers/elastic/elasticsearch/data:/var/lib/elasticsearch/data # 挂载 Elasticsearch 数据目录
      - /data/development_containers/elastic/elasticsearch/plugins:/usr/share/elasticsearch/plugins # 挂载 Elasticsearch 插件目录
    # 网络配置
    networks:
      - development # 连接到名为 development 的网络
  
  kibana: # Kibana 服务
    image: kibana:7.17.28 # 使用的 Kibana 镜像版本
    container_name: kibana # 容器名称
    # 端口映射
    ports:
      - "5601:5601" # 将主机的 5601 端口映射到容器的 5601 端口
    # 数据卷挂载
    volumes:
      - /data/development_containers/elastic/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml # 挂载 Kibana 配置文件
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

