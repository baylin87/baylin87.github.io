---
title: Nginx+Hexo+SSL搭建个人站点
tags:
  - Nginx
  - Hexo
  - Linux
comments: true
date: 2017-11-28 14:09:02
categories: 杂七杂八
permalink:
---

给闲置VPS增加点用处系列

<!--more-->

# Nginx

Nginx相关安装配置参照我的[上一篇关于Nginx配置的文章](https://binlv.top/2017/11/27/20171127-部署Nginx服务与基本配置/)



# Hexo 部署至Nginx

一般有三种方法

- git设置git hook方式
- rsync同步本地Hexo至VPS
- VPS上部署Hexo，并执行`hexo server` ，再配置Nginx反向代理，让域名指向 http://localhost:4000

## git hook方式

### 配置服务器环境

安装git并创建仓库，设置git hook

```bash
apt-get install git -y
mkdir <git工作目录> && cd <git工作目录>
git init --bare
nano <git工作目录>/hook/post-reveive
```

将以下内容修改好放入`post-reveive`中

```bash
#!/bin/bash
GIT_REPO=/var/www/blog.git  #git仓库
TMP_GIT_CLONE=/tmp/blog
PUBLIC_WWW=/var/www/blog #网站目录
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
```

或使用如下命令放入`post-reveive`中：

```bash
#!/bin/bash -l
git --work-tree=/var/www/blog --git-dir=/var/www/blog.git checkout -f
```



赋予执行权限

```bash
chmod +x post-receive
```

### 在本机配置

在博客目录下运行下面命令，安装 git 部署工具。

```bash
$ npm install hexo-deployer-git --save
```

修改博客的配置文件 _config.yml，修改deploy选项：

```bash
deploy: 
  type: git
  repository: 用户名@服务器地址:/var/www/blog.git
  branch: master
```



# 开启站点HTTPS

https需要授权证书，这里使用的是[Let's Encrypt](https://letsencrypt.org/)家的免费证书。

对了，要开启HTTPS首先你得有个人域名，因为证书是给域名授权的。至于域名申请，万网之类的都行，然后解析指向你的服务器。



## 配置过程

安装

```bash
apt-get install letsencrypt
```

获取证书

```bash
letsencrypt certonly
```

根据提示完成获取，中间需要输入你的域名，最后出现：

```bash
- Congratulations! Your certificate and chain have been saved at
  /etc/letsencrypt/live/<你的域名>/fullchain.pem. Your cert will
  expire on 2017-09-17. To obtain a new or tweaked version of this
  certificate in the future, simply run certbot-auto again. To
  non-interactively renew *all* of your certificates, run
  "certbot-auto renew"
- If you like Certbot, please consider supporting our work by:

  Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
  Donating to EFF:                    https://eff.org/donate-le
```
  就成功了，看提示，所有的证书文件在 `/etc/letsencrypt/live/你的域名 `下，里面有下面几个文件证书文件，引用别人的解释下：

>- `privkey.pem `
>
>这是私匙，对应 Nginx 的 ssl_certificate_key 选项，或者 Apache2 的 SSLCertificateKeyFile 选项。
>
>- `cert.pem` 
>
>服务器证书，这个只有 Apache2 低于 2.4.8 版本需要，对应 SSLCertificateFile 选项。
>
>- `chain.pem `
>
>除服务器证书之外的所有证书，对于 1.3.7 版以上的 Nginx 对应 ssl_trusted_certificate 选项，对于低于2.4.8 的 Apache2 对应 SSLCertificateChainFile 选项。
>
>- `fullchain.pem `
>
>包括上面的服务器证书和其他证书, Nginx 对应 ssl_certificate 选项，2.4.8 版以上的 Apache2 对应 SSLCertificateFile 选项。
>
>
>
>如果是 Nginx，需要上面的 privkey.pem（对应 ssl_certificate_key 选项）和 fullchain.pem （对应 ssl_certificate 选项），还需要一个 dhparam.pem（对应 ssl_dhparam 选项） 需自己生成：
>
>openssl dhparam -out /etc/nginx/ssl/dhparam.pem 2048
>
>这个文件创建稍微需要一点时间，注意看我的路径，我的是在 /etc/nginx/ssl/ 下，如果没有ssl这个目录就自己建一个，当然你换其他地方也行。



有了这三个文件: `privkey.pem`、`fullchain.pem` 、`dhparam.pem` 就可以配置 Nginx 了。


## 配置Nginx
编辑 `/etc/nginx/conf.d/xx.conf`

```bash
server {
        listen 80;
        server_name binlv.top;
    #实现访问http的时候自动跳转到https
        return 301 https://$server_name$request_uri;
}

server {
        listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name binlv.top;
    
    #不输出 Nginx 版本号及其他错误信息
    server_tokens off;
    
    # 开启 HSTS,这么写是为了提交到 https://hstspreload.org/
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
        # 启用 XSS 保护，检查到 XSS 攻击时，停止渲染页面。
    add_header X-XSS-Protection "1; mode=block";
    
        ssl_certificate /etc/letsencrypt/live/binlv.top/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/binlv.top/privkey.pem;
        ssl_dhparam /etc/nginx/ssl/dhparam.pem;
        ssl_session_cache shared:SSL:50m;
        ssl_session_timeout 1d;
        ssl_session_tickets off;
        ssl_protocols    TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
        ssl_prefer_server_ciphers on;
        ssl_stapling on;
        ssl_stapling_verify on;
    
    #设置根目录，必须为已存在目录	
    location / {
        root /var/www/blog;
        index index.php index.htm index.html;
    }
}
```

更新配置后要重启一下Nginx
```bash
systemctl restart nginx
```

## 设置证书自动更新
Let’s Encrypt SSL 免费证书为短期证书，只有 90 天期限，提示到期前可以运行命令 `letsencrypt renew` 续期

实现定时更新证书，我们可以用linux自带的定时器crontab
```bash
$ crontab -e
```
输入：　
```bash
　　30 2 1 * * /usr/bin/letsencrypt renew >> /var/log/letsencrypt/le-renew.log
　　35 2 1 * * /usr/bin/systemctl reload nginx
```

## 取消授权

如果想把授权的域名取消该怎么办呢？因为免费证书如果不定期更新的话几个月就会自动过期，所以删掉本地部署时对应创建的几个文件就行啦。

```bash
rm -rf /etc/letsencrypt/live/www.example.com/
rm -rf /etc/letsencrypt/archive/www.example.com/
rm /etc/letsencrypt/renewal/www.example.com.conf
```

# 参考文档

很多。。。

- [CentOS 7配置Nginx+SSL，参考](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-centos-7)
- [git hooks 官方文档](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90)



本文完。