# 在Windows上用certbot获取Lets Encrypt证书并配置Nginx

本教程适用于Windows系统。在Cygwin上利用certbot获取Lets Encrypt的证书，并配置Windows版本的nginx。

编写日期：2019.04.10

**注意：如果采用Cygwin版本的nginx，在指定证书路径时，需要使用不同风格的路径。实际上如果服务器一定是Windows，那么用Cygwin的nginx应该是更好的选择。**

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

**这里用/etc/letsencrypt下的archive目录而没有用live目录，是因为Windows版的nginx不支持读取Cygwin下的符号链接。在更新证书以后，上述三条路径需要更新。**

## 用crontab来定时更新 Lets Encrypt的证书

因为Lets Encrypt的证书只有3个月有效期。运行`crontab -e`，输入如下内容，实现每隔3个月，8号凌晨一点半更新证书：

```
30 01 08 */3 * /usr/bin/certbot renew
```
