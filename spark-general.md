## access local files
Spark master must be local.  If it is YARN, file will not be found unless file exists across all nodes.
```
val df = spark.read.parquet("file:///absolute_path/to/file.parquet")
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