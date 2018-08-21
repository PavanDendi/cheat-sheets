## S3

### Set AWS credentials for s3a file system using sc

    sc.hadoopConfiguration.set("fs.s3a.access.key", ACCESS_KEY)
    sc.hadoopConfiguration.set("fs.s3a.secret.key", SECRET_KEY)

### read file using traditional spark.read API, but specify file location using s3a://
    val s3df = spark.read.csv("s3a://bucket_name/path/to/file.csv")

## Teradata

### Download JDBC drivers from teradata, both terajdbc4.jar and tdgssconfig.jar are needed

http://downloads.teradata.com/download/connectivity/jdbc-driver

### Include driver jars when launching spark-shell

```
spark2-shell --master local --deploy-mode client --driver-memory 16g --jars ~/terajdbc4.jar,tdgssconfig.jar
```

### Read table using JDBC read API

```
val teradf = spark.read.format("jdbc")
    .option("url","jdbc:teradata://teradata_server_ip")
    .option("dbtable","database.table_name")
    .option("user","username")
    .option("password","password")
    .option("driver","com.teradata.jdbc.TeraDriver")
    .load()
```

## Hive/Impala

### Create external table from DataFrame

```
df.write.partitionBy("dataDt")
    .option("path","path/to/hdfs/location/")
    .saveAsTable("db_name.table_name")
```

### Update Impala metadata to see new table
```
INVALIDATE METADATA [[db_name.]table_name]
```
Calling INVALIDATE METADATA without a table_name will flush metadata for entire catalog.

Can also try REFRESH to see new data for existing table