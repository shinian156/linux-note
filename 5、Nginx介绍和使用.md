

### 正向代理

正向代理是为客户端服务的，通过代理客户端的请求，去访问客户端本身无法直接访问的服务器，翻墙工具就是最常见的正向代理。正向代理对于客户端透明，对于服务器端不透明。

nginx配置示例：
```
server {
    resolver 8.8.8.8;       #指定DNS服务器IP地址 
    listen 80;
    location / {
        proxy_pass http://$host;     #设定代理服务器的协议和地址 
        proxy_set_header HOST $host;
        proxy_buffers 256 4k;
        proxy_max_temp_file_size 0k;
        proxy_connect_timeout 30;
        proxy_send_timeout 60;
        proxy_read_timeout 60;
        proxy_next_upstream error timeout invalid_header http_502;
    }
}
```

### 反向代理

反向代理是指为服务端服务的，反向代理接收来自客户端的请求，帮助服务器进行请求转发、负载均衡等。通常解决前端跨域问题也是通过反向代理实现的。反向代理对于服务端是透明的，对于客户端是不透明的。

nginx配置示例：

```
server {
    listen       80;
    server_name  xx.a.com; // 监听地址
    location / {
        root html;
        proxy_pass xx.b.com; // 请求转向地址
        index index.html; // 设置默认页
    }
}
```

### 负载均衡

负载均衡是指nginx将大量客户端的请求合理地分配到各个服务器上，以达到资服务端资源的充分利用和更快的响应请求。

nginx配置示例：

```
upstream balanceServer {
    server 1.1.2.3:80;
    server 1.1.2.4:80;
    server 1.1.2.5:80;
}

server {
        server_name  xx.a.com;
        listen 80;
        location /api {
            proxy_pass http://balanceServer;
        }
    }
```


### 常用的负载均衡策略：

1.轮询策略 
默认策略，遍历服务器节点列表，逐一分配请求，如果服务器down掉，自动剔除。
2.加权轮询 每台服务器有不同的权重，一般权重越大意味着该服务器的性能越好，可以承载更多的请求。
```
upstream balanceServer {
    server 1.1.2.3:80 weight=5;
    server 1.1.2.4:80 weight=10;
    server 1.1.2.5:80 weight=20;
}
```
3.最小连接策略 
将请求优先分配给压力较小的服务器，避免压力大的服务器添加更多的请求。
```
upstream balanceServer {
    least_conn;
    server 1.1.2.3:80;
    server 1.1.2.4:80;
    server 1.1.2.5:80;
}
```
4.最快响应时间策略 
将请求有限分配给响应时间最短的服务器。
```
upstream balanceServer {
    fair;
    server 1.1.2.3:80;
    server 1.1.2.4:80;
    server 1.1.2.5:80;
}
```
5.客户端ip绑定 
将来自同一个ip的请求永远只分配给一台固定的服务器。
```
upstream balanceServer {
    ip_hash;
    server 1.1.2.3:80;
    server 1.1.2.4:80;
    server 1.1.2.5:80;
}
```


参考文章：

<!-- https://cloud.tencent.com/developer/article/1480498 -->

<!-- 
    原文地址： [记一次前端面试记录（头条、蚂蚁）](https://juejin.im/post/5ea528496fb9a03c576cceac#heading-1)
 -->


[从原理到实战，彻底搞懂Nginx（高级篇）](https://juejin.im/post/5e1c263e5188254dc74a3b23)

[前端开发者必备的 Nginx 知识](https://juejin.im/post/5d01bcc7f265da1b91638d75)
