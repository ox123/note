### nginx官网地址
https://nginx.org/en/docs/http/websocket.html

```xml
location / {
    proxy_pass http://127.0.0.1:9999;
    proxy_connect_timeout 60;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-NginX-Proxy true;

    # 下面是关键
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    # 这是配置webpysessoin丢失的问题
    fastcgi_param  SCRIPT_NAME        "";
}
```

### websocket配置
- https://www.jianshu.com/p/07f37dd4657f