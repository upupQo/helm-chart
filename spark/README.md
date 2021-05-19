#  install chart:

install spark chart之前，先install hadoop chart。因为spark chart当前模式是spark on yarn，yarn是在hadoop chart里面启的。

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

## test3:

login master & `cd /spark-2.3.1-bin-hadoop2.6` & start spark-shell: `./bin/spark-shell`

```scala
import org.apache.spark.sql._
val rows=Seq(Row("person1","junior",7000,"2000-12-01"),Row("person2","junior",8000,null),Row("person3","middle",15000,null),Row("person4","middle",16000,null),Row("person5","senior",30000,null),Row("person6","senior",50000,null))

import org.apache.spark.sql.types._
val structSchema=StructType(Array(StructField("name",StringType,true),StructField("level",StringType,true),StructField("salary",IntegerType,true),StructField("date",StringType,true)))

val df=spark.createDataFrame(spark.sparkContext.parallelize(rows),structSchema)

df.show()

df.createOrReplaceTempView("person")
spark.sql("select * from person").show()

//mutil partition & wirte as parquet
df.write.partitionBy("level","date").parquet("test_data_dir")

//mutil partition & wirte as json
df.write.partitionBy("level","date").json("test_data_dir_json")
```

因为配置了hadoop，文件已经写到hdfs里面了，通过hadoop-namenode-ui查看(先minikube kubectl -- portforward port,再VNC连接到容器里面的浏览器，在里面打开url访问)：http://hadoop-hadoop-hdfs-nn-0.hadoop-hadoop-hdfs-nn.default:50070/

![](https://github.com/upupQo/helm-chart/raw/main/spark/snap/files.png)
