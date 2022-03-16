## docker-frp

- **支持构架：** ARM64、AMD64、386

### Docker Hub
- https://hub.docker.com/r/devcto/frps
- https://hub.docker.com/r/devcto/frpc


### 手动构建
```
git clone https://github.com/skiy/docker-frp.git
cd docker-frp
docker build -t frpc -f ./Dockerfile-frpc .
docker build -t frps -f ./Dockerfile-frps .
```

---

### frps
`docker-compose.yaml`
```yaml
version: '3'
services:
  frps:
    image: devcto/frps:latest
    container_name: frps-serve
    volumes:
      - "/etc/localtime:/etc/localtime"
      - "./conf/frps.ini:/app/frps.ini"
    restart: unless-stopped
    network_mode: "host"
```

```bash
docker run -d \
-v /etc/localtime:/etc/localtime \
-v ./conf/frps.ini:/app/frps.ini \
--name frps-serve \
--net=host \
--restart=unless-stopped \
devcto/frps:latest
```

[frps.ini](https://github.com/fatedier/frp/blob/dev/conf/frps.ini)
```
[common]
bind_port = 7000

vhost_http_port = 7080
vhost_https_port = 7443

dashboard_port = 8080
# dashboard 用户名密码，默认都为 admin
dashboard_user = admin
dashboard_pwd = admin
```

[frps_full.ini](https://github.com/fatedier/frp/blob/dev/conf/frps_full.ini)

---

### frpc
`docker-compose.yaml`
```yaml
version: '3'
services:
  frpc:
    image: devcto/frpc:latest
    container_name: client
    volumes:
      - "/etc/localtime:/etc/localtime"
      - "./conf/frpc.ini:/app/frpc.ini"
    restart: unless-stopped
    network_mode: "host"
```

```bash
docker run -d \
-v /etc/localtime:/etc/localtime \
-v ./conf/frpc.ini:/app/frpc.ini \
--name frpc \
--net=host \
--restart=unless-stopped \
devcto/frpc:latest
```

[frpc.ini](https://github.com/fatedier/frp/blob/dev/conf/frpc.ini)
> 需要先将域名 `blog.example.com` A 记录到 IP `xxx.xxx.xxx.xxx`。再通过 `http://blog.example.com:7080` 访问。其中 `7080` 端口是通过 frps 配置文件中的 `vhost_http_port` 配置。可以配合反向代理解决需要添加端口访问的问题。   
```
[common]
server_addr = xxx.xxx.xxx.xxx
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000

[web]
type = http
local_port = 80
custom_domains = blog.example.com

```

[frpc_full.ini](https://github.com/fatedier/frp/blob/dev/conf/frpc_full.ini)

---

更多配置请查看 [FRP 官方](https://github.com/fatedier/frp)
