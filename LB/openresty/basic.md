- content_by_lua_block 模块

访问：curl http://localhost:6699 -i
``` xml
http {
    server {
        #监听端口，若你的6699端口已经被占用，则需要修改
        listen 6699;
        location / {
            default_type text/html;
            content_by_lua_block {
                ngx.say("HelloWorld")
            }
        }
    }
}
```
ngx.location.capture_multi 函数，直接完成了两个子请求并行执

```
location = /sum {
    internal;
    content_by_lua_block {
        ngx.sleep(0.1)
        local args = ngx.req.get_uri_args()
        ngx.print(tonumber(args.a) + tonumber(args.b))
    }
}
location = /subduction {
    internal;
    content_by_lua_block {
        ngx.sleep(0.1)
        local args = ngx.req.get_uri_args()
        ngx.print(tonumber(args.a) - tonumber(args.b))
    }
}

location = /app/test_parallels {
    content_by_lua_block {
            local start_time = ngx.now()
            local res1, res2 = ngx.location.capture_multi( {
                {"/sum", {args={a=3, b=8}}},
                {"/subduction", {args={a=3, b=8}}}
            })
            ngx.say("status:", res1.status, " response:", res1.body)
            ngx.say("status:", res2.status, " response:", res2.body)
            ngx.say("time used:", ngx.now() - start_time)
        }
}
location = /app/test_queue {
content_by_lua_block {
    local start_time = ngx.now()
    local res1 = ngx.location.capture_multi( {
        {"/sum", {args={a=3, b=8}}}
    })
    local res2 = ngx.location.capture_multi( {
        {"/subduction", {args={a=3, b=8}}}
    })
    ngx.say("status:", res1.status, " response:", res1.body)
    ngx.say("status:", res2.status, " response:", res2.body)
    ngx.say("time used:", ngx.now() - start_time)
}
```
### 常用方法汇总
- ngx.exec 方法与 ngx.redirect
``` xml
location ~ ^/static/([-_a-zA-Z0-9/]+).jpg {
    set $image_name $1;
    content_by_lua_block {
        ngx.exec("/download_internal/images/"
        .. ngx.var.image_name .. ".jpg");
        };
}

location /download_internal {
    internal;
    # 这里还可以有其他统一的 download 下载设置，例如限速等
    alias ../download;
}
```
- 外部重定向
``` xml
location = /foo {
    content_by_lua_block {
        ngx.say([[I am foo]])
    }
}
location = / {
    rewrite_by_lua_block {
        return ngx.redirect('/foo');
    }
}
执
```

- 获取请求 uri 参数

访问请求：curl '127.0.0.1/print_param?a=1&b=2%26' -d 'c=3&d=4%26'

ngx.req.get_uri_args 、 ngx.req.get_post_args 获取数据来源是有明显区
别的，前者来自 uri 请求参数，而后者来自 post 请求内容。
``` xml
server {
    listen 80;
    server_name localhost;
    location /print_param {
    content_by_lua_block {
        local arg = ngx.req.get_uri_args()
        for k,v in pairs(arg) do
        ngx.say("[GET ] key:", k, " v:", v)
        end
        ngx.req.read_body() -- 解析 body 参数之前一定要先读取 bod
        y
        local arg = ngx.req.get_post_args()
        for k,v in pairs(arg) do
        ngx.say("[POST] key:", k, " v:", v)
        end
    }
}
```