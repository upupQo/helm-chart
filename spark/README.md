#  install chart:

 `helm install spark ./spark`  

# run tests:

## test1:

login master & `cd /spark-2.3.1-bin-hadoop2.6` & run submit test to yarn: `./bin/spark-submit   --class org.apache.spark.examples.SparkPi   --master yarn   --deploy-mode cluster  ./examples/jars/spark-examples_2.11-2.3.1.jar   1000`  

在此期间，可以登到一台hadoop机子上(这里登的hadoop-hadoop-hdfs-nn-0)，用yarn logs命令查看这个正在跑的application的log：`yarn logs --applicationId application_1621259008100_0003`。

## test2:

login master & `cd /spark-2.3.1-bin-hadoop2.6` & start spark-shell: `./bin/spark-shell`

```shell
scala> var files=spark.read.format("text").load("hdfs://hadoop-hadoop-hdfs-nn-0.hadoop-hadoop-hdfs-nn.default:9000/input")
files: org.apache.spark.sql.DataFrame = [value: string]
scala> files.show()
```

在此期间，会在master上开一个4040端口，一个关于jobs的ui。

