# nginx tls debug 调试
## 生成服务端证书
```shell
cd server-cert
# 生成 CA 证书
openssl req -new -x509 -nodes -keyout ca.key -out ca.crt -days 3650 -config ca.conf
# 生成服务端证书
openssl req -new -nodes -keyout server.key -out server.csr -config client.conf
openssl x509 -req -days 3650 -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt -sha256 -extfile client.conf -extensions v3_req
```
## 生成客户端证书
```shell
cd client-cert
# 生成 CA 证书
openssl req -new -x509 -nodes -keyout ca.key -out ca.crt -days 3650 -config ca.conf
# 生成服务端证书
openssl req -new -nodes -keyout client.key -out client.csr -config client.conf
openssl x509 -req -days 3650 -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt -sha256 -extfile client.conf -extensions v3_req
```
## 启动 nginx 服务
```shell
docker-compose up -d
```
## 测试
```shell
curl --cert ./client-cert/client.crt --key ./client-cert/client.key --cacert ../server-cert/ca.crt https://127.0.0.1:443
# 查看 nginx 日志，会有 tls 握手日志
docker logs -f nginx --tail 200
```