## S3

### Set AWS credentials for s3a file system using sc

    sc.hadoopConfiguration.set("fs.s3a.access.key", ACCESS_KEY)
    sc.hadoopConfiguration.set("fs.s3a.secret.key", SECRET_KEY)

### read file using traditional spark.read API, but specify file location using s3a://
    val s3df = spark.read.csv("s3a://bucket_name/path/to/file.csv")

## Execute SQL query for JDBC reads

SQL queries can be given instead of a table name for the `dbtable` option.  Spark is basically doing a `SELECT * FROM $dbtable`, so queries must be in parentheses and a table name given.

```
val sqldf = spark.read.format("jdbc")
    .option("dbtable", "(SELECT * FROM db.table) foo")
```
Reading a complex query from a file:
```
val query = scala.io.Source.fromFile("complex_query.sql").getLines.mkString
val formatted_query = "(" + query + ") foo"

val sqldf = spark.read.format("jdbc")
    .option("dbtable", formatted_query)
```
### SQL query formatting for MySQL
Database/field names might need to be surrounded by backticks to prevent overlap with reserved keywords.  
For example, if the JDBC driver complains about:
```
(select * from db.values limit 10) as foo
```
Try changing the query to:
```
(select * from `db`.`values` limit 10) as `foo`
```

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
## Microsoft SQL Server

### Add JDBC driver as dependency or include jar file

#### As dependency
As argument for spark-shell:
```
-- packages com.microsoft.sqlserver:mssql-jdbc:7.0.0.jre8
```
As dependency for build.sbt:
```
// libraryDependencies += groupID % artifactID % revision % configuration
libraryDependencies += "com.microsoft.sqlserver" % "mssql-jdbc" % "7.0.0.jre8"
```

### Read
```
val sqldf = spark.read
    .format("jdbc")
    .option("url","jdbc:sqlserver://server_name")
    .option("database", "database_name")
    .option("dbtable","db.table")
    .option("user","username")
    .option("password","password")
    .option("driver","com.microsoft.sqlserver.jdbc.SQLServerDriver")
    .load()
```

### Write
```
df.write.
    .format("jdbc")
    .option("url","jdbc:sqlserver://server_name")
    .option("database", "database_name")
    .option("dbtable","db.table")
    .option("user","username")
    .option("password","password")
    .option("driver","com.microsoft.sqlserver.jdbc.SQLServerDriver")
    .option("truncate", "true")
    .mode("overwrite")
    .save()
```

## MySQL

Two main versions of JDBC driver exist, 5 and 8:
```
mysql:mysql-connector-java:5.1.47
mysql:mysql-connector-java:8.0.12
```
Read/write is similar to Teradata and MS SQL.  The "database" option is not needed.

Spark Driver for version 5:
```
.option("driver","com.mysql.jdbc.Driver")
```
Above has been deprecated but still works for version 8.  Recommended to use:
```
.option("driver","com.mysql.cj.jdbc.Driver")
```

## Hive/Impala

### Create external table from DataFrame

```
df.write.partitionBy("dataDt")
    .option("path","path/to/hdfs/location/")
    .mode("append")
    // .mode("overwrite")
    // .mode(SaveMode.Append)
    // .mode(SaveMode.OverWrite)
    .saveAsTable("db_name.table_name")
```

### Update Impala metadata to see new table
```
INVALIDATE METADATA [[db_name.]table_name]
```
Calling INVALIDATE METADATA without a table_name will flush metadata for entire catalog.

Can also try REFRESH to see new data for existing table
