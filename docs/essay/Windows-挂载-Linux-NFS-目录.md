### 创建一个目录作为共享文件目录

```
mkdir -p /Project/File
```

### 给目录增加读写权限

```
chmod a+rw /Project/File
```

## Ubuntu

- 安装 NFS 服务端

```bash
apt-get update
apt-get install -y nfs-kernel-server
```

## CentOS

- 安装 NFS 服务端

```bash
yum update
yum install -y nfs-server
```

- 配置 NFS 服务目录，打开文件 `vi /etc/exports`，在尾部新增一行，内容如下
  - `/Project/File`：作为服务目录向客户端开放
  - *：表示任何 IP 都可以访问
  - rw：读写权限
  - sync：同步权限
  - no_subtree_check：表示如果输出目录是一个子目录，NFS 服务器不检查其父目录的权限

```
/Project/File *(rw,sync,no_subtree_check,no_root_squash,insecure)
```

## 常见问题

- 网络错误 - 53

  关闭 linux 系统防火墙

## CentOS8 firewall 相关命令

```
#进程与状态相关
systemctl start firewalld.service            #启动防火墙
systemctl stop firewalld.service             #停止防火墙
systemctl status firewalld                   #查看防火墙状态
systemctl enable firewalld             #设置防火墙随系统启动
systemctl disable firewalld                #禁止防火墙随系统启动
firewall-cmd --state                         #查看防火墙状态
firewall-cmd --reload                        #更新防火墙规则
firewall-cmd --list-ports                    #查看所有打开的端口
firewall-cmd --list-services                 #查看所有允许的服务
firewall-cmd --get-services                  #获取所有支持的服务

#区域相关
firewall-cmd --list-all-zones                    #查看所有区域信息
firewall-cmd --get-active-zones                  #查看活动区域信息
firewall-cmd --set-default-zone=public           #设置public为默认区域
firewall-cmd --get-default-zone                  #查看默认区域信息


#接口相关
firewall-cmd --zone=public --add-interface=eth0  #将接口eth0加入区域public
firewall-cmd --zone=public --remove-interface=eth0       #从区域public中删除接口eth0
firewall-cmd --zone=default --change-interface=eth0      #修改接口eth0所属区域为default
firewall-cmd --get-zone-of-interface=eth0                #查看接口eth0所属区域

#端口控制
firewall-cmd --query-port=8080/tcp             # 查询端口是否开放
firewall-cmd --add-port=8080/tcp --permanent               #永久添加8080端口例外(全局)
firewall-cmd --remove-port=8800/tcp --permanent            #永久删除8080端口例外(全局)
firewall-cmd --add-port=65001-65010/tcp --permanent      #永久增加65001-65010例外(全局)
firewall-cmd  --zone=public --add-port=8080/tcp --permanent            #永久添加8080端口例外(区域public)
firewall-cmd  --zone=public --remove-port=8080/tcp --permanent         #永久删除8080端口例外(区域public)
firewall-cmd  --zone=public --add-port=65001-65010/tcp --permanent   #永久增加65001-65010例外(区域public)
```

