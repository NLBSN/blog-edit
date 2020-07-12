---
title: "自己常用的docker-compose文件"
date: 2020-07-12 12:00:00
categories:
  - 容器
  - docker
  - docker-compose
tags:
  - 容器
  - docker
  - docker-compose
thumbnailImagePosition: left
thumbnailImage: https://gitee.com/zzf35/cloudimg/raw/master/img/20200622193715.jpg

---

自己常用的一些docker-compose文件的记录

<!--more-->

[TOC]

# elasticsearch

```
version: '3.1'
services:
  elasticsearch:
    image: elasticsearch:6.4.3
    container_name: elasticsearch
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - /data/prometheus/elk/data/1:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - esnet
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.5"
#          memory: 512M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#      placement:
#        constraints: 
#          - node.labels.func == db
  elasticsearch2:
    image: elasticsearch:6.4.3
    container_name: elasticsearch2
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - "discovery.zen.ping.unicast.hosts=elasticsearch"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - /data/prometheus/elk/data/2:/usr/share/elasticsearch/data
    networks:
      - esnet
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.5"
#          memory: 512M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#      placement:
#        constraints:               
#          - node.labels.func == db
#volumes:
#  esdata1:
#    driver: local
#  esdata2:
#    driver: local

networks:
  esnet:
```

# etcd

```
version: '3.7'

services:
  etcd:
    image: bitnami/etcd:3.3.13
    environment:
      - ALLOW_NONE_AUTHENTICATION=yes
      - ETCD_ADVERTISE_CLIENT_URLS=http://etcd:2379
    ports:
      - 2379:2379
      - 2380:2380
    networks:
      - app-tier
      
networks:
  app-tier:
    driver: bridge
```

# ftpgrab

```yml
version: "3.2"

services:
  ftpgrab:
    image: ftpgrab/ftpgrab:6.4.0
    container_name: ftpgrab
    volumes:
      - "./db:/db:rw"
      - "./download:/download:rw"
      - "./ftpgrab.yml:/ftpgrab.yml:ro"
    environment:
      - "TZ=Asia/Shanghai"
      - "SCHEDULE=*/30 * * * *"
      - "LOG_LEVEL=debug"
      - "LOG_JSON=false"
    restart: always
```



```yaml
# cat frpgrab.yml

server:
  type: ftp
  ftp:
    host: 10.xx.xx.xx
    port: 21
    username: xxx
    password: "xxxxxxxx"
    sources:
      - /data/test
    timeout: 5s
    disable_epsv: false
    tls: false
    insecure_skip_verify: false
    log_trace: false

db:
  enable: true

download:
  uid: 0
  gid: 0
  chmod_file: 0644
  chmod_dir: 0755
  include:
  exclude:
  since: 2020-05-18T00:00:00Z
  retry: 3
  hide_skipped: false
  create_basedir: false

notif:
  mail:
    enable: false
    host: smtp.example.com
    port: 587
    ssl: false
    insecure_skip_verify: false
    username: webmaster@example.com
    password: apassword
    from: ftpgrab@example.com
    to: webmaster@example.com
  webhook:
    enable: true
    endpoint: http://10.xx.xx.xx:9090/wis/ftpgrab/webhook
    method: GET
    headers:
      Content-Type: application/json
    #  Authorization: Token123456
    timeout: 10s
```



# java

```
version: '3.1'

services:
  java-8-test-idv:
    image: store/oracle/serverjre:8
    restart: always
    container_name: java-8-test-idv
    volumes:
      - /data/java:/data/java
    command: ["tail","-f","/dev/null"]
```



# mongodb

```yaml
version: '3.1'
services:
  mongo:
    image: mongo:4.1.7
    container_name: mongo
    restart: always
    ports:
      - 27017:27017
    volumes:
      - /data/mongo/data:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: xxxxxx
      wiredTigerCacheSizeGB: 1.5
#      MONGO_INITDB_DATABASE: gravitee
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      placement:
#        constraints: 
#          - node.labels.func == db

  mongo-express:
    image: mongo-express:0.49
    container_name: mongo-express
    restart: always
    ports:
      - 8087:8081
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: root
      ME_CONFIG_MONGODB_ADMINPASSWORD: xxxxxx
      ME_CONFIG_MONGODB_PORT: 27017
      ME_CONFIG_MONGODB_SERVER: 10.xx.xx.xx
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
```



# mysql

```yaml
version: '3.1'

services:
  mysql-db:
    image: mysql:5.7
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    ports:
      - 3306:3306
    environment:
      MYSQL_ROOT_PASSWORD: xxxxxxxxxxx
#    volumes:
#      - /data/mysql/data:/var/lib/mysql
#      - /data/mysql/conf:/etc/mysql/conf.d
    networks:
      - mysql-network
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      placement:
#        constraints: 
#          - node.labels.func == count

networks:
  mysql-network:
```



# nginx(补充)

```
version: '3.1'

services:
  nginx-web-ssh:
    image: 10.16.48.44:5000/nginx:1.14
    restart: always
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
#      - /data:/usr/share/nginx/html
    ports:
      - "8084:80"
      - "388:3888"
    environment:
      - NGINX_HOST=wis.com
      - NGINX_PORT=80
    networks:
      - nginx_net
networks:
  nginx_net:
```

# openmeetings

```
version: '3.7'

services:
  openmeetings:
    image: apache/openmeetings:5.0.0-M4
    restart: always
    ports:
      - "5443:5443"
      - "8888:8888"
    container_name: openmeetings
    networks:
      - openmeetings
networks:
  openmeetings:
```



# postgis

```
version: '3.1'

services:
  postgres:
    image: postgres:9.6
    restart: always
    container_name: db_postgis
    environment:
      POSTGRES_USER: root
      POSTGRES_DB: traffic
      POSTGRES_PASSWORD: "xxxxxx"
    ports:
      - 5432:5432
    volumes:
      - /data/database:/var/lib/postgresql/data
    networks:
      - postgres-network
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      placement:
#        constraints: 
#          - node.labels.func == db
  
  adminer:
    image: adminer:4.6.3
    restart: always
    container_name: database_dw
    networks:
      - adminer-network
    ports:
      - 8083:8080
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure

networks:
  postgres-network:
  adminer-network:
```

# registry

```
version: '3.1'

services:
  registry:
    image: registry:2.7
    restart: always
    ports:
      - 5000:5000
    volumes:
      - /data/registry/docker/registry:/var/lib/registry 
```

# jenkins

```
version: '3.1'

services:
  jenkins:
    image: jenkins/jenkins:centos
    container_name: jenkins_wis
    restart: always
    ports:
      - 8535:8080
      - 50000:50000
    volumes:
      - ./data:/var/jenkins_home
    networks:
      - jenkins_net

volumes:
  data:
    driver: local

networks:
  jenkins_net:
```



# rabbitmq

```
version: '3.1'
services:
  rabbitmq:
    image: 10.16.48.44:5000/rabbitmq:3.7.15
    restart: always
    environment:
      RABBITMQ_DEFAULT_USER: rabbitmq 
      RABBITMQ_DEFAULT_PASS: rabbitmq
    ports: 
      - 4369:4369
      - 5671:5671
      - 5672:5672
      - 25672:25672
```

# rap2

```
version: '3.1'

services:
  rap2-delos:
    container_name: rap2-delos
    # build from ./Dockerfile
#    build: .
    # build from images
    image: 10.16.48.44:5000/blackdog1987/rap2-delos:2.6.aa3be03
    environment:
      - MYSQL_URL=10.xx.xx.xxx
      - MYSQL_PORT=3306
      - MYSQL_USERNAME=rap2
      - MYSQL_PASSWD=rap2@123
      - MYSQL_SCHEMA=rap2
      - REDIS_URL=10.xx.xx.xxx
      - REDIS_PORT=6379
      # production / development
      - NODE_ENV=production
    privileged: true
    command: node_dispatch_js
    ports:
      - "8090:8080"
    deploy:
      replicas: 1
      mode: replicated
      restart_policy:
        condition: on-failure
      placement:
        constraints: 
          - node.labels.func == data

```



# redis

```
version: '3.1'

services:
  redis:
    image: redis:4.0.11
    restart: always
    container_name: redis_db
    hostname: redis
    ports: 
      - 6379:6379
    #cpus: 0.5
    #mem_limit: 50g
    #mem_limit: 1000000000
    #memswap_limit: 2000000000
    #memswap_limit: 70g
    #mem_reservation: 15g
    #privileged: true
 #   volumes:
 #     - /data/redis:/data
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      placement:
#        constraints: 
#          - node.labels.func == db
```



# redmine

中文乱码需要去将mysql字符换成utf-8

```
version: '3.1'

services:

  redmine:
    image: redmine:4.0.3
    restart: always
    ports:
      - 8098:3000
    volumes:
      - /data/redmine/files:/usr/src/redmine/files
      - /data/redmine/themes:/usr/src/redmine/public/themes
    environment:
      REDMINE_DB_MYSQL: 10.xx.xx.xxx
      REDMINE_DB_PORT: 3306
      REDMINE_DB_USERNAME: redmine
      REDMINE_DB_PASSWORD: xxxxxxx@xxx
      REDMINE_DB_DATABASE: redmine
      REDMINE_DB_ENCODING: utf8
    deploy:
      replicas: 1
      mode: replicated
      restart_policy:
        condition: on-failure
      placement:
        constraints:
          - node.labels.func == count
```

# kafka

```
version: '3.1'
services:
  zoo1:
    image: zookeeper:3.6.1
#    restart: always
    hostname: zoo1
    ports:
      - 2181:2181
    environment:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=0.0.0.0:2888:3888;2181

  kafka:
    image: wurstmeister/kafka:2.12-2.5.0
    hostname: kafka
    container_name: kafka
 #   restart: always
    ports:
      - 9092:9092
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 10.xx.xx.xx
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181
      KAFKA_BROKER_ID: 1
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    depends_on:
      - zoo1
```

# prometheus

```
version: '3.7'

services:
  prometheus:
    image: prom/prometheus:v2.19.2
    container_name: monitor_prometheus
    restart: always
    ports:
      - 9090:9090
    volumes:
      - ./etc/prometheus.yml:/etc/prometheus/prometheus.yml   #prometheus配置文件
#      - /data/prometheus/prometheus/data:/prometheus/data   #prometheus数据目录
    environment:
      - TZ=Asia/Shanghai
#      - /data/prometheus/prometheus/config/first_rules.yml:/etc/prometheus/first_rules.yml   #报警配置文件
#    command: --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/prometheus --web.console.libraries=/usr/share/prometheus/console_libraries  --web.console.templates=/usr/share/prometheus/consoles --web.external-url=http://10.16.48.44:9090    #重写了启动的配置参数，其中web.external-url配置问prometheus地址是为了在报警邮件里面点击直接到prometheus的web界面
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.5"
#          memory: 512M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#        order: start-first # 更新顺序为新任务启动优先
#      placement:
#        constraints: 
#          - node.labels.func == db
```



```
# cat prometheus.yml

# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'
    static_configs:
      - targets: ['192.168.8.153:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['192.168.8.153:9100']

  - job_name: 'dcgm-exporter'
    static_configs:
      - targets: ['192.168.8.153:9400']
```



```
FROM prom/prometheus:v2.9.2

RUN chmod -R 777 /prometheus
```



# grafana

```
version: '3.7'

services:
  grafana:
    image: grafana/grafana:7.0.6
    container_name: monitor_grafana
    restart: always
    ports:
      - 3001:3000
    environment:
      - TZ=Asia/Shanghai
#    volumes:
#      - /data/prometheus/grafana/config:/etc/grafana
#      - /data/prometheus/grafana/data:/var/lib/grafana   #prometheus数据目录
#    deploy:
#      replicas: 1
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.2"
#          memory: 200M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#        # order: start-first # 更新顺序为新任务启动优先
#      placement:
#        constraints: 
#          - node.labels.func == data
```

# node-exporter

```
version: '3.7'

services:
  node-exporter:
    image: prom/node-exporter:v1.0.1
    container_name: monitor_node-exporter
    restart: always
    environment:
      - TZ=Asia/Shanghai
    ports:
      - 9100:9100
#    volumes:
#      - /data:/data
#    deploy:
#      mode: global
#      replicas: 1
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.1"
#          memory: 64M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#        order: start-first # 更新顺序为新任务启动优先
```



# pushgateway

```
version: '3.1'

services:
  pushgateway:
    image: prom/pushgateway:v0.8.0
    container_name: monitor_pushgateway
    restart: always
    environment:
      - TZ=Asia/Shanghai
    ports: 
      - 9091:9091
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.5"
#          memory: 200M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#      placement:
#        constraints:              
#          - node.labels.func == count
```



# cadvisor

```
version: '3.1'

services:
  cadvisor:
    image: google/cadvisor:v0.33.0
    container_name: monitor_cadvisor
    restart: always
    environment:
      - TZ=Asia/Shanghai
    volumes:
       - /:/rootfs:ro
       - /var/run:/var/run:rw
       - /sys:/sys:ro
       - /var/lib/docker/:/var/lib/docker:ro
    ports: 
      - 8184:8080
    privileged: true
#    deploy:
#      mode: global
#      replicas: 1
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.2"
#          memory: 200M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#        order: start-first # 更新顺序为新任务启动优先
```



# dcgm-exporter

```
#!/bin/bash

sudo docker run -d --gpus all --rm -p 9400:9400 nvidia/dcgm-exporter:1.7.2
```





# elasticsearch_exporter

```
version: '3.1'

services:
  elasticsearch_exporter:
    image: justwatch/elasticsearch_exporter:1.0.2
    container_name: monitor_elasticsearch_exporter
    command:
      - '-es.uri=http://10.xx.xx.xx:9200'
    restart: always
    ports:
      - 9108:9108
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.5"
#          memory: 100M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#      placement:
#        constraints: 
#          - node.labels.func == db
```



# filebeat

```
version: '3.1'
services:
  filebeat1:
    image: store/elastic/filebeat:7.0.0
    restart: always
    container_name: monitor_filebeat
    user: root
    volumes:
      - /data/prometheus/filebeat/config/filebeat.docker.yml:/usr/share/filebeat/filebeat.yml:ro
      - /data/docker/data/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
#    command: filebeat -e -strict.perms=false -E output.elasticsearch.hosts=["10.16.48.44:9200"] 
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.5"
#          memory: 200M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#      placement:
#        constraints: 
#          - node.labels.func == db
 
  filebeat2:
    image: store/elastic/filebeat:7.0.0
    restart: always
    container_name: monitor_filebeat
    user: root
    volumes:
      - /data/prometheus/filebeat/config/filebeat.docker.yml:/usr/share/filebeat/filebeat.yml:ro
      - /data/docker/data/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
#    command: filebeat -e -strict.perms=false -E output.elasticsearch.hosts=["10.16.48.44:9200"] 
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.5"
#          memory: 200M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#      placement:
#        constraints:              
#          - node.labels.func == data
  
  filebeat3:
    image: store/elastic/filebeat:7.0.0
    restart: always
    container_name: monitor_filebeat
    user: root
    volumes:
      - /data/prometheus/filebeat/config/filebeat.docker.yml:/usr/share/filebeat/filebeat.yml:ro
      - /data/docker/data/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
#    command: filebeat -e -strict.perms=false -E output.elasticsearch.hosts=["10.16.48.44:9200"] 
#    deploy:
#      replicas: 1
#      mode: replicated
#      restart_policy:
#        condition: on-failure
#      resources:
#        limits:
#          cpus: "0.5"
#          memory: 200M
#      update_config:
#        parallelism: 1 # 每次更新1个副本
#        delay: 5s # 每次更新间隔 
#        monitor: 10s # 单次更新多长时间后没有结束则判定更新失败
#        max_failure_ratio: 0.1 # 更新时能容忍的最大失败率
#      placement:
#        constraints:              
#          - node.labels.func == data
```











































