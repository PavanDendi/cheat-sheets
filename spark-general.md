## access local files
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
                    .schema(holdout_schema)
                    .csv("/path/to/file.csv")
```

## read text file
```
val pw = spark.read.textFile("file:///absolute_path/to/password.txt").take(1)(0)
val pw = sc.textFile("file:///absolute_path/to/password.txt").take(1)(0)
```