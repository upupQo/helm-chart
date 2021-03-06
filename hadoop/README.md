# upupQo  

## From

https://github.com/helm/charts/tree/master/stable/hadoop

## 操作  
install chart: `helm install hadoop --set yarn.nodeManager.replicas=1 ./hadoop`  
access yarn-ui: `minikube kubectl -- port-forward service/hadoop-hadoop-yarn-ui 8088:8088` ，open localhost:8088  

## 组成  
### hdfs  
dataNode、nameNode  

### yarn  
nodeManager、resourceManager

### 配置  

chart的默认hadoop配置在hadoop-configmap.yaml文件。

## test

1.login hdfs data-node `minikube kubectl -- exec -it pod/hadoop-hadoop-hdfs-dn-0 /bin/bash`

2.prepare input

```shell
cd /home
mkdir input
echo Hello World Bye World > input/file01
echo Hello Hadoop Goodbye Hadoop > input/file02
/usr/local/hadoop/bin/hadoop fs -put input / # Put the data into the HDFS drive
/usr/local/hadoop/bin/hadoop fs -rm -r -f /output # may need to clear output dir if not first time run
```

3.run map-reduce example & cat res

```shell
/usr/local/hadoop/bin/hadoop jar /usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.9.0.jar wordcount /input /output  #run wordcount map-reduce example
/usr/local/hadoop/bin/hadoop fs -ls  /output
/usr/local/hadoop/bin/hadoop fs -cat /output/part-r-00000
```

the result is as followed:

```
Bye	1
Goodbye	1
Hadoop	2
Hello	2
World	2
```
hadoop-mapreduce-examples source code: https://github.com/apache/hadoop/tree/rel/release-2.9.0/hadoop-mapreduce-project/hadoop-mapreduce-examples
## 参考：
https://gist.github.com/TeemuKoivisto/5632fabee4915dc63055e8e544247f60

https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html  
## 遇到的问题：
### 1.hdfs-datanode pod重启失败  
quick fix：



过程：

1.先打pod log: `minikube kubectl -- logs pod/hadoop-hadoop-hdfs-dn-0` : java.io.IOException: Incompatible clusterIDs in /root/hdfs/datanode: namenode clusterID = CID-3994d043-8223-4335-9bdf-98a12c2b5233; datanode clusterID = CID-3f70a157-7824-4d7a-a40a-8adaccd9bdea

2.查资料，看了下dn 和nn的确实不一样: login ns , `cat /root/hdfs/namenode/current/VERSION`  ;login ds `cat /root/hdfs/datanode/current/VERSION`  

3.不知道为什么会出现clusterID不一致的问题。todo。  

# Repo Archive Notice⚠️ 

As of Nov 13, 2020, charts in this repo will no longer be updated.
For more information, see the Helm Charts [Deprecation and Archive Notice](https://github.com/helm/charts#%EF%B8%8F-deprecation-and-archive-notice), and [Update](https://helm.sh/blog/charts-repo-deprecation/).

# Hadoop Chart

[Hadoop](https://hadoop.apache.org/) is a framework for running large scale distributed applications.

This chart is primarily intended to be used for YARN and MapReduce job execution where HDFS is just used as a means to transport small artifacts within the framework and not for a distributed filesystem. Data should be read from cloud based datastores such as Google Cloud Storage, S3 or Swift.

## DEPRECATION NOTICE

This chart is deprecated and no longer supported.

## Chart Details

## Installing the Chart

To install the chart with the release name `hadoop` that utilizes 50% of the available node resources:

```
$ helm install --name hadoop $(stable/hadoop/tools/calc_resources.sh 50) stable/hadoop
```

> Note that you need at least 2GB of free memory per NodeManager pod, if your cluster isn't large enough, not all pods will be scheduled.

The optional [`calc_resources.sh`](./tools/calc_resources.sh) script is used as a convenience helper to set the `yarn.numNodes`, and `yarn.nodeManager.resources` appropriately to utilize all nodes in the Kubernetes cluster and a given percentage of their resources. For example, with a 3 node `n1-standard-4` GKE cluster and an argument of `50`, this would create 3 NodeManager pods claiming 2 cores and 7.5Gi of memory.

### Persistence

To install the chart with persistent volumes:

```
$ helm install --name hadoop $(stable/hadoop/tools/calc_resources.sh 50) \
  --set persistence.nameNode.enabled=true \
  --set persistence.nameNode.storageClass=standard \
  --set persistence.dataNode.enabled=true \
  --set persistence.dataNode.storageClass=standard \
  stable/hadoop
```

> Change the value of `storageClass` to match your volume driver. `standard` works for Google Container Engine clusters.

## Configuration

The following table lists the configurable parameters of the Hadoop chart and their default values.

| Parameter                                         | Description                                                                        | Default                                                          |
| ------------------------------------------------- | -------------------------------                                                    | ---------------------------------------------------------------- |
| `image.repository`                                | Hadoop image ([source](https://github.com/Comcast/kube-yarn/tree/master/image))    | `danisla/hadoop`                                                 |
| `image.tag`                                       | Hadoop image tag                                                                   | `2.9.0`                                                          |
| `imagee.pullPolicy`                               | Pull policy for the images                                                         | `IfNotPresent`                                                   |
| `hadoopVersion`                                   | Version of hadoop libraries being used                                             | `2.9.0`                                                          |
| `antiAffinity`                                    | Pod antiaffinity, `hard` or `soft`                                                 | `hard`                                                           |
| `hdfs.nameNode.pdbMinAvailable`                   | PDB for HDFS NameNode                                                              | `1`                                                              |
| `hdfs.nameNode.resources`                         | resources for the HDFS NameNode                                                    | `requests:memory=256Mi,cpu=10m,limits:memory=2048Mi,cpu=1000m`   |
| `hdfs.dataNode.replicas`                          | Number of HDFS DataNode replicas                                                   | `1`                                                              |
| `hdfs.dataNode.pdbMinAvailable`                   | PDB for HDFS DataNode                                                              | `1`                                                              |
| `hdfs.dataNode.resources`                         | resources for the HDFS DataNode                                                    | `requests:memory=256Mi,cpu=10m,limits:memory=2048Mi,cpu=1000m`   |
| `hdfs.webhdfs.enabled`                            | Enable WebHDFS REST API                                                            | `false`
| `yarn.resourceManager.pdbMinAvailable`            | PDB for the YARN ResourceManager                                                   | `1`                                                              |
| `yarn.resourceManager.resources`                  | resources for the YARN ResourceManager                                             | `requests:memory=256Mi,cpu=10m,limits:memory=2048Mi,cpu=1000m`   |
| `yarn.nodeManager.pdbMinAvailable`                | PDB for the YARN NodeManager                                                       | `1`                                                              |
| `yarn.nodeManager.replicas`                       | Number of YARN NodeManager replicas                                                | `2`                                                              |
| `yarn.nodeManager.parallelCreate`                 | Create all nodeManager statefulset pods in parallel (K8S 1.7+)                     | `false`                                                          |
| `yarn.nodeManager.resources`                      | Resource limits and requests for YARN NodeManager pods                             | `requests:memory=2048Mi,cpu=1000m,limits:memory=2048Mi,cpu=1000m`|
| `persistence.nameNode.enabled`                    | Enable/disable persistent volume                                                   | `false`                                                          | 
| `persistence.nameNode.storageClass`               | Name of the StorageClass to use per your volume provider                           | `-`                                                              |
| `persistence.nameNode.accessMode`                 | Access mode for the volume                                                         | `ReadWriteOnce`                                                  |
| `persistence.nameNode.size`                       | Size of the volume                                                                 | `50Gi`                                                           |
| `persistence.dataNode.enabled`                    | Enable/disable persistent volume                                                   | `false`                                                          | 
| `persistence.dataNode.storageClass`               | Name of the StorageClass to use per your volume provider                           | `-`                                                              |
| `persistence.dataNode.accessMode`                 | Access mode for the volume                                                         | `ReadWriteOnce`                                                  |
| `persistence.dataNode.size`                       | Size of the volume                                                                 | `200Gi`                                                          |

## Related charts

The [Zeppelin Notebook](https://github.com/kubernetes/charts/tree/master/stable/zeppelin) chart can use the hadoop config for the hadoop cluster and use the YARN executor:

```
helm install --set hadoop.useConfigMap=true stable/zeppelin
```

# References

- Original K8S Hadoop adaptation this chart was derived from: https://github.com/Comcast/kube-yarn
