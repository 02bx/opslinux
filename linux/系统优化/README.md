# 一、系统初始化

```
cd /etc && wget -O /etc/history_conf https://raw.githubusercontent.com/Lancger/opslinux/master/linux/%E7%B3%BB%E7%BB%9F%E4%BC%98%E5%8C%96/history_conf

cd /tmp && wget -O /tmp/initialization.sh https://raw.githubusercontent.com/Lancger/opslinux/master/linux/%E7%B3%BB%E7%BB%9F%E4%BC%98%E5%8C%96/Centos_initialization.sh && chmod +x /tmp/initialization.sh && sh /tmp/initialization.sh

#tomcat日志切割工具
cd /tmp/
wget -O /tmp/cronolog-1.6.2.tar.gz https://raw.githubusercontent.com/Lancger/opslinux/master/linux/%E7%B3%BB%E7%BB%9F%E4%BC%98%E5%8C%96/cronolog-1.6.2.tar.gz
tar -zxvf /tmp/cronolog-1.6.2.tar.gz
cd cronolog-1.6.2
./configure
make && make install
which cronolog
```

# 二、验证日志审计切割

```
logrotate -vf /etc/logrotate.d/shell_audit 

tail -100f /var/log/shell_audit/audit.log 
```

# 三、磁盘挂载

```
cat > /tmp/disk.sh << \EOF
#!/bin/bash
echo "n
p
1


w
" | fdisk /dev/vdb && mkfs.ext4 /dev/vdb1
echo '/dev/vdb1 /data0                  ext4    defaults        0 0' >> /etc/fstab
mkdir /data0
mount /dev/vdb1 /data0
df -h
EOF

chmod +x /tmp/disk.sh && sh /tmp/disk.sh
mkdir -p /data0/{opt,logs}
ln -s /data0/logs/ /opt/logs
chown -R www:www /data0/
chown -R www:www /opt/logs
mv /home/ /data0/
ln -s /data0/home/ /home
ls -l /data0/
ls -l /opt/
ls -l /home/

#ln -s /data /data0  (前面为目标，后面为软链)
```

# 四、java环境

```
echo "47.106.90.8 download.devops.com" >> /etc/hosts
cd /usr/local/src/
mkdir -p /opt/java
wget http://download.devops.com/jdk-8u211-linux-x64.tar.gz
tar -zxvf jdk-8u211-linux-x64.tar.gz
mv jdk1.8.0_211 /opt/java/
ls -l /opt/java/

vim /etc/profile
#在最后一行添加
#java environment
export JAVA_HOME=/opt/java/jdk1.8.0_211
export CLASSPATH=.:${JAVA_HOME}/jre/lib/rt.jar:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
export PATH=$PATH:${JAVA_HOME}/bin

source /etc/profile  #(生效环境变量)

java -version        #(检查安装 是否成功)
```

参考文档

https://jaminzhang.github.io/shell/Automated-Disk-Partion-Via-Shell-Script/  Shell 脚本自动化分区
