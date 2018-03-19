# springcloud-elk

Elasticsearch+Logstash+Kibana as SpringCloud's log system.  
Use SpringAdmin to controller log's level and provide local log to user.

# 使用的版本：

logstash 6.2.2  
elasticsearch-6.2.2  
kibana-6.2.2  
rabbitmq 3.7.2  

# 遇到问题：

zipkin数据持久化至elasticsearch，但其带来了一个负面作用，无法正常显示dependencies，但不影响链路内容显示。

# 解决办法：

1、下载zipkin-dependencies的jar包。  
2、执行zipkin以后本机执行jar包：  
$ STORAGE_TYPE=elasticsearch ES_HOSTS=localhost:9200 java -jar zipkin-dependencies-1.9.3.jar。  
3、这样可以正常显示了，但是问题是刷新的时候需要每次手动执行一下该JAR包，否则就不会更新链路图。  

# logstash启动

logstash启动的时候需要调用自己的启动配置文件:logstash.bat -f logstash.conf。  
logstash.conf是本地配置的  
logstash1.conf是远程中心化配置的  


# logstash.conf:

input {  
  file {  
    type => "server"  
    path => ["D:\logelk\service1-8765.log.json","D:\logelk\service-zuul-8769.log.json"]  
  }  
}  
filter {  
  #Only matched data are send to output.  
}  
output {  
  elasticsearch {  
    action => "index"          #The operation on ES  
    hosts  => "127.0.0.1:9200"   #ElasticSearch host, can be array.  
    index  => "applog"         #The index to write data to.  
  }  
}

# logstash1.conf:

input {  
   tcp {  
    mode => "server"  
    host => "127.0.0.1"  
    port => 9250  
  }  
}  
filter {  
  #Only matched data are send to output.  
}  
output {  
   elasticsearch {  
    action => "index"          #The operation on ES  
    hosts  => "127.0.0.1:9200"   #ElasticSearch host, can be array.  
    index  => "applog"         #The index to write data to.  
  }  
}
