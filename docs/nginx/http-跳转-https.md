以 `http://www.a.com` 为例，要求所有访问该页面的请求全部跳转至 `https://www.a.com`，且请求的 `URI` 和参数 `$query_string` 要保留下来。

常见的几种方法：

## 1、使用 `if` 进行协议判断 -- 最差

这种情况下，多为把 `http` 和 `https` 写在了同一个 `server` 中，配置如下：

```nginx
server {
    listen	80  default_server;
    listen 443	ssl default_server;
    server_name	www.a.com;
    ssl_certificate	'/data/nginx/ssl/nginx.crt';
    ssl_certificate_key '/data/nginx/ssl/nginx.key';
    root	/data/nginx/a;
    charset	utf-8;
    if ($scheme = http) {
        rewrite ^/(.*)$	 https://www.a.com/$1 permanent;
    }
}
```

http https 同时可以访问，http 不跳转 https

```nginx
server {
	listen 80;
    listen 443 ssl;
    server_name www.a.com a.com;
    ssl_certificate	'/data/nginx/ssl/nginx.crt';
    ssl_certificate_key '/data/nginx/ssl/nginx.key';
    charset	utf-8;
    root	/data/nginx/domain/;
    location {
        index index.html;
    }
}
```

这种配置看起来简介很多，但是性能是最差的。首先每次连接进来都需要 `Nginx` 进行协议判断，其次判断为 `http` 协议时进行地址匹配、重写、返回、再次判断，最后还有正则表达式的处理.......，所以，生产上我们极不建议这种写法。另外，能少用 `if` 的尽量不用，如果一定要使用，也最好在 `location` 段，并且结合 `return` 或者 `rewrite ... last` 来使用。

## 2、`rewrite` 方法1 -- 差

```nginx
server {
    listen 80  default_server;
    server_name www.a.com a.com;
    rewrite ^/(.*)$	 https://www.a.com/$1 permanent;
}
server {
    listen 443 ssl default_server;
    server_name www.a.com a.com;
    ssl_certificate	'/data/nginx/ssl/nginx.crt';
    ssl_certificate_key '/data/nginx/ssl/nginx.key';
    root	/data/nginx/a;
    charset	utf-8;
}
```

```
使用 curl http链接，会重定向到 https链接，301状态码 location 中是重写后的地址
```

## 3、`rewrite` 方法2 -- 好

不使用正则表达式，而使用变量来提升性能

```
server {
    listen 80;
    server_name www.a.com a.com;
    rewrite ^  https://www.a.com$request_uri? permanent;
}
server {
    listen 443 ssl;
    server_name www.a.com a.com;
    ssl_certificate	'/data/nginx/ssl/nginx.crt';
    ssl_certificate_key '/data/nginx/ssl/nginx.key';
    charset	utf-8;
    root	/data/nginx/domain/;
    location {
        index index.html;
    }
}
```

## 4、使用 `return` 实现最优解 -- 最好（推荐）

虽然上面我们使用参数代替了正则，但是 `rewrite` 规则会先对 `URL` 进行匹配，匹配上了再执行相应的规则。而 `return` 没有匹配 `URL` 层面的性能消耗，直接返回用户新的连结，所以是最优的解决方案

```nginx
server {
    listen 80;
    server_name www.a.com a.com;
    # return 302  https://$host$request_uri;
    return 302  https://www.a.com$request_uri;
}
server {
    listen 443 ssl;
    server_name www.a.com a.com;
    ssl_certificate	'/data/nginx/ssl/nginx.crt';
    ssl_certificate_key '/data/nginx/ssl/nginx.key';
    charset	utf-8;
    root	/data/nginx/domain/;
    location {
        index index.html;
    }
}
```

注意：在 `return` 中，`$request_uri` 后面不用加 `?` （加 `?` 用来避免携带参数是 `rewrite` 中的特性）。

如果希望实现永久重定向，则使用 `return 301 https://xxx`，不过我们两个域名都会使用，所以更多情况下使用 `302` 临时重定向。 