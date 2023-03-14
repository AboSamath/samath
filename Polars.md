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

The Polars library is a data processing library for Python, which allows to manipulate tabular data in an efficient and fast way. 
This library provides similar functionality to Pandas, but with much higher performance, using Rust as the underlying language.

## Concept presentation

The Polars library is particularly useful for processing large data, as it uses task parallelization to speed up data transformation operations.
In the picture below we have a comparison regarding the performance with the other library. So we can notice that the time of the execution of the polar is smaller than the others.

![Polars !](/polarperformance.png "PolarsPerformance")

Now we are going to show how to install and use polar.

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
  - conda activate spyder-env
  - conda install spyder-kernels scikit-learn -y

    


