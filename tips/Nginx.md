# Nginx 反代相关的折腾记录

安卓设备如果要安装rootCA.crt，应该选择CA证书，而不是VPN和应用用户证书

Windows上则是安装到受信任的根证书颁发机构

iOS直接安装，是以描述文件的形式，注意要在证书信任设置中信任一下

# 1. Vaultwarden 密码库
## 更新时间 2026.10.18

  NAS上有些服务必须要https来运行，比如这个第三方的bitwarden服务器

  想本地搭建，不买域名，不折腾Let's encrypt证书，不修改dns记录

  就想利用自签证书来操作，根据AI操作的步骤，操作了4，5次还是不太对

  最终版按照这篇[博文](https://blog.csdn.net/weixin_42530732/article/details/168153384)来

  1.进入docker的vaultwarden目录底下
```
cd /volume1/docker/vaultwarden/
```
  2.生成pki文件夹，和嵌套文件夹nginx/certs，然后cd进入到pki目录下
```
mkdir -p pki nginx/certs
```
```
cd pki
```

  3.创建本地根CA私钥，新建一个ca.cnf配置文件生成本地根证书，这里本地根证书设置为10年，也就是3650天
```
openssl genrsa -out rootCA.key 2048
```
  ca.cnf内容如下
```
[req]
distinguished_name = ca_dn
prompt = no

[ca_dn]
C = CN
O = Home LAN
CN = Home LAN Root CA

[v3_ca]
basicConstraints = critical,CA:TRUE
keyUsage = critical,keyCertSign,cRLSign
subjectKeyIdentifier = hash
```
  生成本地根证书
```
openssl req -new -x509 -days 3650 -key rootCA.key -out rootCA.crt -config ca.cnf -extensions v3_ca
```
  4.生成服务器私钥与证书签名请求
```
openssl genrsa -out ../nginx/certs/server.key 2048
```
    先新建一个san.cnf配置文件，内容如下
```
[req]
distinguished_name = dn
req_extensions = v3_req
prompt = no

[dn]
C = CN
O = Home LAN
CN = 192.168.10.100

[v3_req]
basicConstraints = CA:FALSE
keyUsage = digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
IP.1 = 192.168.10.100
```
然后利用server.key和san.cnf生成CSR
```
openssl req -new -key ../nginx/certs/server.key -out server.csr -config san.cnf
```
  5.用下面的命令CA根证书，生成服务器证书并签名
  
  这里只签397天，时间长了iOS设备上可能会报证书错误`NET::ERR_CERT_VALIDITY_TOO_LONG`
```
openssl x509 -req -in server.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out ../nginx/certs/server.crt -days 397 -extfile san.cnf -extensions v3_req
```

  6.检查一下这个时候nginx/certs文件夹下应该有server.crt和server.key，可以根据此搭建nginx服务
  
  7.Docker Compose文件如下，这里最终的端口号为8443

  创建了一个名为vaultwarden的桥接网络，服务名为vaultwarden
  
  服务名为services下的名称，为vaultwarden，这个在nginx的配置文件中有用
```
  networks:
  default:
    name: vaultwarden

services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    environment:
      DOMAIN: "https://192.168.10.100:8443"
      SIGNUPS_ALLOWED: "true"
      WEBSOCKET_ENABLED: "true"
    volumes:
      - ./vw-data:/data
    networks:
      - default

  nginx:
    image: nginx:alpine
    container_name: vaultwarden-nginx
    restart: unless-stopped
    depends_on:
      - vaultwarden
    ports:
      - "8443:443"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
      - ./nginx/certs:/etc/nginx/certs:ro
    networks:
      - default
```
  8.修改nginx的配置文件default.conf
  
  其中proxy_pass中的参数http后面是服务名，这里是vaultwarden，就不用纠结要不要填IP地址了
```
server {
    listen 443 ssl;
    server_name 192.168.10.100;

    ssl_certificate     /etc/nginx/certs/server.crt;
    ssl_certificate_key /etc/nginx/certs/server.key;
    ssl_protocols TLSv1.2 TLSv1.3;

    # WebSocket 通知端点
    location /notifications/hub {
        proxy_pass http://vaultwarden:3012;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }

    # negotiate 请求走 Vaultwarden 主服务，而非 WebSocket 端口
    location = /notifications/hub/negotiate {
        proxy_pass http://vaultwarden:80;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }

    location / {
        proxy_pass http://vaultwarden:80;
        proxy_http_version 1.1;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }
}
```

# 2. 给FolderSync配置带有Https的WebDAV服务
## 更新时间 2026.10.18

  这里用的是Openlist，有网页，自带webdav

  复制vaultwarden里的nginx那个嵌套文件夹，到openlist文件夹下

  新建一个openlistdata用于存放openlist的数据

  1.Docker Compose文件如下，这里最终的端口号为5443

```
networks:
  default:
    name: openlist

services:
  openlist:
    image: openlistteam/openlist:latest
    container_name: openlist
    restart: unless-stopped
    user: '1026:100'
    expose:
      - "5244"
    volumes:
      - ./openlistdata:/opt/openlist/data
      - /volume1/N1:/data
    networks:
      - default

  nginx:
    image: nginx:alpine
    container_name: openlist-nginx
    restart: unless-stopped
    depends_on:
      - openlist
    ports:
      - "5080:80"
      - "5443:443"
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
      - ./nginx/certs:/etc/nginx/certs:ro
    networks:
      - default
```
  2.修改nginx的配置文件default.conf
  
  其中proxy_pass中的参数http后面是服务名，这里叫openlist，同样不用纠结要不要填IP地址了
```
server {
    listen 80;
    server_name 192.168.10.100;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name 192.168.10.100;

    ssl_certificate     /etc/nginx/certs/server.crt;
    ssl_certificate_key /etc/nginx/certs/server.key;

    proxy_read_timeout 3600s;
    proxy_send_timeout 3600s;
    proxy_request_buffering off;

    location / {
        proxy_pass http://openlist:5244;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Range $http_range;
        proxy_set_header If-Range $http_if_range;
        proxy_redirect off;
        proxy_http_version 1.1;
        client_max_body_size 20000m;
    }
}
```
  3.配置FolderSync

  注意用户名密码需要在Openlist中设置，并给足权限

  服务器地址填`https://192.168.10.100/dav`，端口为5443，不要在路径中填写`/dav`，会一直报错，这是最大的坑

  底下只需勾选一个允许自签名证书即可

  测试一下一切正常即可使用
