# helm-chart
# hadoop

## UI入口

| desc                               | url                                                          |
| ---------------------------------- | ------------------------------------------------------------ |
| hadoop yarn resource manager webui | http://hadoop-hadoop-yarn-rm-0.hadoop-hadoop-yarn-rm.default:8088 |
|                                    |                                                              |
|                                    |                                                              |

# spark

## UI入口

| desc                                                         | url                                               |
| ------------------------------------------------------------ | ------------------------------------------------- |
| spark master/driver node webui                               | http://spark-lb-svc.default:8080                  |
| spark work/salve node1 webui                                 | http://spark-worker-0.spark-hls-svc.default:8081  |
| spark work/salve node2 webui                                 | http://spark-worker-1.spark-hls-svc.default:8081/ |
| sparkcontext‘s ui。every sparkcontext launches a web UI, by default on port 4040 https://spark.apache.org/docs/2.2.3/monitoring.html | http://<driver-node>:4040                         |

