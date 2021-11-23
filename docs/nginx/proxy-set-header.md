## 前提条件

```
客户端请求web服务，客户端：
ip：192.168.223.1
```

```
nginx作为反向代理服务器：
192.168.223.136

nginx作为后端web服务器：
192.168.223.137
```

配置 nginx 转发到后端服务器，将左侧匹配到 `/proxy_path/` 开头的 url 全部转发到后端服务器 `192.168.223.137`

```
server {
    listen 8080;
    server_name 192.168.223.136;
    location / {
        root "/www/html";
        index index.html;
        #auth_basic "required auth";
        #auth_basic_user_file "/usr/local/nginx/users/.htpasswd";
        error_page 404 /404.html;
    }
    location /images/ {
        root "/www";
        rewrite ^/images/bbs/(.*\.jpeg)$ /images/$1 break;
        rewrite ^/images/www/(.*)$ http://192.168.223.136/$1 redirect;
    }
    location /basic_status {
        stub_status;
    }
    location ^~ /proxy_path/ {
        root "/www/html";
        index index.html;
        proxy_pass http://192.168.223.137/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        #proxy_set_header X-Forwarded-For $remote_addr;

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

location ^~ /proxy_path/ {
    root "/www/html";
    index index.html;
    proxy_pass http://192.168.223.137/;
}
```

## 属性测试

**proxy_set_header**：设置转发后的请求头的相关信息，现在测试各个 `proxy_set_header` 设置的变量的内容：

#### 1、proxy_set_header Host $host;

将136代理服务器，137后端服务器的log_format修改为如下：

```
log_format main '$remote_addr - $remote_user [$time_local] "$request" $http_host '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_forwarded_for"';
```

```
proxy_set_header Host $host;
// 这里的 Host 变量的值对应的就是日志中的 $http_host 的值
```

当客户端用户访问 `http://192.168.223.136:8080/proxy_path/index.html` 时

查看代理服务器和后端服务器的地址，可以发现 **$http_host** 对应的值为： `192.168.223.136:8080`

```
192.168.223.1 - - [18/Jul/2017:10:21:25 +0800] "GET /favicon.ico HTTP/1.1" **192.168.223.136:8080** 404 24 "http://192.168.223.136:8080/proxy_path/index.html" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36" "-"
```

 然后开启137后端nginx，查看日志： 

```
192.168.223.136 "192.168.223.1" - - [17/Jul/2017:17:06:44 +0800] "GET /index.html HTTP/1.0" "192.168.223.136" 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) like Gecko" "192.168.223.1"
```

 即验证了proxy_set_header Host $host;  $host就是nginx代理服务器，也就是客户端请求的host。 

####  **2、proxy_set_header Host $proxy_host;** 

顾名思义：请求头中的 host 设置为代理后的域名。

- 当配置了 upstream，那么 `$proxy_host` 中的值就是 upstream 的名字

```
upstream open-hz8443{
	server 10.60.6.184:8000 max_fails=1 fail_timeout=3s weight=10;
}
```

那么这里 $proxy_host 的值就是 open-hz8443。 

- 当没有配置  upstream，那么 `$proxy_host` 的值就是 `proxy_pass` 后面的 ip 和端口

将设置修改为上述 proxy_host 然后重启ngxin代理服务器136 

```
[root@wadeson nginx]# sbin/nginx -s reload
```

重新请求代理页面：`http://192.168.223.136:8080/proxy_path/index.html`，然后日志如下：

首先查看136代理服务器的日志：

```
192.168.223.1 - - [18/Jul/2017:10:30:12 +0800] "GET /proxy_path/index.html HTTP/1.1" 192.168.223.136:8080 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36" "-"
```

因为win10是 136 的客户端，请求的 host 为 `192.168.223.136:8080`，而 nginx 代理服务器作为 137 后端服务器的客户端，将请求的报文首部重新封装，将 proxy_host 封装为请求的 host

那么 137 上面日志请求的 host 就是其自身，proxy_host 就是代理服务器请求的 host 也就是后端服务器137

```
192.168.223.136 "192.168.223.1" - - [18/Jul/2017:10:30:12 +0800] "GET /index.html HTTP/1.0" "192.168.223.137" 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36" "192.168.223.1"
```

####  **3、proxy_set_header Host $host:$proxy_port;** 

了解了上面的知识，那么此处对应的host就知道代表的啥了，$host代表转发服务器，$proxy_port代表136转发服务器请求后端服务器的端口，也就是80

于是观察136、137的日志进行验证：

```
192.168.223.1 - - [18/Jul/2017:10:38:38 +0800] "GET /proxy_path/index.html HTTP/1.1" 192.168.223.136:8080 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36" "-"

192.168.223.136 "192.168.223.1" - - [18/Jul/2017:10:38:38 +0800] "GET /index.html HTTP/1.0" "192.168.223.136:80" 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36" "192.168.223.1"
```

####  **4、proxy_set_header X-Real-IP $remote_addr;** 

将$remote_addr的值放进变量X-Real-IP中，此变量名可变，$remote_addr的值为客户端的ip

nginx转发136服务器日志格式为：

```
log_format main '$remote_addr - $remote_user [$time_local] "$request" $http_host '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_forwarded_for"';
```

 nginx后端137服务器的日志格式： 

```
log_format main '$remote_addr "$http_x_real_ip" - $remote_user [$time_local] "$request" "$http_host" '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_forwarded_for"';
```

两者区别在于**"$http_x_real_ip"，添加了这个变量的值**

**重新请求需要访问的地址http://192.168.223.136:8080/proxy_path/index.html**

**136的日志：**

```
192.168.223.1 - - [18/Jul/2017:10:45:07 +0800] "GET /proxy_path/index.html HTTP/1.1" 192.168.223.136:8080 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36" "-"
```

 **137的日志：** 

```
192.168.223.136 "192.168.223.1" - - [18/Jul/2017:10:45:07 +0800] "GET /index.html HTTP/1.0" "192.168.223.136:80" 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36" "192.168.223.1"
```

 **红色标记的就是"$http_x_real_ip"的值，即可以看见用户真实的ip，也就是客户端的真实ip。** 

####  **5、proxy_set_header X-Forwarded-For $remote_addr;** 

**理解了上面的含义那么这个封装报文的意思也就请求了**

**首先还是比对136和137的日志格式：**

**136代理服务器的日志格式：**

```
log_format main '$remote_addr - $remote_user [$time_local] "$request" $http_host ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" **"$http_x_forwarded_for"**';
```

137后端服务器的日志格式：

```
log_format main '$remote_addr "$http_x_real_ip" - $remote_user [$time_local] "$request" "$http_host" ' '$status $body_bytes_sent "$http_referer" ' '"$http_user_agent" **"$http_x_forwarded_for"**';
```

重新请求需要访问的地址 `http://192.168.223.136:8080/proxy_path/index.html`

**136的日志显示：**

```
192.168.223.1 - - [18/Jul/2017:10:51:25 +0800] "GET /proxy_path/index.html HTTP/1.1" 192.168.223.136:8080 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36" "-"
```

**最后一个字段"$http_x_forwarded_for"对应的为空值**

**137的日志显示：**

```
192.168.223.136 "192.168.223.1" - - [18/Jul/2017:10:51:25 +0800] "GET /index.html HTTP/1.0" "192.168.223.136:80" 304 0 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36" "192.168.223.1"
```

**可以看出137后端服务器成功的显示了真实客户端的ip。**

####  **6、proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;** 

**5、6两者的区别：**

**在只有一个代理服务器的转发的情况下，两者的效果貌似差不多，都可以真实的显示出客户端原始ip**

**但是区别在于：**

$proxy_add_x_forwarded_for变量包含客户端请求头中的"X-Forwarded-For"，与$remote_addr两部分，他们之间用逗号分开。

举个例子，有一个web应用，在它之前通过了两个nginx转发，www.linuxidc.com 即用户访问该web通过两台nginx。

在第一台nginx中,使用

```
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

现在的$proxy_add_x_forwarded_for变量的"X-Forwarded-For"部分是空的，所以只有$remote_addr，而$remote_addr的值是用户的ip，于是赋值以后，X-Forwarded-For变量的值就是用户的真实的ip地址了。

到了第二台nginx，使用

```
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

现在的$proxy_add_x_forwarded_for变量，X-Forwarded-For部分包含的是用户的真实ip，$remote_addr部分的值是上一台nginx的ip地址，于是通过这个赋值以后现在的X-Forwarded-For的值就变成了

“用户的真实ip，第一台nginx的ip”。