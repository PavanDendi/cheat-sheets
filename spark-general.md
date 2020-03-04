## access local files
Spark master must be local.  If it is YARN, file will not be found unless file exists across all nodes.
```
val df = spark.read.parquet("file:///absolute_path/to/file.parquet")
```

## get files from http url
```
val dataUrl = "https://...."
sc.addFile(dataUrl)
val data = spark.read.csv(SparkFiles.get(dataUrl.split("/").last))
```
for databricks, prepend "file://" so it reads from local file system
```
val fileName = s"""file://${SparkFiles.get("parkinsons.data")}"""
val data = spark.read.csv(fileName)
```
not using SparkFiles and reading directly into memory
```
val dataUrl = "https://..../parkinsons.data"
val dataTxt = scala.io.Source.fromURL(dataUrl).mkString
val dataDS = dataTxt.lines.toList.toDS
val dataDF = spark.read.csv(dataDS)
```

## reading csv using custom schema
```
import org.apache.spark.sql.types._

val custom_schema = StructType(Array(
    StructField("timestamp_column", TimestampType, true),
    StructField("integer_column", IntegerType, true),
    StructField("string_column", StringType, true)
))

val df = spark.read.option("header", "false")
                    .option("delimiter","|")
                    .schema(custom_schema)
                    .csv("/path/to/file.csv")
```

## read text file

### read single line
```
val pw = spark.read.textFile("file:///absolute_path/to/password.txt").take(1)(0)
val pw = sc.textFile("file:///absolute_path/to/password.txt").take(1)(0)
```

### read entire file as single string
```
val query = scala.io.Source.fromFile("complex_query.sql").getLines.mkString
val query = spark.read.textFile("complex_query.sql").collect.mkString
val query = sc.textFile("complex_query.sql").collect.mkString
```

## execute shell commands
required import:
```
import sys.process._
```
return exit code from executed string
```
val exit_code = "ls -al".!
```
return output from executed string
```
val cmd_output = "ls -al".!!
```
To run as a shell command.  Useful if scala cannot parse complex arguments such as SQL queries.
```
Seq("/bin/sh", "-c", shellCmd)
```
`!` and `!!` are blocking calls.  If you want to run a command and send it to background, use `run`.\
This will return a `Process` class that has `destroy` and `exitValue` methods.\
`exitValue` is also a blocking call if the command has not exited.

## Maps

For Map of [String,org.apache.spark.sql.DataFrame], to retrieve a single df:
```
testmap("df_key")
testmap.get("df_key").get
```

To check if key exists in Map:
```
testmap.contains("df_key")
testmap.keySet.exists(_ == "df_key")
testmap.keysIterator.contains("df_key")
```

## uJson library provides python like JSON functionality
```
com.lihaoyi %% ujson % 0.6.6
```
Parse a json string
```
val json_string = sc.textFile("sample.json").collect.mkString
val parsed: ujson.Js = ujson.read(json_string)
```
Access keys/values similar to Python
```
parsed("input")(0)("Name")
```
Iterate through keys
```
for (k <- parsed("input").arr) {
    println(k)
}
```
Iterate through key/value pairs
```
for ((k,v) <- parsed.obj) {
    println(k)
    println(v)
}
```

## Column order for unions
As of spark 2.2, a union between two DataFrames must have the same order of columns, even if the schema is otherwise identical.

It seems that the union does not match up column names which leads to values being mapped to wrong columns or null values.

http://lobotomys.blogspot.com/2017/07/spark-union-column-order-issue.html\
https://issues.apache.org/jira/browse/SPARK-21109\
https://issues.apache.org/jira/browse/SPARK-21043\
https://stackoverflow.com/questions/38084117/spark-dataframes-how-can-i-change-the-order-of-columns-in-java-scala

## Casting columns to different data types
```
import org.apache.spark.sql.types._

df = df.withColumn("col_name", $"col_name".cast(StringType))
```
## Regex replacement of all white space
str.replaceAll() accepts regex expressions
```
str.replaceAll("\\s+", " ")
```
## Working with Dates and Time
```
import java.time.LocalDate
import java.time.LocalDateTime
import java.time.format.DateTimeFormatter
```
java.time library uses ISO format by default
```
>>> val date = LocalDate.parse("2018-03-01")
>>> 2018-03-01

>>> val datedt = LocalDateTime.parse("2018-03-01T00:00:00")
>>> 2018-03-01T00:00:00

>>> datedt.plusDays(1).toString().replace("T"," ")
>>> 2018-03-02 00:00:01
```
Specifying custom formats
```
scala> val date = LocalDate.parse("01/01/2020", DateTimeFormatter.ofPattern("MM/dd/yyyy"))
date: java.time.LocalDate = 2020-01-01

scala> date.format(DateTimeFormatter.ofPattern("yyyy.MM.dd"))
res0: String = 2020.01.01
```
