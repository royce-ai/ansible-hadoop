# ansible-hadoop
ansible部署HadoopHA3.x集群



```shell
##ansible检测yaml语法问题
ansible-playbook --syntax-check  -i /path/hosts/hadoop_hosts  /path/ansible_hadoop.yaml
##执行ansible-playbook
ansible-playbook -i /path/hosts/hadoop_hosts  /path/ansible_hadoop.yaml
#开始初次启动Hadoop
##格式化zkfc
hdfs zkfc -formatZK
##启动三台台journalnode
systemctl start hadoop-hdfs-journalnode.service
###查看journalnode的启动状态
systemctl status hadoop-hdfs-journalnode.service
##在其中一台namenode上，格式化namenode
hdfs namenode -format
##启动第一台namenode1（active）
systemctl start hadoop-hdfs-namenode
systemctl status hadoop-hdfs-namenode.service
##在namenode2上同步namenode1的数据
hdfs namenode -bootstrapStandby
##启动namenode2
##启动三台datanode
systemctl start hadoop-hdfs-datanode.service 
systemctl status hadoop-hdfs-datanode.service
##启动resourcemanager，nodemanager
systemctl start hadoop-yarn-resourcemanager.service 
systemctl status hadoop-yarn-resourcemanager.service 
systemctl start hadoop-yarn-nodemanager.service 
systemctl status hadoop-yarn-nodemanager.service
##在两台namenode上启动ZKFC
systemctl start hadoop-hdfs-zkfc.service 
systemctl status hadoop-hdfs-zkfc.service
##启动mr_history
systemctl start hadoop-hdfs-mapredhistory.service
systemctl status hadoop-hdfs-mapredhistory.service
```



`##启动namenode遇到的问题`

`2023-06-11 08:45:49,459 INFO org.apache.hadoop.util.JvmPauseMonitor: Starting JVM pause monitor`
`2023-06-11 08:45:49,477 INFO org.apache.hadoop.hdfs.DFSUtil: Filter initializers set : org.apache.hadoop.http.lib.StaticUserWebFilter,org.apache.hadoop.hdfs.web.AuthFilterInitializer`
`2023-06-11 08:45:49,481 INFO org.apache.hadoop.hdfs.DFSUtil: Starting Web-server for hdfs at: http://warehouse_emr000101:9870`
`2023-06-11 08:45:49,491 INFO org.eclipse.jetty.util.log: Logging initialized @857ms to org.eclipse.jetty.util.log.Slf4jLog`
`2023-06-11 08:45:49,583 INFO org.apache.hadoop.security.authentication.server.AuthenticationFilter: Unable to initialize FileSignerSecretProvider, falling back to use random secrets.`
`2023-06-11 08:45:49,592 INFO org.apache.hadoop.http.HttpRequestLog: Http request log for http.requests.namenode is not defined`
`2023-06-11 08:45:49,598 INFO org.apache.hadoop.http.HttpServer2: Added global filter 'safety' (class=org.apache.hadoop.http.HttpServer2$QuotingInputFilter)`
`2023-06-11 08:45:49,600 ERROR org.apache.hadoop.hdfs.server.namenode.NameNode: Failed to start namenode.`
`java.lang.IllegalArgumentException: The value of property bind.address must not be null`

解决办法：

在/etc/hosts文件中，eg：hostname名称中不能有下划线，如错误日志中的`http://warehouse_emr000101:9870`，将服务器的hostname改成不带下划线的名字即可

```reStructuredText
::1     localhost       localhost.localdomain   localhost6      localhost6.localdomain6
127.0.0.1       localhost       localhost.localdomain   localhost4      localhost4.localdomain4

ipv4  warehouse-emr000101  warehouse-emr000101
ipv4  warehouse-emr000102  warehouse-emr000102
ipv4  warehouse-emr000103  warehouse-emr000103
ipv4  warehouse-emr000104  warehouse-emr000104
ipv4  warehouse-emr000105  warehouse-emr000105
ipv4  warehouse-emr000106  warehouse-emr000106
```

