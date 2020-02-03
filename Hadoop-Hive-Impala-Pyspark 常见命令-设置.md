# Hadoop/Hive/Impala/Pyspark 常见命令/设置
## Hadoop CMD
```bash
# 不同集群间迁移
hadoop distcp -update / -overwrite <clusterA> <clusterB>

# mkdir 创建文件夹
hadoop fs -mkdir [-p] <hdfs://>
# touchz 创建文件
hadoop fs -touchz <hdfs://>

# ls 列出文件夹和文件
hadoop fs -ls [-R] <hdfs://>
# stat 状态信息
hadoop fs -stat
# du 查看逻辑空间占用 | -s 汇总 | -h 最合适单位
hadoop fs -du [-s] [-h] <hdfs://>
# fsck | HDFS物理空间=逻辑空间*block备份数
hadoop fsck <hdfs://>
# count | -q 完整空间使用情况 | -h 最合适单位
hadoop fs -count [-q] [-h] <hdfs://>
# report
hdfs dfsadmin -report <hdfs://>

# cp | -f 覆盖 | -p 保留属性
hadoop -cp [-f] [-p] <pathA> <pathB>
# mv
hadoop fs -mv <pathA> <pathB>
# rm | -r 递归 | -f 不报错
hadoop fs -rm -r -f <hdfs://>
# cat
hadoop fs -cat <hdfs://>
# head 100 行
hadoop fs -text <hdfs://> | head -n 100
# tail 最后 1k 数据
hadoop fs -tail <hdfs://>
# grep 筛选
hadoop fs -cat <hdfs://> | grep Bob
# wc -l 文件行数
hadoop fs -cat <hdfs://> | wc -l

# put / upload
hadoop fs -put <localhost> <hdfs://>
# get / download | -ignorecrc 复制CRC校验失败文件 | -crc 复制文件&crc信息
hadoop fs -get [-ignorecrc][-crc] <hdfs://> <localhost>
```
## MapReduce SET
```
set mapreduce.map.speculative=true
set mapreduce.reduce.speculative=true
```

## HIVE CMD
```
hive -f hdfs://CMD.sql;
hive -e 'CMD';
hive --hiveconf xxx.xxx=xxx;
hive -i hive.conf;
hive --define key=value;

# Priority: hive-site.xml > hivemetastore-site.xml > hiveserver2-site.xml > --hiveconf > set
```


## HIVE SET
```sql
set COMPRESSION_CODEC = snappy;
set hive.exec.dynamic.partition = true;
set hive.exec.max.created.files = 200000;
set hive.exec.max.dynamic.partitions = 200000;
set hive.exec.max.dynamic.partitions.pernode = 200000;
set hive.exec.reducers.max = 8000;
set hive.groupby.mapaggr.checkintervsl = 100;
set hive.groupby.skewindata = true;
set hive.merge.mapfiles = true;
set hive.merge.mapredfiles = true;
set hive.merge.size.per.task = 256000000;
set hive.merge.smallfiles.avgsize = 160000000;
set hive.optimize.skewjoin = true;
set hive.optimize.sort.dynamic.partition = true;  
set hive.skewjoin.key = 1000;
set mapred.job.priority = VERY_HIGH;
set mapred.reduce.child.java.opts = -Xmx8192m; 
set mapred.reduce.tasks = 3600;
set mapreduce.job.queuename = root.high;
set mapreduce.map.java.opts = -Xmx8192m;
set mapreduce.map.memory.mb = 8192;
set mapreduce.reduce.memory.mb = 8192;
set mapreduce.reduce.shuffle.memory.limit.percent=0.4;
set mapreduce.reduce.shuffle.parallelcopies=5;
set mapreduce.reduce.speculative = true;
set mapreduce.task.timeout = 1800000;
set parquet.block.size = 402653184;
set parquet.compression = snappy;
set yarn.app.mapreduce.am.resource.mb=4096; 
set yarn.app.mapreduce.am.command-opts=-Xmx4096m;

MSCK REPAIR TABLE xxx.xxx;

distribute by rand() -- 防止数据倾斜
```

## Impala CMD
```sql
insert overwrite table xxx.xxx partition(ds=%YYYYMMDDHH%, product_id)
select * from xxx.xxx where xx = xx;

insert into table xxx.xxx partition (ds=, product_id) 
select ds, product_id from xxx.xxx 
union all
select ds, product_id from xxx.xxx;

alter table xxx.xxx add if not exists partition (ds=%YYYYMMDDHH%);

refresh xxx.xxx partition(ds=%YYYYMMDDHH%, product_id ='%product_id%');

show partitions xxx.xxx;

ALTER TABLE xxx.xxx RECOVER PARTITIONS;
```

## Pyspark SET
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import sys
from pyspark.context import SparkContext
from pyspark.sql import HiveContext, SparkSession, Row

dstm = sys.argv[1]

spark = SparkSession \
    .builder \
    .appName("Task_%s"%dstm) \
    .config("spark.driver.maxResultSize","8g") \
    .config("spark.executor.memory","8g") \
    .config("spark.driver.memory","8g") \
    .config('spark.sql.shuffle.partitions','1999') \
    .config('spark.default.parallelism','400') \
    .config('spark.executor.cores','6') \
    .config('spark.executor.memory','4096m') \ 
    .getOrCreate()

sc = spark.sparkContext

sqlContext = HiveContext(sc)
sqlContext.setConf('spark.sql.parquet.compression.codec','gzip')
```



