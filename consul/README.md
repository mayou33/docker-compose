https://store.docker.com/images/consul

docker pull consul:1.2.1

由于Consul几乎总是--net=host在Docker中运行，因此在配置Consul的IP地址时需要注意。Consul具有其集群地址的概念以及其客户端地址.

容器将其数据目录公开/consul/data 为卷VOLUME。这是Consul将存储其持久状态的地方。

容器具有设置的Consul配置目录/consul/config

在启动时，代理将从中读取配置JSON文件/consul/config。数据将保留在/consul/data卷中

目录所有权将更改为consul用户

我们还建议通过consul snapshot外部进行额外备份，并将其存储在外部。

```
groupadd -r consul
useradd -r -g consul -d /var/lib/consul -s /sbin/nologin -c "consul user" consul

mkdir -p /data/consul/{config,data}
chown -R consul:consul   /data/consul/
chmod -R 0755 /data/consul/
````

```
docker-compose.yml
 version: '3.5'
services: 
  consul:
    container_name: consul100
    image: consul:1.2.1
    restart: always
    network_mode: "host"
    volumes:
      - /data/consul/config:/consul/config
      - /data/consul/data:/consul/data
    ports:
      - 8300:8300
      - 8301:8301
      - 8301:8301/udp
      - 8302:8302
      - 8302:8302/udp
      - 8500:8500
      - 8600:8600
``` 
## 
编辑配置文件 

server模式还是client模式 在配置文件里面设置,
示例如下
```
cat >> /data/consul/config/consul.json <<EOF
{
    "datacenter": "dc1",
    "data_dir": "/var/lib/consul",
    "log_level": "INFO",
    "node_name": "consul30",
    "server": true,
    "ui": true,
    "bootstrap_expect": 1,
    "bind_addr": "192.168.1.30",
    "client_addr": "127.0.0.1 192.168.1.30",
    "retry_join": ["192.168.1.31","192.168.1.32"],
    "retry_interval": "3s",
    "protocol": 3,
    "raft_protocol": 3,
    "enable_debug": false,
    "rejoin_after_leave": true,
    "enable_syslog": false
}
EOF
`````

