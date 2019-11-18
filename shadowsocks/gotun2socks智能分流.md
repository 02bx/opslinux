# 一、前言

本文采用Shadowsocks实现与外网通讯，如有需要，你也可以换成其他的软件，例如Gost/ShadowsocksR/V2Ray等。

本教程基于Centos7 x86_64环境建立，其他环境大同小异。

# 二、ss-server服务端
```bash
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ss-go.sh && chmod +x ss-go.sh && bash ss-go.sh
 
#Shadowsocks 用户配置：
————————————————
地址   : 202.182.106.129
端口   : 11451
密码   : 62903bcf7df17c6b
加密   : aes-128-cfb
链接  [ipv4] : ss://YWVzLTEyOC1jZmI6NjI5MDNiY2Y3ZGYxN2M2YkAyMDIuMTgyLjEwNi4xMjk6MTE0NTE 
二维码[ipv4] : http://doub.pw/qr/qr.php?text=ss://YWVzLTEyOC1jZmI6NjI5MDNiY2Y3ZGYxN2M2YkAyMDIuMTgyLjEwNi4xMjk6MTE0NTE

详细日志模式   : NO
```

# 三、环境准备

## 1、安装shadowsocks-libev

```bash
#安装报错
Error: Package: shadowsocks-libev-3.1.3-1.el7.centos.x86_64 (librehat-shadowsocks)
           Requires: libsodium >= 1.0.4
Error: Package: shadowsocks-libev-3.1.3-1.el7.centos.x86_64 (librehat-shadowsocks)
           Requires: mbedtls
```

```bash
#需要首先启用 EPEL，再安装 shadowsocks-libev
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

wget https://copr.fedorainfracloud.org/coprs/librehat/shadowsocks/repo/epel-7/librehat-shadowsocks-epel-7.repo -O /etc/yum.repos.d/shadowsocks-epel-7.repo
yum clean all
yum install -y shadowsocks-libev ipset
```

## 3、配置shadowsocks-libev
```bash
cd /etc/shadowsocks-libev

cat >/etc/shadowsocks-libev/config.json<<\EOF
{
    "server":"202.182.106.129",
    "mode":"tcp_and_udp",
    "server_port":11451,
    "local_port":1080,
    "password":"62903bcf7df17c6b",
    "timeout":300,
    "fast_open":true,
    "method":"aes-128-cfb"
}
EOF
```

## 4、配置ss-local
```bash
cat >/etc/systemd/system/ss-local.service<<\EOF
[Unit]
Description=Shadowsocks-Libev Client Service
After=network.target

[Service]
User=nobody
Group=nogroup
LimitNOFILE=1048576
CapabilityBoundingSet=~CAP_SYS_ADMIN
ExecStart=/usr/bin/ss-local -u -c /etc/shadowsocks-libev/config.json

[Install]
WantedBy=multi-user.target
EOF

#重启服务
systemctl enable ss-local && systemctl start ss-local
```
## 5、测试验证
```bash
curl -s --socks5 127.0.0.1:1080 google.com

<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
如一切无误，此时ss-local已经开始正常工作。
```

# 四、安装gotun2socks
```bash
cd /usr/local/src/
wget https://github.com/eycorsican/go-tun2socks/releases/download/v1.16.7/tun2socks-linux-amd64
chmod +x tun2socks-linux-amd64

./tun2socks-linux-amd64
2019/11/18 20:11:04 Running tun2socks

ip addr add 192.168.0.1/24 dev tun1
```


参考资料：

https://luxing.im/socks5-as-a-vpn/  

https://xn--m80a.ml/crossgfw/5.html#mdui-dialog
