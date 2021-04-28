::: {.cell .markdown}
# Goals

## Using pyspark

-   filter, map and reduce
-   Working with CSV and JSON files
-   SQL and pyspark
-   Wrtiting and reading ORC and Parquet files
:::

::: {.cell .markdown}
#### Exercice 5.1 \[★\] {#exercice-51-}

First, we will install pyspark

`$ pip install pyspark`
:::

::: {.cell .markdown}
If the installation was successful, the following code will be
successfully executed.
:::

::: {.cell .code}
``` {.python}
from pyspark import SparkConf
from pyspark.context import SparkContext
```
:::

::: {.cell .markdown}
We will use default SparkConfiguration for creating a spark context.
This spark context will be used throughout our examples.

Firstly, we read a CSV file that contains two columns: languageLabel and
year.
:::

::: {.cell .code}
``` {.python}
sc = SparkContext.getOrCreate(SparkConf())
lines = sc.textFile("../../data/pl.csv")
```
:::

::: {.cell .markdown}
We will use the methods map() and reduce() with the lambda expressions.

Note that in the following exercise, we are not using the builtin
functions of Python. The goal of this examples is to count the number of
characters line by line.
:::

::: {.cell .code}
``` {.python}
line_length = lines.map(lambda line: len(line))
total_length = line_length.reduce(lambda a,b: a + b)
print(total_length)
```
:::

::: {.cell .markdown}
In this example, we get the number of tokens separated by a comma for
each line. Finally, we calculate the total number of tokens.
:::

::: {.cell .code}
``` {.python}
line_token_count = lines.map(lambda line: len(line.split(",")))
total_token_count = line_token_count.reduce(lambda a,b: a + b)
print(total_token_count)
```
:::

::: {.cell .markdown}
We slightly modify the code and use space as a separator for counting
the number of tokens.
:::

::: {.cell .code}
``` {.python}
line_token_count = lines.map(lambda line: len(line.split(" ")))
total_token_count = line_token_count.reduce(lambda a,b: a + b)
print(total_token_count)
```
:::

::: {.cell .markdown}
So far, we only considered one single file at a file.

What if we want to manipulate and analyse multiple CSV files.

The method textFile() can also handle such cases.
:::

::: {.cell .code}
``` {.python}
sc = SparkContext.getOrCreate(SparkConf())
lines = sc.textFile("../../data/*.csv")

line_length = lines.map(lambda line: len(line))
total_length = line_length.reduce(lambda a,b: a + b)
print(total_length)
```
:::

::: {.cell .markdown}
Our next goal is to collect all the lines stored in multiple files and
access them using a single variable.

This is possible using the collect() method.
:::

::: {.cell .code scrolled="false"}
``` {.python}
sc = SparkContext.getOrCreate(SparkConf())
lines = sc.textFile("../../data/*.csv")

all_lines = lines.collect()
print(len(all_lines))
for line in all_lines:
    print(line)
```
:::

::: {.cell .markdown}
Like Python builtin function map(), it is also possible to pass a user
defined function as an input to the map() function from pyspark.

In the following example, we count the number of occurences in all the
CSV files.

For this purpose, we have written a function count_Language() that
counts the number of occurrences of \"Language\" in a line.
:::

::: {.cell .code}
``` {.python}
import re

sc = SparkContext.getOrCreate(SparkConf())
lines = sc.textFile("../../data/*.csv")

def count_Language(line):
    return len(re.findall("Language", line))

line_length = lines.map(count_Language)
total_length = line_length.reduce(lambda a,b: a + b)
print(total_length)
```
:::

::: {.cell .markdown}
**Question** Download 50 HTML pages. Write a program using Spark to
count the total number of times `<div>` or `<div/>` is present in all
these downloaded files.
:::

::: {.cell .markdown}
#### Exercice 5.2 \[★★\] {#exercice-52-}

Our next goal is to work with JSON files and create pandas dataframe.

However, we would like to use the dataframe support provided by Spark.

We first create a Spark session.
:::

::: {.cell .code}
``` {.python}
from pyspark.sql import SparkSession

spark = SparkSession.builder.getOrCreate()
```
:::

::: {.cell .markdown}
We read a JSON file and convert it to pandas dataframe.

This pandas dataframe is then converted to Spark dataframe.
:::

::: {.cell .code}
``` {.python}
from pandas import json_normalize
import pandas as pd
import json

data = json.load(open('../../data/pl.json'))
dataframe = json_normalize(data)
dataframe = dataframe.astype(dtype= {
      "languageLabel" : "<U200", "year" : "int64"})

df = spark.createDataFrame(dataframe)
```
:::

::: {.cell .markdown}
We now display the Spark dataframe.
:::

::: {.cell .code}
``` {.python}
df.show()
```
:::

::: {.cell .markdown}
It is also possible to specify the number of rows for display.
:::

::: {.cell .code}
``` {.python}
df.show(30)
```
:::

::: {.cell .markdown}
The following schema will display the schema of the dataframe.
:::

::: {.cell .code}
``` {.python}
df.printSchema()
```
:::

::: {.cell .markdown}
In the following code, we write a filter function to obtain the
programming languages that were release in 1952.
:::

::: {.cell .code}
``` {.python}
def pandas_filter_func(iterator):
    for pandas_df in iterator:
        yield pandas_df[pandas_df.year == 1952]

df.mapInPandas(pandas_filter_func, schema=df.schema).show()
```
:::

::: {.cell .markdown}
In the following code, we apply a user defined function on the column
year to obtain the century when a programming language was first
released.
:::

::: {.cell .code}
``` {.python}
from pyspark.sql.functions import pandas_udf

@pandas_udf('long')
def century(series: pd.Series) -> pd.Series:
    return (series / 100) + 1

df.select(century(df.year)).show()
```
:::

::: {.cell .markdown}
Like pandas, it is possible to group data by specific column values.

In the following example, we want to get the count of programming
languages first released every year.
:::

::: {.cell .code}
``` {.python}
df.groupby('year').count().show()
```
:::

::: {.cell .markdown}
We have so far used CSV and JSON files.

But for optimized performance, ORC and parquet files are suggested.

In the following examples, we see how we can write a ORC and parquet
files using a Spark dataframe.
:::

::: {.cell .code}
``` {.python}
df.write.orc('languages.orc')
spark.read.orc('languages.orc').show()
```
:::

::: {.cell .code}
``` {.python}
df.write.parquet('languages.parquet')
spark.read.parquet('languages.parquet').show()
```
:::

::: {.cell .code}
``` {.python}
df.write.csv('languages.csv')
spark.read.csv('languages.csv').show()
```
:::

::: {.cell .markdown}
Before continuing, check your current directory and see how these
dataframes have been written.
:::

::: {.cell .markdown}
**Question** Write a query on Wikidata to download the names of all
software along with their first release date. Write a program using
Spark to obtain the number of software released every year.
:::

::: {.cell .markdown}
#### Exercice 5.3 \[★★\] {#exercice-53-}

It is possible to work using SQL language on spark dataframes.

For this purpose, we will create temporary views and run SQL queries.

The following example will return and display all the languages.
:::

::: {.cell .code}
``` {.python}
df.createOrReplaceTempView("languages")
spark.sql("SELECT * from languages").show()
```
:::

::: {.cell .markdown}
The following example uses SQL query to obtain the number of programming
languages.
:::

::: {.cell .code}
``` {.python}
spark.sql("SELECT count(*) from languages").show()
```
:::

::: {.cell .markdown}
The following query will return the count of languages first released in
1952.
:::

::: {.cell .code}
``` {.python}
spark.sql("SELECT count(*) from languages where year=1952").show()
```
:::

::: {.cell .markdown}
The following query is similar to the groupby example seen above.
:::

::: {.cell .code}
``` {.python}
spark.sql("SELECT year, count(*) from languages Group by year ORDER by year").show(100)
```
:::

::: {.cell .markdown}
**Question** Instead of using Spark aggregation functions, use SQL and
Spark to obtain the number of software released every year using the
data downloaded in the previous exercise.
:::

::: {.cell .markdown}
For more examples, check [official
document](https://spark.apache.org/docs/latest/api/python/getting_started/quickstart.html)
:::
