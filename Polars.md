![Polars !](/polarsimage.png "Polars")

# POLARS

###### Autor : Abdou Samath YATTE - Master 1 Artificial intelligency DIT Jan 2023


## Contents

1. Introduction
2. Concept presentation
3. Practical example
4. Potential alternatives
5. Concrete use cases related to data analysis
6. Conclusion
7. Consulted resources

## Introduction

Polar is a Python module that contains simple to use data science functions. It is built on top of SciPy, scikit-learn, seaborn and pandas.
The Polars library is a data processing library, which allows to manipulate tabular data in an efficient and fast way. 
This library provides similar functionality to Pandas, but with much higher performance, using Rust as the underlying language.

## Concept presentation

The Polars library is particularly useful for processing large data, as it uses task parallelization to speed up data transformation operations.
In the picture below we have a comparison regarding the performance with the other library. So we can notice that the time of the execution of the polar is smaller than the others.

![Polars !](/polarperformance.png "PolarsPerformance")

Polars also offers data manipulation functions such as filtering, selecting, transforming, merging and reorganizing data.

As far as Polars is concerned, this library provides tools for basic statistics calculations, string manipulation, missing value handling, and temporal data management.

In addition, Polars supports data visualization using the Vega-Lite library. This feature allows you to create interactive charts with advanced customization options.

#### Differences in concepts between Polars and Pandas

Pandas gives a label to each row with an index. Polars does not use an index and each row is indexed by its integer position in the table.

Polars aims to have predictable results and readable queries, as such we think an index does not help us reach that objective. We believe the semantics of a query should not change by the state of an index or a reset_index call.

In Polars a DataFrame will always be a 2D table with heterogeneous data-types. The data-types may have nesting, but the table itself will not. Operations like resampling will be done by specialized functions or methods that act like 'verbs' on a table explicitly stating columns that 'verb' operates on. As such, it is our conviction that not having indices make things simpler, more explicit, more readable and less error-prone.

Note that an 'index' data structure as known in databases will be used by polars as an optimization technique.

**Polars uses Apache Arrow arrays to represent data in memory while Pandas uses Numpy arrays**

Polars represents data in memory with Arrow arrays while Pandas represents data in memory in Numpy arrays. Apache Arrow is an emerging standard for in-memory columnar analytics that can accelerate data load times, reduce memory usage and accelerate calculations.

Polars can convert data to Numpy format with the to_numpy method.

**Key syntax differences**

Users coming from Pandas generally need to know one thing...

    polars != pandas

**Selecting data**

As there is no index in Polars there is no .loc or iloc method in Polars - and there is also no SettingWithCopyWarning in Polars.

However, the best way to select data in Polars is to use the expression API. For example, if you want to select a column in Pandas you can do one of the following:

    df['a']
    df.loc[:,'a']

but in Polars you would use the .select method:

    df.select(['a'])

If you want to select rows based on the values then in Polars you use the .filter method:

    df.filter(pl.col('a') < 10)

As noted in the section on expressions below, Polars can run operations in .select and filter in parallel and Polars can carry out query optimization on the full set of data selection criteria.

**Files**

Polars supports different file types, and its respective parsers are amongst the fastest out there.

For instance, it is faster to load a CSV file via Polars before handing it to Pandas than loading them using Pandas. 

In the exemples below we will see how to read and writre a CSV file : 

Reading : 

    df = pl.read_csv("path.csv")

CSV files come in many different flavors, so make sure to check the read_csv() API.

Writting :

    df = pl.DataFrame({"foo": [1, 2, 3], "bar": [None, "bak", "baz"]})
    df.write_csv("path.csv")

Scanning : 

Polars allows you to scan a CSV input. Scanning delays the actual parsing of the file and instead returns a lazy computation holder called a LazyFrame.

    df = pl.scan_csv("path.csv")

Let's see now some examples regarding a JSON file.

Reading :

    df = pl.read_json("path.json")

Writting : 

    df = pl.DataFrame({"foo": [1, 2, 3], "bar": [None, "bak", "baz"]})
    # json
    df.write_json("path.json")
    # ndjson
    df.write_ndjson("path.json")

Scanning : 

    df = pl.scan_ndjson("path.json")

Polars can deal with multiple files differently depending on your needs and memory strain.
Let's create some files to give use some context:

    import polars as pl

    df = pl.DataFrame({"abo": [1, 2, 3], "velo": ["auto", "car", "spam"]})

    for i in range(5):
        df.write_csv(f"my_many_files_{i}.csv")


**Group By Concept**

One of the most efficient ways to process tabular data is to parallelize its processing via the "split-apply-combine" approach. This operation is at the core of the Polars grouping implementation, allowing it to attain lightning-fast operations. Specifically, both the "split" and "apply" phases are executed in a multi-threaded fashion.

A simple grouping operation is taken below as an example to illustrate this approach:

![Capture4 !](/capture4.png "capture 4")

For the hashing operations performed during the "split" phase, Polars uses a multithreaded lock-free approach that is illustrated on the following schema:

![Capture5 !](/capture5.png "capture 5")

Examples : 

    import polars as pl

    from .dataset import dataset

    q = (
        dataset.lazy()
        .groupby(["state", "party"])
        .agg([pl.count("party").alias("count")])
        .filter((pl.col("party") == "Anti-Administration") | (pl.col("party") == "Pro-Administration"))
        .sort("count", descending=True)
        .limit(5)
    )

    df = q.collect()
    
    shape: (5, 3)
    ┌───────┬─────────────────────┬───────┐
    │ state ┆ party               ┆ count │
    │ ---   ┆ ---                 ┆ ---   │
    │ cat   ┆ cat                 ┆ u32   │
    ╞═══════╪═════════════════════╪═══════╡
    │ NJ    ┆ Pro-Administration  ┆ 3     │
    │ VA    ┆ Anti-Administration ┆ 3     │
    │ CT    ┆ Pro-Administration  ┆ 3     │
    │ NC    ┆ Pro-Administration  ┆ 2     │
    │ NC    ┆ Anti-Administration ┆ 1     │
    └───────┴─────────────────────┴───────┘
    
## Practical example

**installation**

Many way are possible for the installation of Polar, all depend on your Operating System and the tool you are using to develop on python.
In our case we have a microsoft OS and we use Spyder as a tool to devepol on python. The installation will be done as follows :

* If you want to use other modules(polars, pandas, ...) in Spyder that don’t come with our installer, you need to install Miniconda (only if you don’t have Anaconda or Miniconda yet!). For Spyder to recognize it, the installation should be done in one of the following default paths:

  - C:\Users\<username>\Anaconda
  - C:\Users\<username>\Miniconda
  - C:\Users\<username>\Anaconda3
  - C:\Users\<username>\Anaconda3

In our case we use Anaconda3 so the path will be : C:\Users\DELL\anaconda3

* Then, you need to create a new conda environment with the modules that you want to use with Spyder and include **spyder-kernels** in it. For example, if you want to use scikit-learn, open your terminal or the Anaconda prompt on Windows and run the following commands:

  - conda create -n spyder-env -y
 
    ![Capture1 !](/capture1.png "capture 1")
    
  - conda activate spyder-env
    
  - conda install spyder-kernels scikit-learn -y

    ![Capture2 !](/capture2.png "capture 2")
  
* Finally, you need to connect Spyder to this environment by changing Spyder’s default Python interpreter. To do this, click the name of the current environment in the status bar, and then click Change default environment in Preferences.

* This will open the Preferences dialog in the Python interpreter section. Here, select the option Use the following Python interpreter, and use the dropdown below to select your preferred environment. If it is not listed, use the text box or the Select file button to enter the path to the Python interpreter you want to use.

     ![Capture3 !](/capture3.png "capture 3")

**Dependencies**
    
  polar requires : 
  
 - Python (>= 3.5)
 - NumPy (>= 1.11.0)
 - SciPy (>= 0.17.0)
 - Seaborn (>= 0.9.0)
 - scikit-learn (>= 0.21.3)
 - nltk (>= 3.4.5)
 - python-pptx (>= 0.6.18)
 - cryptography (> 2.8)
 - imblearn 

In this section we will go through some examples, but first let's create a dataset:

          import polars as pl
          import numpy as np

          np.random.seed(12)

          df = pl.DataFrame(
              {
                  "nrs": [1, 2, 3, None, 5],
                  "names": ["foo", "ham", "spam", "egg", None],
                  "random": np.random.rand(5),
                  "groups": ["A", "A", "B", "C", "B"],
              }
          )
          print(df)
          
 Output : 
      
        shape: (5, 4)
      ┌──────┬───────┬──────────┬────────┐
      │ nrs  ┆ names ┆ random   ┆ groups │
      │ ---  ┆ ---   ┆ ---      ┆ ---    │
      │ i64  ┆ str   ┆ f64      ┆ str    │
      ╞══════╪═══════╪══════════╪════════╡
      │ 1    ┆ foo   ┆ 0.154163 ┆ A      │
      │ 2    ┆ ham   ┆ 0.74005  ┆ A      │
      │ 3    ┆ spam  ┆ 0.263315 ┆ B      │
      │ null ┆ egg   ┆ 0.533739 ┆ C      │
      │ 5    ┆ null  ┆ 0.014575 ┆ B      │
      └──────┴───────┴──────────┴────────┘

Now we will try to calculate some informations like the sum, the minimum, the max, the variance, ... by using what we call aggregations.
Below are examples of some of them, but there are more such as median, mean, first, etc.

        out = df.select(
    [
        pl.sum("random").alias("sum"),
        pl.min("random").alias("min"),
        pl.max("random").alias("max"),
        pl.col("random").max().alias("other_max"),
        pl.std("random").alias("std dev"),
        pl.var("random").alias("variance"),
    ]
    )
    print(out)
    

Output : 

        shape: (1, 6)
      ┌──────────┬──────────┬─────────┬───────────┬──────────┬──────────┐
      │ sum      ┆ min      ┆ max     ┆ other_max ┆ std dev  ┆ variance │
      │ ---      ┆ ---      ┆ ---     ┆ ---       ┆ ---      ┆ ---      │
      │ f64      ┆ f64      ┆ f64     ┆ f64       ┆ f64      ┆ f64      │
      ╞══════════╪══════════╪═════════╪═══════════╪══════════╪══════════╡
      │ 1.705842 ┆ 0.014575 ┆ 0.74005 ┆ 0.74005   ┆ 0.293209 ┆ 0.085971 │
      └──────────┴──────────┴─────────┴───────────┴──────────┴──────────┘

We can also do some pretty complex things. In the next snippet we count all names ending with the string "am".

        out = df.select(
    [
        pl.col("names").filter(pl.col("names").str.contains(r"am$")).count(),
    ]
    )
    print(out)
    
Output : 

      shape: (1, 1)
        ┌───────┐
        │ names │
        │ ---   │
        │ u32   │
        ╞═══════╡
        │ 2     │
        └───────┘
        
A polars expression can also do an implicit GROUPBY, AGGREGATION, and JOIN in a single expression. In the examples below we do a GROUPBY OVER "groups" and AGGREGATE SUM of "random", and in the next expression we GROUPBY OVER "names" and AGGREGATE a LIST of "random". These window functions can be combined with other expressions and are an efficient way to determine group statistics. 

            df = df.select(
    [
        pl.col("*"),  # select all
        pl.col("random").sum().over("groups").alias("sum[random]/groups"),
        pl.col("random").list().over("names").alias("random/name"),
    ]
    )
    print(df)
    
Output : 
      
      shape: (5, 6)
      ┌──────┬───────┬──────────┬────────┬────────────────────┬─────────────┐
      │ nrs  ┆ names ┆ random   ┆ groups ┆ sum[random]/groups ┆ random/name │
      │ ---  ┆ ---   ┆ ---      ┆ ---    ┆ ---                ┆ ---         │
      │ i64  ┆ str   ┆ f64      ┆ str    ┆ f64                ┆ list[f64]   │
      ╞══════╪═══════╪══════════╪════════╪════════════════════╪═════════════╡
      │ 1    ┆ foo   ┆ 0.154163 ┆ A      ┆ 0.894213           ┆ [0.154163]  │
      │ 2    ┆ ham   ┆ 0.74005  ┆ A      ┆ 0.894213           ┆ [0.74005]   │
      │ 3    ┆ spam  ┆ 0.263315 ┆ B      ┆ 0.27789            ┆ [0.263315]  │
      │ null ┆ egg   ┆ 0.533739 ┆ C      ┆ 0.533739           ┆ [0.533739]  │
      │ 5    ┆ null  ┆ 0.014575 ┆ B      ┆ 0.27789            ┆ [0.014575]  │
      └──────┴───────┴──────────┴────────┴────────────────────┴─────────────┘

This tips listed previously is some of the thing that we can do with polars, next we are going to see the potential alternative existing.


## Potential alternative

Now lets list below the potential alternatives of polars : 

* db-benchmark : reproducible benchmark of database-like ops
* arrow-datafusion : Apache Arrow DataFusion SQL Query Engine
* Apache Arrow : Apache Arrow is a multi-language toolbox for accelerated data interchange and in-memory processing
* redpanda : Redpanda is a streaming data platform for developers. Kafka API compatible. 10x faster. No ZooKeeper. No JVM!
* plotters : A rust drawing library for high quality data plotting for both WASM and native, statically and realtimely.
* SaaSHub : SaaSHub - Software Alternatives and Reviews. SaaSHub helps you find the best software and product alternatives

## Concrete use case related to data analysis

Now we a are going to see an exammple related to data analysis.

We have a csv file named "food-consumption", we are going to do some manipulation of the data in this file.

First of all we need to import all the important library that we are going to use : 

    import polars as pl
    import numpy as np
    from sklearn.model_selection import train_test_split

In a Second time we read the CSV file : 

    df =pl.read_csv(r'C:\Users\DELL\Desktop\Abo\Perso\Master 1 IA DIT\Visualisation Exploration de données\food-consumption.csv')
    
    print(df)
    
    shape: (16, 21)
    ┌─────────┬─────────────┬─────────┬─────┬─────┬───────────┬───────────┬─────────┬─────────────┐
    │ Country ┆ Real coffee ┆ Instant ┆ Tea ┆ ... ┆ Margarine ┆ Olive oil ┆ Yoghurt ┆ Crisp bread │
    │ ---     ┆ ---         ┆ coffee  ┆ --- ┆     ┆ ---       ┆ ---       ┆ ---     ┆ ---         │
    │ str     ┆ i64         ┆ ---     ┆ i64 ┆     ┆ i64       ┆ i64       ┆ i64     ┆ i64         │
    │         ┆             ┆ i64     ┆     ┆     ┆           ┆           ┆         ┆             │
    ╞═════════╪═════════════╪═════════╪═════╪═════╪═══════════╪═══════════╪═════════╪═════════════╡
    │ Germany ┆ 90          ┆ 49      ┆ 88  ┆ ... ┆ 85        ┆ 74        ┆ 30      ┆ 26          │
    │ Italy   ┆ 82          ┆ 10      ┆ 60  ┆ ... ┆ 24        ┆ 94        ┆ 5       ┆ 18          │
    │ France  ┆ 88          ┆ 42      ┆ 63  ┆ ... ┆ 47        ┆ 36        ┆ 57      ┆ 3           │
    │ Holland ┆ 96          ┆ 62      ┆ 98  ┆ ... ┆ 97        ┆ 13        ┆ 53      ┆ 15          │
    │ ...     ┆ ...         ┆ ...     ┆ ... ┆ ... ┆ ...       ┆ ...       ┆ ...     ┆ ...         │
    │ Norway  ┆ 92          ┆ 17      ┆ 83  ┆ ... ┆ 94        ┆ 28        ┆ 2       ┆ 62          │
    │ Finland ┆ 98          ┆ 12      ┆ 84  ┆ ... ┆ 94        ┆ 17        ┆ null    ┆ 64          │
    │ Spain   ┆ 70          ┆ 40      ┆ 40  ┆ ... ┆ 51        ┆ 91        ┆ 16      ┆ 13          │
    │ Ireland ┆ 30          ┆ 52      ┆ 99  ┆ ... ┆ 25        ┆ 31        ┆ 3       ┆ 9           │
    └─────────┴─────────────┴─────────┴─────┴─────┴───────────┴───────────┴─────────┴─────────────┘


Selection of one column : 

    colonne =   df['Country']
    print(colonne)
    
    shape: (16,)
    Series: 'Country' [str]
    [
        "Germany"
        "Italy"
        "France"
        "Holland"
        "Belgium"
        "Luxembourg"
        "England"
        "Portugal"
        "Austria"
        "Switzerland"
        "Sweden"
        "Denmark"
        "Norway"
        "Finland"
        "Spain"
        "Ireland"
    ]

Always in the Exploratory Data Analys we can try to determine some values like the average and the standard.

    #Find the average of each of the numeric features.

    Real_coffee_mean = df['Real coffee'].mean()
    Instant_coffee_mean = df['Instant coffee'].mean()
    Tea_mean = df['Tea'].mean()


    # Find the standard deviation of each of the numeric features

    Real_coffee_std = df['Real coffee'].std()
    Instant_coffee_std = df['Instant coffee'].std()
    Tea_std = df['Tea'].std()

    # Printing the results
    print('Average Real_coffee score:',Real_coffee_mean)
    print('Average Instant_coffee score:',Instant_coffee_mean)
    print('Average Tea score:',Tea_mean )
    print('Real_coffee score standard deviation:',Real_coffee_std)
    print('Instant_coffee score standard deviation:',Instant_coffee_std)
    print('Tea score standard deviation:',Tea_std)
    
  output : 
       
    Average Real_coffee score: 78.5625
    Average Instant_coffee score: 39.25
    Average Tea score: 78.5
    Real_coffee score standard deviation: 23.14582395739384
    Instant_coffee score standard deviation: 23.147354060453647
    Tea score standard deviation: 18.54004674571597


   
## Conclusion

In conclusion, the Polars library is a powerful data processing library for Python, with similar functionality to Pandas. It is particularly useful for processing large data and offers tools for basic statistical operations, string manipulation and time data management. In addition, it offers the ability to create interactive graphics with the Vega-Lite library.

## Consulted resources

 * [Introduction - Polars - User Guide (pola-rs.github.io)](https://pola-rs.github.io/polars-book/user-guide/dsl/groupby.html)
 * [Learn Rust - Rust Programming Language (rust-lang.org)](https://www.rust-lang.org/)
 * https://pypi.org/project/polar/
 * https://docs.spyder-ide.org/5/faq.html#using-packages-installer
 * https://www.markdownguide.org/basic-syntax#paragraphs-1![image]
 * https://www.libhunt.com/r/polars


