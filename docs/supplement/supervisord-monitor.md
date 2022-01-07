supervisord-monitor是对supervisord的一个集中化管理工具，可以对supervisor统一化操作

[Supervisord Monitor Github 地址](https://github.com/mlazarov/supervisord-monitor)

supervisord-monitor 是一个 php 应用，所以需要 php 环境

参考：https://blog.csdn.net/geerniya/article/details/80107761

## 安装 php php-fpm

```bash
yum install php php-fpm -y
```

## 启动 php-fpm

```bash
#执行以下命令启动php-fpm
systemctl start php-fpm
#查看php-fpm启动状态
systemctl status php-fpm
#查看自启动情况
systemctl list-unit-files | grep php-fpm
#开机自启动
systemctl enable php-fpm
```

## 安装 supervisord-monitor

```bash
# 下载
git clone https://github.com/mlazarov/supervisord-monitor.git

# 生成配置文件
cd supervisord-monitor/
cp application/config/supervisor.php.example application/config/supervisor.php
```

## 修改配置文件

需要监控的每一个 supervisor 节点，都需要开启 web 监控以及设置访问用户名密码

```
$config['supervisor_servers'] = array(
        'mweb08' => array(
                'url' => 'http://mweb08/RPC2',
                'port' => '9001',
                'username' => 'admin',
                'password' => '123456'
        ),
        'mweb07' => array(
                'url' => 'http://mweb07/RPC2',
                'port' => '9001',
                'username' => 'admin',
                'password' => '123456'
        ),
);
```

## 添加 nginx 对 sm 的支持

```
    server {
       listen 82;
       server_name  localhost;
       set $web_root /data/web/supervisord-monitor/public_html;
       root $web_root;
       index  index.php index.html index.htm;

       location / {
            try_files $uri $uri/ /index.php;
       }

       location ~ \.php$ {
           fastcgi_pass   127.0.0.1:9000;
           fastcgi_index  index.php;
           include        fastcgi_params;
           fastcgi_param  SCRIPT_FILENAME $web_root$fastcgi_script_name;
           fastcgi_param  SCHEME $scheme;
        }

     }
```

