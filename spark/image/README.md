# setup

```shell
cd ~repos/helm-chart/spark/image
docker build -t upupdididi/spark-2.3.1-with-hadoop2.6:1.0.0 .
```

# 说明

spark是用scala写的，但是运行时只需要java环境。scala代码经过编译已经成了java字节码。 

因此，基镜像用openjdk:8，不需要额外的scala包。

spark包下载地址：https://archive.apache.org/dist/spark/spark-2.3.1

坑：
不想和hadoop混在一起，所以刚开始下的spark-2.3.1-bin-without-hadoop.tgz包，spark-shell跑不起来。最终还是下了一个带hadoop jar的包spark-2.3.1-bin-hadoop2.6.tgz。

# test

login container：

```shell
docker run -it --name spark-test -d upupdididi/spark-2.3.1-with-hadoop-2.6:1.0.0
docker exec -it spark-test /bin/bash
```

在容器内操作：

```shell
# pwd /
cd spark-2.3.1-bin-hadoop2.6

# 1.test spark-shell
./bin/spark-shell

# 2.test start spark-master
./sbin/start-master.sh

# 3.test start spark-slave
./sbin/start-slave.sh spark://SPARK_MASTER_IP:7077

# jsp will show like follow
326 Jps
167 Master
252 Worker
```

