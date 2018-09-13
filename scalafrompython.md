## Scala for Python Peeps


#### [A super awesome crash course book](http://go.databricks.com/hubfs/Landing_pages/DE%20Guide%20to%20Spark/The-Data-Engineers-Guide-to-Apache-Spark.pdf?utm_campaign=Data%20Engineer%27s%20Guide%20to%20Apache%20Spark&utm_content=61159957&utm_medium=social&utm_source=twitter)

***
### Syntax

- [using a dynamic `groupBy`](https://stackoverflow.com/questions/36307867/scala-spark-dynamically-call-groupby-and-agg-with-parameter-values)
- [`reduce`](https://bradcollins.com/2015/05/23/scala-saturday-the-reduce-method/)

- [pyspark cheatsheet](https://www.qubole.com/resources/pyspark-cheatsheet/)

- only double quotes allowed
- [loading csv files](https://github.com/databricks/spark-csv)
- [partitioning a table](https://stackoverflow.com/questions/38872592/how-to-filter-data-using-window-functions-in-spark)
- [declare parquet schema](https://stackoverflow.com/questions/41740499/parquet-schema-and-spark)

*similarity* vs *disimilarity* string filtering
``` scala
//similarity syntax
display(dbscan.select($"city").filter(lower($"city").like("%salt lake%")).distinct)

// disimilarity syntax
display(dbscan.select($"city").filter(!lower($"city").like("%salt lake%")).distinct)
```

- [getting distinct values](https://stackoverflow.com/questions/38946337/fetching-distinct-values-on-a-column-using-spark-dataframe)

- getting distinct values as a list of strings:
```
      df.select($"col_name".cast(String))
               .collect().map(_(0)).toList
```


- `orderBy` only sorts *ascending*

- get current time `java.LocalDate.now`

- [`CROSS JOIN` syntax](http://www.gatorsmile.io/spark-2-1-new-cross-join-syntax/)
- filtering values [between][link] `a` and `b`  
    - `filter($"someCol".between(a,b))`
    - inclusive bound
          between(java.lang.Object lowerBound,
             java.lang.Object upperBound)

- [typecasting][link2]
  - [data types](https://docs.tibco.com/pub/sfire-analyst/7.7.1/doc/html/en-US/TIB_sfire-analyst_UsersGuide/connectors/apache-spark/apache_spark_data_types.htm)

- [creating a substring](https://stackoverflow.com/a/42823661/7143036)

- [case when](https://stackoverflow.com/a/30784887/7143036) syntax:

      // Use when/otherwise syntax
      val df1 = df.withColumn("Green_Ind", when($"color" === "Green", 1).otherwise(0))

- how do I get values in `dataFrame_1` not in `dataFrame_2`?
  - use a [`left anti join`](https://stackoverflow.com/a/45990634/7143036s)

- formattting a date:
  - use `SimpleDateFormat`
  - also can use   
      `.withColumn("dow",from_unixtime(
      unix_timestamp($"utc_datetime", "MM/dd/yyyy"), "EEEEE"))`
- `repartition` a column `colorDf = peopleDf.repartition($"color")`
- using `crosstab`
`display(walkinsTemporal.stat.crosstab("breakout_rows","breakout_columns"))`

- how do I deal with a nested struct or array?
  - [`explode`](https://hadoopist.wordpress.com/2016/05/16/how-to-handle-nested-dataarray-of-structures-or-multiple-explodes-in-sparkscala-and-pyspark/)

- [How do I create an empty df?](https://stackoverflow.com/questions/31477598/how-to-create-an-empty-dataframe-with-a-specified-schema)

- [how can I write a SQL `CASE WHEN` in scala?](https://stackoverflow.com/a/43587956/7143036)

[link]: https://spark.apache.org/docs/1.5.1/api/java/org/apache/spark/sql/Column.html#between(java.lang.Object,%20java.lang.Object)

[link2]: https://spark.apache.org/docs/2.1.0/api/java/org/apache/spark/sql/Column.html#cast(org.apache.spark.sql.types.DataType)



***
#### Issues I have

- scala doesn't use single quotes for strings, *unlike* python  
- [When do I use `==` and `===`?](https://stackoverflow.com/questions/39490236/difference-between-and-in-scala-spark)

- [What's the right syntax for `countDistinct`?](https://stackoverflow.com/questions/37949494/how-to-count-occurrences-of-each-distinct-value-in-a-column)  
- [Aliasing a `count()`](https://stackoverflow.com/questions/32119936/dataframe-how-to-groupby-count-then-filter-on-count-in-scala)
- returned empty dataframe?
  - did you check the data? is the lat long the right direction (+/-)?
- tidbits on using [`map` vs *looping*](https://stackoverflow.com/questions/1975250/when-should-i-use-a-map-instead-of-a-for-loop)

- `manually specify schema` issue with parquet
  - check location for `SUCCESS` file, if not there, there was an issue with the write
***
#### Spark tuning

regarding parallelism, coalescing files
> I learned it by experience though
it is like you need to observe the file size and number of files after first run result
generally you can keep number of partitions 200..but need to tune based on required parallelism
example if  there are 200 files of just 1kb size then possibly merge those into single file, but some times it reduces performance of last stage so you need to tune it again to 10 or 20 to balance parallelism
