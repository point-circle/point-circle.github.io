# 在Windows上用certbot获取Lets Encrypt证书并配置Nginx

本教程适用于Windows系统。在Cygwin上利用certbot获取Lets Encrypt的证书，并配置Windows版本的nginx。

编写日期：2019.04.10

**注意：如果采用Cygwin版本的nginx，在指定证书路径时，需要使用不同风格的路径**

## 安装certbot

### 在Cygwin里为安装certbot做准备

先用Cygwin的官方安装程序将Cygwin更新到最新的版本。本教程假定会使用`apt-cyg`。

```
apt-cyg update
apt-cyg install gcc-core python36-devel python36-pip libffi-devel libcrypt-devel libssl-devel python3-devel
```

### 用pip3安装certbot

```
pip3 install certbot
```

## 申请证书

dns challenge 方法适用于没法使用http 80端口的情况。如果可以使用80端口，则使用另一套简单的方法，该方法本教程不做描述。

比如你的域名是 `example.com`

```
certbot -d "*.example.com" --manual --preferred-challenges dns-01 certonly
```

## 配置 Nginx

```
server {
        listen       18081 ssl;
        server_name  localhost;

        ssl on;
        ssl_certificate d:/cygwin64/etc/letsencrypt/archive/example.com/fullchain1.pem;
        ssl_certificate_key d:/cygwin64/etc/letsencrypt/archive/example.com/privkey1.pem;
        ssl_trusted_certificate d:/cygwin64/etc/letsencrypt/archive/example.com/chain1.pem;
        
        ......
}
```

## 用crontab来定时更新 Lets Encrypt的证书

因为Lets Encrypt的证书只有3个月有效期。
