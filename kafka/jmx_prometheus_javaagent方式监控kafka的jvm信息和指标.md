# 一、下载jmx_prometheus_javaagent和kafka.yml

```
#下载jmx程序包
cd /usr/local/src/
wget https://raw.githubusercontent.com/prometheus/jmx_exporter/master/example_configs/kafka-0-8-2.yml
wget https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.6/jmx_prometheus_javaagent-0.6.jar

#移动到kafka程序目录
mv kafka-0-8-2.yml /usr/local/kafka/
mv jmx_prometheus_javaagent-0.6.jar /usr/local/kafka/
```

# 二、编辑启动脚本
```bash
#打开 kafka-server-start.sh 文件，注意加在脚本前面
vim /usr/local/kafka/bin/kafka-server-start.sh

export JMX_PORT="9999"
export KAFKA_OPTS="-javaagent:/usr/local/kafka/jmx_prometheus_javaagent-0.6.jar=9991:/usr/local/kafka/kafka-0-8-2.yml"
```

# 三、然后重启kafka。
```
cd /usr/local/kafka/
./bin/kafka-server-stop.sh
nohup ./bin/kafka-server-start.sh config/server.properties &

#访问 http://localhost:9991/metrics 可以看到各种指标了。
```

参考资料：

https://blog.csdn.net/qq_25934401/article/details/84840740  Prometheus 监控之 kafka
