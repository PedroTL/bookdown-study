---
title: "Bookdown - A Short Introduction to R"
author: "João Pedro"
date: "28, julho, 2023"
output: 
  html_document:
    code_folding: hide
    keep_md: true
    css: styles.css
    toc: true
    toc_depth: 4
    theme: united
    highlight: tango
---

## 1. Nuts and Bolts

### 1.1 R Objects

R has five basic or “atomic” classes of objects:

- character
- numeric (real numbers)
- integer
- complex
- logical (True/False)

The most basic type of R object is a vector. Empty vectors can be created with the vector() function. There is really only one rule about vectors in R, which is that A vector can only contain objects of the same class.

But of course, like any good rule, there is an exception, which is a list, which we will get to a bit later. A list is represented as a vector but can contain objects of different classes. Indeed, that’s usually why we use them.

There is also a class for “raw” objects, but they are not commonly used directly in data analysis and I won’t cover them here.

### 1.2 Numbers

Numbers in R are generally treated as numeric objects (i.e. double precision real numbers). This means that even if you see a number like “1” or “2” in R, which you might think of as integers, they are likely represented behind the scenes as numeric objects (so something like “1.00” or “2.00”). This isn’t important most of the time…except when it is.

If you explicitly want an integer, you need to specify the L suffix. So entering 1 in R gives you a numeric object; entering 1L explicitly gives you an integer object.

There is also a special number Inf which represents infinity. This allows us to represent entities like 1 / 0. This way, Inf can be used in ordinary calculations; e.g. 1 / Inf is 0.

The value NaN represents an undefined value (“not a number”); e.g. 0 / 0; NaN can also be thought of as a missing value (more on that later)

### 1.3 Attributes

R objects can have attributes, which are like metadata for the object. These metadata can be very useful in that they help to describe the object. For example, column names on a data frame help to tell us what data are contained in each of the columns. Some examples of R object attributes are

- names, dimnames
- dimensions (e.g. matrices, arrays)
- class (e.g. integer, numeric)
- length
- other user-defined attributes/metadata

Attributes of an object (if any) can be accessed using the attributes() function. Not all R objects contain attributes, in which case the attributes() function returns NULL.

### Creating Vectors

The c() function can be used to create vectors of objects by concatenating things together.


```r
x <- c(0.5, 0.6) # Numeric
x <- c(TRUE, FALSE) # Logical
x <- c(T, F) # Logical
x <- c("a", "b", "c") # Character
x <- 9:29 # Integer
x <- c(1+0i, 2+4i) # Complex
```

You can also use the vector() function to initialize vectors.


```r
x <- vector("numeric", length = 10)
x
```

```
##  [1] 0 0 0 0 0 0 0 0 0 0
```

### 1.4 Mixing Objects

There are occasions when different classes of R objects get mixed together.


```r
y <- c(1.7, "a") # Character
y <- c(TRUE, 2) # Numeric
y <- c("a", TRUE) # Character
```

In each case above, we are mixing objects of two different classes in a vector. But remember that the only rule about vectors says this is not allowed. When different objects are mixed in a vector, coercion occurs so that every element in the vector is of the same class.

In the example above, we see the effect of implicit coercion. What R tries to do is find a way to represent all of the objects in the vector in a reasonable fashion. Sometimes this does exactly what you want and…sometimes not. For example, combining a numeric object with a character object will create a character vector, because numbers can usually be easily represented as strings.

### 1.5 Explicit Coercion

Objects can be explicitly coerced from one class to another using the as.* functions, if available.


```r
x <- 0.6
class(x)
```

```
## [1] "numeric"
```

```r
as.numeric(x)
```

```
## [1] 0.6
```

```r
as.logical(x)
```

```
## [1] TRUE
```

```r
as.character(x)
```

```
## [1] "0.6"
```

Sometimes, R can’t figure out how to coerce an object and this can result in NAs being produced.


```r
x <- c("a", "b", "c")

as.numeric(x)
```

```
## Warning: NAs introduzidos por coerção
```

```
## [1] NA NA NA
```

```r
as.logical(x)
```

```
## [1] NA NA NA
```

```r
as.complex(x)
```

```
## Warning: NAs introduzidos por coerção
```

```
## [1] NA NA NA
```

### Matrices

Matrices are vectors with a dimension attribute. The dimension attribute is itself an integer vector of length 2 (number of rows, number of columns)


```r
m <- matrix(nrow = 2, ncol = 3)
m
```

```
##      [,1] [,2] [,3]
## [1,]   NA   NA   NA
## [2,]   NA   NA   NA
```

```r
dim(m)
```

```
## [1] 2 3
```

```r
attributes(m)
```

```
## $dim
## [1] 2 3
```

Matrices are constructed column-wise, so entries can be thought of starting in the “upper left” corner and running down the columns.


```r
m <- matrix(1:6, nrow = 2, ncol = 3)
m
```

```
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6
```

Matrices can also be created directly from vectors by adding a dimension attribute.


```r
m <- 1:10
m
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10
```

```r
dim(m) <- c(2, 5)
m
```

```
##      [,1] [,2] [,3] [,4] [,5]
## [1,]    1    3    5    7    9
## [2,]    2    4    6    8   10
```

Matrices can be created by column-binding or row-binding with the cbind() and rbind() functions.


```r
x <- 1:3
y <- 10:12

cbind(x, y)
```

```
##      x  y
## [1,] 1 10
## [2,] 2 11
## [3,] 3 12
```

```r
rbind(x, y)
```

```
##   [,1] [,2] [,3]
## x    1    2    3
## y   10   11   12
```

### 1.5 Lists

Lists are a special type of vector that can contain elements of different classes. Lists are a very important data type in R and you should get to know them well. Lists, in combination with the various “apply” functions discussed later, make for a powerful combination.

Lists can be explicitly created using the list() function, which takes an arbitrary number of arguments.


```r
x <- list(1, "a", TRUE, 1 +4i)
x
```

```
## [[1]]
## [1] 1
## 
## [[2]]
## [1] "a"
## 
## [[3]]
## [1] TRUE
## 
## [[4]]
## [1] 1+4i
```

We can also create an empty list of a prespecified length with the vector() function


```r
x <- vector("list", length = 5)
x
```

```
## [[1]]
## NULL
## 
## [[2]]
## NULL
## 
## [[3]]
## NULL
## 
## [[4]]
## NULL
## 
## [[5]]
## NULL
```

### 1.6 Factors

Factors are used to represent categorical data and can be unordered or ordered. One can think of a factor as an integer vector where each integer has a label. Factors are important in statistical modeling and are treated specially by modelling functions like lm() and glm().

Using factors with labels is better than using integers because factors are self-describing. Having a variable that has values “Male” and “Female” is better than a variable that has values 1 and 2.

Factor objects can be created with the factor() function.


```r
x <- factor(c("yes", "yes", "no", "yes", "no"))
x
```

```
## [1] yes yes no  yes no 
## Levels: no yes
```

```r
table(x)
```

```
## x
##  no yes 
##   2   3
```

```r
## See the underlying representation of factor
unclass(x)
```

```
## [1] 2 2 1 2 1
## attr(,"levels")
## [1] "no"  "yes"
```

```r
attr(x, "levels")
```

```
## [1] "no"  "yes"
```

Often factors will be automatically created for you when you read a dataset in using a function like read.table(). Those functions often default to creating factors when they encounter data that look like characters or strings.

The order of the levels of a factor can be set using the levels argument to factor(). This can be important in linear modelling because the first level is used as the baseline level.


```r
x <- factor(c("yes", "yes", "no", "yes", "no"))
x
```

```
## [1] yes yes no  yes no 
## Levels: no yes
```

```r
## Levels are put in alphabetical order
x <- factor(c("yes", "yes", "no", "yes", "no"),
            levels = c("yes", "no"))
x
```

```
## [1] yes yes no  yes no 
## Levels: yes no
```

### 1.7 Missing Values

Missing values are denoted by NA or NaN for q undefined mathematical operations.

- is.na() is used to test objects if they are NA
- is.nan() is used to test for NaN
- NA values have a class also, so there are integer NA, character NA, etc.
- A NaN value is also NA but the converse is not true


```r
## Create a vector with NAs in it
x <- c(1, 2, NA, 10, 3)

## Return a logical vector indicating which elements are NA
is.na(x)
```

```
## [1] FALSE FALSE  TRUE FALSE FALSE
```

```r
## Return a logical vector indicating which elements are NaN
is.nan(x)
```

```
## [1] FALSE FALSE FALSE FALSE FALSE
```


```r
## Now create a vector with both NA and NaN values
x <- c(1, 2, NaN, NA, 4)

is.na(x)
```

```
## [1] FALSE FALSE  TRUE  TRUE FALSE
```

```r
is.nan(x)
```

```
## [1] FALSE FALSE  TRUE FALSE FALSE
```

### 1.8 Data Frames

Data frames are represented as a special type of list where every element of the list has to have the same length. Each element of the list can be thought of as a column and the length of each element of the list is the number of rows.

Unlike matrices, data frames can store different classes of objects in each column. Matrices must have every element be the same class (e.g. all integers or all numeric).

In addition to column names, indicating the names of the variables or predictors, data frames have a special attribute called row.names which indicate information about each row of the data frame.

Data frames are usually created by reading in a dataset using the read.table() or read.csv(). However, data frames can also be created explicitly with the data.frame() function or they can be coerced from other types of objects like lists.

Data frames can be converted to a matrix by calling data.matrix(). While it might seem that the as.matrix() function should be used to coerce a data frame to a matrix, almost always, what you want is the result of data.matrix().


```r
x <- data.frame(foo = 1:4,
                bar = c(T, T, F, F))
x
```

```
##   foo   bar
## 1   1  TRUE
## 2   2  TRUE
## 3   3 FALSE
## 4   4 FALSE
```

```r
nrow(x)
```

```
## [1] 4
```

```r
ncol(x)
```

```
## [1] 2
```

### 1.9 Names

R objects can have names, which is very useful for writing readable code and self-describing objects. Here is an example of assigning names to an integer vector.


```r
x <- 1:3
names(x)
```

```
## NULL
```

```r
names(x) <- c("NW", "SA", "LA")
x
```

```
## NW SA LA 
##  1  2  3
```

```r
names(x)
```

```
## [1] "NW" "SA" "LA"
```

Lists can also have names, which is often very useful.


```r
x <- list("LA" = 1, Boston = 2, "London" = 3)
x
```

```
## $LA
## [1] 1
## 
## $Boston
## [1] 2
## 
## $London
## [1] 3
```

```r
names(x)
```

```
## [1] "LA"     "Boston" "London"
```

Matrices can have both column and row names.


```r
m <- matrix(1:4, nrow = 2, ncol = 2)
dimnames(m) <- list(c("a", "b"), # Row names
                    c("c", "d")) # Col names
```

Column names and row names can be set separately using the colnames() and rownames() functions.


```r
colnames(m) <- c("h", "f")
rownames(m) <- c("x", "z")
```

Note that for data frames, there is a separate function for setting the row names, the row.names() function. Also, data frames do not have column names, they just have names (like lists). So to set the column names of a data frame just use the names() function. Yes, I know its confusing. Here’s a quick summary:

Object: data frame | Set column names: names() | Set row names: row.names() <br>
Object: Matrix | Set column names: colnames() | Set row names: rownames()

### 1.10 Summary 

There are a variety of different builtin-data types in R. In this chapter we have reviewed the following

- atomic classes: numeric, logical, character, integer, complex
- vectors, lists
- factors
- missing values
- data frames and matrices

All R objects can have attributes that help to describe what is in the object. Perhaps the most useful attribute is names, such as column and row names in a data frame, or simply names in a vector or list. Attributes like dimensions are also important as they can modify the behavior of objects, like turning a vector into a matrix.

## 2. Getting Data in and Out of R

### 2.1 Reading and Writing Data

There are a few principal functions reading data into R.

- read.table, read.csv, for reading tabular data
- readLines, for reading lines of a text file
- source, for reading in R code files (inverse of dump)
- dget, for reading in R code files (inverse of dput)
- load, for reading in saved workspaces
- unserialize, for reading single R objects in binary form

There are of course, many R packages that have been developed to read in all kinds of other datasets, and you may need to resort to one of these packages if you are working in a specific area.

There are analogous functions for writing data to files

- write.table, for writing tabular data to text files (i.e. CSV) or connections
- writeLines, for writing character data line-by-line to a file or connection
-dump, for dumping a textual representation of multiple R objects
- dput, for outputting a textual representation of an R object
- save, for saving an arbitrary number of R objects in binary format (possibly compressed) to a file.
- serialize, for converting an R object into a binary format for outputting to a connection (or file).

The read.table() function has a few important arguments:

- file, the name of a file, or a connection
- header, logical indicating if the file has a header line
- sep, a string indicating how the columns are separated
- colClasses, a character vector indicating the class of each column in the dataset
- nrows, the number of rows in the dataset. By default read.table() reads an entire file.
- comment.char, a character string indicating the comment character. This defalts to "#". If there are no commented lines in your file, it’s worth setting this to be the empty string "".
- skip, the number of lines to skip from the beginning
- stringsAsFactors, should character variables be coded as factors? This defaults to TRUE because back in the old days, if you had data that were stored as strings, it was because those strings represented levels of a categorical variable. Now we have lots of data that is text data and they don’t always represent categorical variables. So you may want to set this to be FALSE in those cases. If you always want this to be FALSE, you can set a global option via options(stringsAsFactors = FALSE). I’ve never seen so much heat generated on discussion forums about an R function argument than the stringsAsFactors argument. Seriously.

Telling R all these things directly makes R run faster and more efficiently. The read.csv() function is identical to read.table except that some of the defaults are set differently (like the sep argument).

### 2.2 Reading in Larger Datasets with read.table

With much larger datasets, there are a few things that you can do that will make your life easier and will prevent R from choking.

- Read the help page for read.table, which contains many hints

- Make a rough calculation of the memory required to store your dataset (see the next section for an example of how to do this). If the dataset is larger than the amount of RAM on your computer, you can probably stop right here.

- Set comment.char = "" if there are no commented lines in your file.

- Use the colClasses argument. Specifying this option instead of using the default can make ’read.table’ run MUCH faster, often twice as fast. In order to use this option, you have to know the class of each column in your data frame. If all of the columns are “numeric”, for example, then you can just set colClasses = "numeric". A quick an dirty way to figure out the classes of each column is the following:


```r
initial <- read.table("datatable.txt", nrow = 100)
classes <- sapply(initial, class)
tabAll <- read.table("datatable.txt", colClasses = classes)
```

- Set nrows. This doesn’t make R run faster but it helps with memory usage. A mild overestimate is okay. You can use the Unix tool wc to calculate the number of lines in a file.

## 3. Using the readr Package

he readr package is recently developed by Hadley Wickham to deal with reading in large flat files quickly. The package provides replacements for functions like read.table() and read.csv(). The analogous functions in readr are read_table() and read_csv(). These functions are often much faster than their base R analogues and provide a few other nice features such as progress meters.

For the most part, you can read use read_table() and read_csv() pretty much anywhere you might use read.table() and read.csv(). In addition, if there are non-fatal problems that occur while reading in the data, you will get a warning and the returned data frame will have some information about which rows/observations triggered the warning. This can be very helpful for “debugging” problems with your data before you get neck deep in data analysis.

The importance of the read_csv function is perhaps better understood from an historical perspective. R’s built in read.csv function similarly reads CSV files, but the read_csv function in readr builds on that by removing some of the quirks and “gotchas” of read.csv as well as dramatically optimizing the speed with which it can read data into R. The read_csv function also adds some nice user-oriented features like a progress meter and a compact method for specifying column types.

A typical call to read_csv will look as follows.


```r
library(readr)
teams <- read_csv("data/team_standings.csv")
```

By default, read_csv will open a CSV file and read it in line-by-line. It will also (by default), read in the first few rows of the table in order to figure out the type of each column (i.e. integer, character, etc.). From the read_csv help page:

If ‘NULL’, all column types will be imputed from the first 1000 rows on the input. This is convenient (and fast), but not robust. If the imputation fails, you’ll need to supply the correct types yourself.

You can specify the type of each column with the col_types argument.

In general, it’s a good idea to specify the column types explicitly. This rules out any possible guessing errors on the part of read_csv. Also, specifying the column types explicitly provides a useful safety check in case anything about the dataset should change without you knowing about it.


```r
library(readr)
teams <- read_csv("data/team_standings.csv", col_types = "cc") # First and second column are Charecters
```

Note that the col_types argument accepts a compact representation. Here "cc" indicates that the first column is character and the second column is character (there are only two columns). Using the col_types argument is useful because often it is not easy to automatically figure out the type of a column by looking at a few rows (especially if a column has many missing values).

The read_csv function will also read compressed files automatically. There is no need to decompress the file first or use the gzfile connection function. The following call reads a gzip-compressed CSV file containing download logs from the RStudio CRAN mirror.


```r
logs <- read_csv("data/2016-07-19.csv.bz2", n_max = 10)
```

Note that the warnings indicate that read_csv may have had some difficulty identifying the type of each column. This can be solved by using the col_types argument.


```r
logs <- read.csv("data/2016-07-19.csv.bz2", col_types = "ccicccccci", n_max = 10)
```

You can specify the column type in a more detailed fashion by using the various col_* functions. For example, in the log data above, the first column is actually a date, so it might make more sense to read it in as a Date variable. If we wanted to just read in that first column, we could do


```r
logdates <- read_csv("data/2016-07-19.csv.bz2",
                     col_types = cols_only(date = col_date()),
                     n_max = 10)
```

Now the date column is stored as a Date object which can be used for relevant date-related computations

