## 1.部署
**1.1环境准备**
> redis

> nginx

[下载DNSLOG前端和后端程序](https://github.com/joke0jie/DNSLOG/releases)

#### 1.2.安装redis

#### 1.3.安装nginx
**nginx 配置**
```
server {
    listen       80;
    access_log  /var/log/nginx/host.access.log  main;
    location / {
        root   /root/dnslog/dnslog;   		#更改为你自己前端所在的目录 
        index  index.html index.htm;
    }
	    location  /api/ {
	        proxy_pass http://127.0.0.1:443/;  #配置GDNSLOG的转发端口一般默认就行了
	    	}
	}
```


#### 1.4.启动GDNSLOG

可以使用定时任务轮循

```crontab -e```

```30 * * * * /bin/bash /root/damon.sh```

```#!/bin/bash

COUNT=$(ps -ef |grep GDNslog_linux |grep -v "grep" |wc -l)
echo $COUNT
if [ $COUNT -eq 0 ]; then
        cd /root/dnslog
        ./GDNslog_linux
        cd /usr/local/bin
        ./redis-server
else
        echo not run
fi
``` 

**1.5.域名解析**

假设根域名是dnslog.pub，服务器IP是10.10.10.10进行以下配置(建议使用华为云做域名解析，TTL可以设置成100，其它的一般是600)

> 配置A记录，子域名ns，解析到10.10.10.10 

> 配置NS记录，子域名dns，解析到ns.dnslog.pub 

> 配置A记录，子域名web，解析到10.10.10.10 

web.dnslog.pub 用于访问平台web

## 2.使用 

假如前面的都已经配置好了。

**默认用户名密码:admin/dnslog**

ping命令测试一下

```ping dnslogtest.dns.dnslog.pub``` 


如下图所示：

![avatar](https://github.com/joke0jie/DNSLOG/blob/master/test.png)

![avatar](https://github.com/joke0jie/DNSLOG/blob/master/dis.png)

#### 2.1 API接口使用

```
http://web.dnslog.mobi:443/v1/dnslog?token={yourtoken}&key=1588152736960
首次登录请修改你的token
```

**示例结果**

```{"id":26211,"time":"2020-04-29 17:32:17","type":1,"name":"1588152736960.dns.dnslog.pub","raddr":"183.xx.xx.102:56261"}```



