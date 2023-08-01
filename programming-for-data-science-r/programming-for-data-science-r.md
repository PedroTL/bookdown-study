---
title: "Bookdown - A Short Introduction to R"
author: "João Pedro"
date: "01, agosto, 2023"
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

```r
library(knitr)
opts_knit$set(root.dir = "C:\\Users\\pedro\\Documents\\GitHub\\bookdown-study\\short-introduction-r\\data")
```

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

## 4. Using Textual and Binary Formats for Storing Data

There are a variety of ways that data can be stored, including structured text files like CSV or tab-delimited, or more complex binary formats. However, there is an intermediate format that is textual, but not as simple as something like CSV. The format is native to R and is somewhat readable because of its textual nature.

One can create a more descriptive representation of an R object by using the dput() or dump() functions. The dump() and dput() functions are useful because the resulting textual format is edit-able, and in the case of corruption, potentially recoverable. Unlike writing out a table or CSV file, dump() and dput() preserve the metadata (sacrificing some readability), so that another user doesn’t have to specify it all over again. For example, we can preserve the class of each column of a table or the levels of a factor variable.

Textual formats can work much better with version control programs like subversion or git which can only track changes meaningfully in text files. In addition, textual formats can be longer-lived; if there is corruption somewhere in the file, it can be easier to fix the problem because one can just open the file in an editor and look at it (although this would probably only be done in a worst case scenario!). Finally, textual formats adhere to the Unix philosophy, if that means anything to you.

There are a few downsides to using these intermediate textual formats. The format is not very space-efficient, because all of the metadata is specified. Also, it is really only partially readable. In some instances it might be preferable to have data stored in a CSV file and then have a separate code file that specifies the metadata.

### 4.1 Using dput() and dump()

One way to pass data around is by deparsing the R object with dput() and reading it back in (parsing it) using dget().


```r
y <- data.frame(a = 1,
                b = "a")

# Prind dput output to consolde
dput(y)
```

```
## structure(list(a = 1, b = "a"), class = "data.frame", row.names = c(NA, 
## -1L))
```

Notice that the dput() output is in the form of R code and that it preserves metadata like the class of the object, the row names, and the column names.

The output of dput() can also be saved directly to a file.


```r
## Send dput output to a file
dput(y, file = "y.R")

## Read in dput output from a file
new.y <- dget("y.R")
```

Multiple objects can be deparsed at once using the dump function and read back in using source.


```r
x <- "foo"
y <- data.frame(a = 1L,
                b = "a")
```

We can dump() R objects to a file by passing a character vector of their names.


```r
dump(c("x", "y"), file = "data.R")
rm(x, y)
```

The inverse of dump() is source().


```r
source("data.R")
str(y)
```

```
## 'data.frame':	1 obs. of  2 variables:
##  $ a: int 1
##  $ b: chr "a"
```

### 4.2 Binary Formats

The complement to the textual format is the binary format, which is sometimes necessary to use for efficiency purposes, or because there’s just no useful way to represent data in a textual manner. Also, with numeric data, one can often lose precision when converting to and from a textual format, so it’s better to stick with a binary format.

The key functions for converting R objects into a binary format are save(), save.image(), and serialize(). Individual R objects can be saved to a file using the save() function.


```r
a <- data.frame(x = rnorm(100),
                y = runif(100))
b <- c(3, 4.4, 1/3)

# Save `a` and `b` to a file
save(a, b, file = "mydata.rda")

# Load `a` and `b` into your workspace
load("mydata.rda")
```

If you have a lot of objects that you want to save to a file, you can save all objects in your workspace using the save.image() function.


```r
# Save everything to a file
save.image(file = "mydata.RData")

# Load all objects in this file
load("mydata.RData")
```

Notice that I’ve used the .rda extension when using save() and the .RData extension when using save.image(). This is just my personal preference; you can use whatever file extension you want. The save() and save.image() functions do not care. However, .rda and .RData are fairly common extensions and you may want to use them because they are recognized by other software.

The serialize() function is used to convert individual R objects into a binary format that can be communicated across an arbitrary connection. This may get sent to a file, but it could get sent over a network or other connection.

When you call serialize() on an R object, the output will be a raw vector coded in hexadecimal format.


```r
x <- list(1, 2, 3)
serialize(x, NULL)
```

```
##  [1] 58 0a 00 00 00 03 00 04 02 03 00 03 05 00 00 00 00 05 55 54 46 2d 38 00 00
## [26] 00 13 00 00 00 03 00 00 00 0e 00 00 00 01 3f f0 00 00 00 00 00 00 00 00 00
## [51] 0e 00 00 00 01 40 00 00 00 00 00 00 00 00 00 00 0e 00 00 00 01 40 08 00 00
## [76] 00 00 00 00
```

The benefit of the serialize() function is that it is the only way to perfectly represent an R object in an exportable format, without losing precision or any metadata. If that is what you need, then serialize() is the function for you.

## 5. Interfaces to the Outside World

Data are read in using connection interfaces. Connections can be made to files (most common) or to other more exotic things.

- file, opens a connection to a file
- gzfile, opens a connection to a file compressed with gzip
- bzfile, opens a connection to a file compressed with bzip2
- url, opens a connection to a webpage

In general, connections are powerful tools that let you navigate files or other external objects. Connections can be thought of as a translator that lets you talk to objects that are outside of R. Those outside objects could be anything from a data base, a simple text file, or a a web service API. Connections allow R functions to talk to all these different external objects without you having to write custom code for each object.

### 5.1 File Connections

Connections to text files can be created with the file() function.


```r
str(file)
```

```
## function (description = "", open = "", blocking = TRUE, encoding = getOption("encoding"), 
##     raw = FALSE, method = getOption("url.method", "default"))
```

The file() function has a number of arguments that are common to many other connection functions so it’s worth going into a little detail here.

- description is the name of the file
- open is a code indicating what mode the file should be opened in

The open argument allows for the following options:

- “r” open file in read only mode
- “w” open a file for writing (and initializing a new file)
- “a” open a file for appending
- “rb”, “wb”, “ab” reading, writing, or appending in binary mode (Windows)

In practice, we often don’t need to deal with the connection interface directly as many functions for reading and writing data just deal with it in the background.

For example, if one were to explicitly use connections to read a CSV file in to R, it might look like this


```r
## Create a connection to 'foo.txt'
con <- file("foo.txt")

## Open connection to 'foo.txt' in read-only mode
open(con, "r")

## Read from the connection
data <- read.csv(con)

## Close the connection
close(con)
```

which is the same as


```r
data <- read.csv("foo.txt")
```

The above example shows the basic approach to using connections. Connections must be opened, then the are read from or written to, and then they are closed.

### Reading Lines of a Text File

Text files can be read line by line using the readLines() function. This function is useful for reading text files that may be unstructured or contain non-standard data.


```r
## Open connection to gz-compressed text file
con <- gzfile("words.gz")
x <- readLines(con, 10)
x
```

For more structured text data like CSV files or tab-delimited files, there are other functions like read.csv() or read.table().

The above example used the gzfile() function which is used to create a connection to files compressed using the gzip algorithm. This approach is useful because it allows you to read from a file without having to uncompress the file first, which would be a waste of space and time.

There is a complementary function writeLines() that takes a character vector and writes each element of the vector one line at a time to a text file.

### 5.2 Reading From a URL connection

The readLines() function can be useful for reading in lines of webpages. Since web pages are basically text files that are stored on a remote server, there is conceptually not much difference between a web page and a local text file. However, we need R to negotiate the communication between your computer and the web server. This is what the url() function can do for you, by creating a url connection to a web server.

This code might take time depending on your connection speed.


```r
## Open a URL connection for reading
con <- url("https//www.jhu.edu", "r")

## Read the webpage
x <- readLines(con)

## Print out the first few lines
head(x)
```

We can use URL connection to read in specific data files that are stored on web servers.

## 6. Subsetting R Objects

There are three operators that can be used to extract subsets of R objects.

- The [ operator always returns an object of the same class as the original. It can be used to select multiple elements of an object

- The [[ operator is used to extract elements of a list or a data frame. It can only be used to extract a single element and the class of the returned object will not necessarily be a list or data frame.

- The $ operator is used to extract elements of a list or data frame by literal name. Its semantics are similar to that of [[.

### 6.1 Subsetting a vector

Vectors are basic objects in R and they can be subsetted using the [ operator.


```r
x <- c("a", "b", "c", "d", "e", "a")
x[1] ## Extract the first element
```

```
## [1] "a"
```

```r
x[2] ## Extract the second element
```

```
## [1] "b"
```

The [ operator can be used to extract multiple elements of a vector by passing the operator an integer sequence. Here we extract the first four elements of the vector.


```r
x[1:4]
```

```
## [1] "a" "b" "c" "d"
```

The sequence does not have to be in order; you can specify any arbitrary integer vector.


```r
x[c(1, 3, 4)]
```

```
## [1] "a" "c" "d"
```

We can also pass a logical sequence to the [ operator to extract elements of a vector that satisfy a given condition. For example, here we want the elements of x that come lexicographically after the letter “a”.


```r
u <- x > "a"
x[u]
```

```
## [1] "b" "c" "d" "e"
```

Another, more compact, way to do this would be to skip the creation of a logical vector and just subset the vector directly with the logical expression.


```r
x[x > "a"]
```

```
## [1] "b" "c" "d" "e"
```

### 7.2 Subsetting a Matrix

Matrices can be subsetted in the usual way with (i,j) type indices. Here, we create simple 2×3 matrix with the matrix function.


```r
x <- matrix(1:6, ncol = 3, nrow = 2)
x
```

```
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6
```

We can access the (1,2) or the (2,1) element of this matrix using the appropriate indices.


```r
x[1,2]
```

```
## [1] 3
```

```r
x[2,1]
```

```
## [1] 2
```

Indices can also be missing. This behavior is used to access entire rows or columns of a matrix.


```r
x[1, ]
```

```
## [1] 1 3 5
```

```r
x[2, ]
```

```
## [1] 2 4 6
```

#### 7.2.1 Dropping matrix

By default, when a single element of a matrix is retrieved, it is returned as a vector of length 1 rather than a 1×1 matrix. Often, this is exactly what we want, but this behavior can be turned off by setting drop = FALSE


```r
x <- matrix(1:6, 2, 3)
x[1,2]
```

```
## [1] 3
```

```r
x[1,2, drop = FALSE]
```

```
##      [,1]
## [1,]    3
```

```r
x
```

```
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6
```

Similarly, when we extract a single row or column of a matrix, R by default drops the dimension of length 1, so instead of getting a  1×3 matrix after extracting the first row, we get a vector of length 3. This behavior can similarly be turned off with the drop = FALSE option.


```r
x <- matrix(1:6, 2, 3)
x[1, ]
```

```
## [1] 1 3 5
```

```r
x[1, ,drop = FALSE]
```

```
##      [,1] [,2] [,3]
## [1,]    1    3    5
```

Be careful of R’s automatic dropping of dimensions. This is a feature that is often quite useful during interactive work, but can later come back to bite you when you are writing longer programs or functions.

### 7.3 Subsetting Lists

Lists in R can be subsetted using all three of the operators mentioned above, and all three are used for different purposes.


```r
x <- list(foo = 1:4, bar = 0.6)
x
```

```
## $foo
## [1] 1 2 3 4
## 
## $bar
## [1] 0.6
```

The [[ operator can be used to extract single elements from a list. Here we extract the first element of the list.


```r
x[[1]]
```

```
## [1] 1 2 3 4
```

The [[ operator can also use named indices so that you don’t have to remember the exact ordering of every element of the list. You can also use the $ operator to extract elements by name.


```r
x[["bar"]]
```

```
## [1] 0.6
```

```r
x$bar
```

```
## [1] 0.6
```

Notice you don’t need the quotes when you use the $ operator.

One thing that differentiates the [[ operator from the $ is that the [[ operator can be used with computed indices. The $ operator can only be used with literal names.


```r
x <- list(foo = 1:4, bar = 0.6, baz = "Hello")
name <- "foo"

## Computed index for foo
x[[name]]
```

```
## [1] 1 2 3 4
```

```r
## Element name dosen`t exist! (But no error here)
x$name
```

```
## NULL
```

```r
## Element "foo" does exist
x$foo
```

```
## [1] 1 2 3 4
```

### 7.4 Subsetting Nestes Elements of a List

The [[ operator can take an integer sequence if you want to extract a nested element of a list.


```r
x <- list(a = list(10, 12, 14),
          b = c(3.14, 2.81))

## Get the 3rd element of the 1 element
x[[c(1, 3)]]
```

```
## [1] 14
```

```r
## Same as above
x[[1]][[3]]
```

```
## [1] 14
```

```r
## 1st element of the 2nd element
x[[c(2, 1)]]
```

```
## [1] 3.14
```

```r
## Same as above
x[[2]][[1]]
```

```
## [1] 3.14
```

### 7.5 Extracting Multiple Elements of a List

The [ operator can be used to extract multiple elements from a list. For example, if you wanted to extract the first and third elements of a list, you would do the following


```r
x <- list(foo = 1:4, 
          bar = 0.6,
          baz = "Hello")
x[c(1, 3)]
```

```
## $foo
## [1] 1 2 3 4
## 
## $baz
## [1] "Hello"
```

Note that


```r
# This is not the same
x[c(1, 3)]
```

```
## $foo
## [1] 1 2 3 4
## 
## $baz
## [1] "Hello"
```

```r
# As this
x[[c(1, 3)]]
```

```
## [1] 3
```

Remember that the [ operator always returns an object of the same class as the original. Since the original object was a list, the [ operator returns a list. In the above code, we returned a list with two elements (the first and the third).

### 7.6 Partial Matching

Partial matching of names is allowed with [[ and $. This is often very useful during interactive work if the object you’re working with has very long element names. You can just abbreviate those names and R will figure out what element you’re referring to.


```r
x <- list(aardvark = 1:5)
x$a
```

```
## [1] 1 2 3 4 5
```

```r
x[["a"]]
```

```
## NULL
```


```r
x[["a", extract = FALSE]]
```

In general, this is fine for interactive work, but you shouldn’t resort to partial matching if you are writing longer scripts, functions, or programs. In those cases, you should refer to the full element name if possible. That way there’s no ambiguity in your code.

### 7.7 Removing NA Values

A common task in data analysis is removing missing values (NAs).


```r
x <- c(1, 2, NA, 4, NA, 5)
bad <- is.na(x)
print(bad)
```

```
## [1] FALSE FALSE  TRUE FALSE  TRUE FALSE
```

```r
x[!bad]
```

```
## [1] 1 2 4 5
```

What if there are multiple R objects and you want to take the subset with no missing values in any of those objects?


```r
x <- c(1, 2, NA, 4, NA, 5)
y <- c("a", "b", NA, "d", NA, "f")
good <- complete.cases(x, y)
good
```

```
## [1]  TRUE  TRUE FALSE  TRUE FALSE  TRUE
```

```r
x[good]
```

```
## [1] 1 2 4 5
```

```r
y[good]
```

```
## [1] "a" "b" "d" "f"
```

You can use complete.cases on data frames too.


```r
head(airquality)
```

```
##   Ozone Solar.R Wind Temp Month Day
## 1    41     190  7.4   67     5   1
## 2    36     118  8.0   72     5   2
## 3    12     149 12.6   74     5   3
## 4    18     313 11.5   62     5   4
## 5    NA      NA 14.3   56     5   5
## 6    28      NA 14.9   66     5   6
```

```r
good <- complete.cases(airquality)
head(airquality[good, ])
```

```
##   Ozone Solar.R Wind Temp Month Day
## 1    41     190  7.4   67     5   1
## 2    36     118  8.0   72     5   2
## 3    12     149 12.6   74     5   3
## 4    18     313 11.5   62     5   4
## 7    23     299  8.6   65     5   7
## 8    19      99 13.8   59     5   8
```

## 8. Vecotirez operations

Many operations in R are vectorized, meaning that operations occur in parallel in certain R objects. This allows you to write code that is efficient, concise, and easier to read than in non-vectorized languages.

The simplest example is when adding two vectors together.


```r
x <- 1:4
y <- 6:9
z <- x + y
z
```

```
## [1]  7  9 11 13
```

Natural, right? Without vectorization, you’d have to do something like


```r
z <- numeric(length(x))
for(i in seq_along(x)) {
  z[i] <- x[i] + y[i]
}
z
```

```
## [1]  7  9 11 13
```

Another operation you can do in a vectorized manner is logical comparisons. So suppose you wanted to know which elements of a vector were greater than 2. You could do he following


```r
x
```

```
## [1] 1 2 3 4
```

```r
x > 2
```

```
## [1] FALSE FALSE  TRUE  TRUE
```

```r
x >= 2
```

```
## [1] FALSE  TRUE  TRUE  TRUE
```

```r
x < 3
```

```
## [1]  TRUE  TRUE FALSE FALSE
```

```r
y == 8
```

```
## [1] FALSE FALSE  TRUE FALSE
```

Notice that these logical operations return a logical vector of TRUE and FALSE.

Of course, subtraction, multiplication and division are also vectorized.


```r
x - y
```

```
## [1] -5 -5 -5 -5
```

```r
x * y
```

```
## [1]  6 14 24 36
```

```r
x / y
```

```
## [1] 0.1666667 0.2857143 0.3750000 0.4444444
```

### 8.1 Vectorized Matrix Operations

Matrix operations are also vectorized, making for nicly compact notation. This way, we can do element-by-element operations on matrices without having to loop over every element.


```r
x <- matrix(1:4, 2, 2)
y <- matrix(rep(10, 4), 2, 2)

x
```

```
##      [,1] [,2]
## [1,]    1    3
## [2,]    2    4
```

```r
y
```

```
##      [,1] [,2]
## [1,]   10   10
## [2,]   10   10
```

```r
## Element wise multiplication
x * y
```

```
##      [,1] [,2]
## [1,]   10   30
## [2,]   20   40
```

```r
## Element wise division
x / y
```

```
##      [,1] [,2]
## [1,]  0.1  0.3
## [2,]  0.2  0.4
```

```r
## True matrix multiplication
x %*% y
```

```
##      [,1] [,2]
## [1,]   40   40
## [2,]   60   60
```

## Exercises

Exercise 1: Using dput and dump<br>
a) Create a numeric vector with random values and use dput to save it into a file named "my_vector.R".<br>
b) Create a data frame with random values and use dump to save it into a file named "my_data.R".<br>


```r
## A
my_vector <- c(1, 2, 3.2)
dput(my_vector, 
     file = "my.vector.R")

## B
my_df <- data.frame(A = c(1, 2, 3.2),
                    B = c(2, 3, 2.2))

dump(my_df, 
     file = "my_df.R")
```


Exercise 2: Subsetting Vectors and Removing NAs<br>
a) Create a numeric vector with some NA values.<br>
b) Use subsetting to extract the elements of the vector that are not NA.<br>
c) Calculate the mean of the non-NA elements.<br>


```r
## A
my_vector2 <- c(1, 2, 3, NA, 2, NA)

## B
vector_na <- is.na(my_vector2)
my_vector2[!vector_na]
```

```
## [1] 1 2 3 2
```

```r
vector_na2 <- complete.cases(my_vector2)
my_vector2[vector_na2]
```

```
## [1] 1 2 3 2
```

```r
## C
mean(!is.na(my_vector2))
```

```
## [1] 0.6666667
```

```r
mean(my_vector2[!vector_na])
```

```
## [1] 2
```

```r
mean(my_vector2[vector_na2])
```

```
## [1] 2
```


Exercise 3: Subsetting Matrices and Removing NAs<br>
a) Create a matrix with some NA values in different rows and columns.<br>
b) Use subsetting to extract the rows and columns that do not contain any NA values.<br>


```r
## A
my_matrix <- matrix(c(1:5, NA, 7, NA, NA), 3, 3)
my_matrix
```

```
##      [,1] [,2] [,3]
## [1,]    1    4    7
## [2,]    2    5   NA
## [3,]    3   NA   NA
```

```r
## B
na_matrix <- complete.cases(my_matrix)
na_matrix
```

```
## [1]  TRUE FALSE FALSE
```

```r
my_matrix[na_matrix, ]
```

```
## [1] 1 4 7
```

```r
my_matrix[, na_matrix]
```

```
## [1] 1 2 3
```


Exercise 4: Subsetting Lists and Removing NAs <br>
a) Create a list with multiple elements, including some NA values.<br>
b) Use subsetting to extract the elements that are not NA.<br>


```r
my_list <- list(letters = c("a", "b", NA, "c", NA),
                numbers = c(1, NA, 3, 4, NA))

na_list <- complete.cases(my_list)
na_list
```

```
## [1]  TRUE FALSE FALSE  TRUE FALSE
```

```r
my_list[[1]][na_list]
```

```
## [1] "a" "c"
```

```r
my_list[[2]][na_list]
```

```
## [1] 1 4
```

```r
my_list[[c(1, 2)]]
```

```
## [1] "b"
```

```r
my_list[[1]][[2]]
```

```
## [1] "b"
```


Exercise 5: Subsetting Data Frames and Removing NAs<br>
a) Create a data frame with some NA values in different columns.<br>
b) Use subsetting to extract rows where all columns have non-NA values.<br>


```r
my_df <- data.frame(numbers = c(2.2, 1, NA, 3, NA),
                    letters = c("a", NA, NA, "c", "d"),
                    rambom = c(1, 2, 3, 4, 5))
my_df
```

```
##   numbers letters rambom
## 1     2.2       a      1
## 2     1.0    <NA>      2
## 3      NA    <NA>      3
## 4     3.0       c      4
## 5      NA       d      5
```

```r
my_df<- my_df[complete.cases(my_df), ]
```

## 9. Dates and Times

R has developed a special representation for dates and times. Dates are represented by the Date class and times are represented by the POSIXct or the POSIXlt class. Dates are stored internally as the number of days since 1970-01-01 while times are stored internally as the number of seconds since 1970-01-01.

### 9.1 Date in R

Dates are represented by the Date class and can be coerced from a character string using the as.Date() function. This is a common way to end up with a Date object in R.


```r
## Coerce a Date object from character
x <- as.Date("1970-01-01")
x
```

```
## [1] "1970-01-01"
```

You can see the internal representation of a Date object by using the unclass() function.


```r
unclass(x)
```

```
## [1] 0
```

### 9.2 Times in R

Times are represented by the POSIXct or the POSIXlt class. POSIXct is just a very large integer under the hood. It use a useful class when you want to store times in something like a data frame. POSIXlt is a list underneath and it stores a bunch of other useful information like the day of the week, day of the year, month, day of the month. This is useful when you need that kind of information.

There are a number of generic functions that work on dates and times to help you extract pieces of dates and/or times.

- weekdays: give the day of the week
- months: give the month name
- quarters: give the quarter number (“Q1”, “Q2”, “Q3”, or “Q4”)

Times can be coerced from a character string using the as.POSIXlt or as.POSIXct function.


```r
x <- Sys.time()
x
```

```
## [1] "2023-08-01 17:23:38 -03"
```

```r
class(x) ## POSIXct
```

```
## [1] "POSIXct" "POSIXt"
```

The POSIXlt object contains some useful metadata.


```r
p <- as.POSIXlt(x)
names(unclass(p))
```

```
##  [1] "sec"    "min"    "hour"   "mday"   "mon"    "year"   "wday"   "yday"  
##  [9] "isdst"  "zone"   "gmtoff"
```

```r
p$wday
```

```
## [1] 2
```

You can also use the POSIXct format.


```r
x <- Sys.time()
x ## Already in POSIXct format
```

```
## [1] "2023-08-01 17:23:38 -03"
```

```r
unclass(x) # Internal representation
```

```
## [1] 1690921418
```


```r
x$sec # Can`t do this with POSIXct
```


```r
p <- as.POSIXlt(x)
p$sec # Thats better
```

```
## [1] 38.17503
```

Finally, there is the strptime() function in case your dates are written in a different format. strptime() takes a character vector that has dates and times and converts them into to a POSIXlt object.


```r
datestring <- c("January 10, 2012, 10:40", "December 9, 2011 9:10")
x <- strptime(datestring, "%B %d, %Y %H:%M")
x
```

```
## [1] NA NA
```

```r
class(x)
```

```
## [1] "POSIXlt" "POSIXt"
```

The weird-looking symbols that start with the % symbol are the formatting strings for dates and times. I can never remember the formatting strings. Check ?strptime for details. It’s probably not worth memorizing this stuff.

### 9.3 Operations on Dates and Times

You can use mathematical operations on dates and times. Well, really just + and -. You can do comparisons too (i.e. ==, <=)


```r
x <- as.Date("2012-01-01")
y <- 2
x-y
```

```
## [1] "2011-12-30"
```


```r
x <- as.POSIXlt(x)
x-y
```

```
## [1] "2011-12-31 23:59:58 UTC"
```

The nice thing about the date/time classes is that they keep track of all the annoying things about dates and times, like leap years, leap seconds, daylight savings, and time zones.

Here’s an example where a leap year gets involved.


```r
x <- as.Date("2012-03-01")
y <- as.Date("2012-02-28")
x-y
```

```
## Time difference of 2 days
```

Here’s an example where two different time zones are in play (unless you live in GMT timezone, in which case they will be the same!).


```r
## My local time zone
x <- as.POSIXct("2012-10-25 01:00:00")
y <- as.POSIXct("2012-10-25 60:00:00", tz = "GMT")
y-x
```

```
## Time difference of -3 hours
```

## 10. Managing Data Frame with the dplyr package


```r
library(gamair)
library(tidyverse)
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.2     ✔ readr     2.1.4
## ✔ forcats   1.0.0     ✔ stringr   1.5.0
## ✔ ggplot2   3.4.1     ✔ tibble    3.2.1
## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
## ✔ purrr     1.0.1     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
data(chicago)
str(chicago)
```

```
## 'data.frame':	5114 obs. of  7 variables:
##  $ death     : int  130 150 101 135 126 130 129 109 125 153 ...
##  $ pm10median: num  -7.434 NA -0.827 5.566 NA ...
##  $ pm25median: num  NA NA NA NA NA NA NA NA NA NA ...
##  $ o3median  : num  -19.6 -19 -20.2 -19.7 -19.2 ...
##  $ so2median : num  1.928 -0.986 -1.891 6.139 2.278 ...
##  $ time      : num  -2556 -2556 -2554 -2554 -2552 ...
##  $ tmpd      : num  31.5 33 33 29 32 40 34.5 29 26.5 32.5 ...
```

```r
chicago <- chicago %>%
  mutate(city = "chic")
```

### select()

The select() function can be used to select columns of a data frame that you want to focus on. Often you’ll have a large data frame containing “all” of the data, but any given analysis might only use a subset of variables or observations. The select() function allows you to get the few columns you might need.


```r
names(chicago)[1:3]
```

```
## [1] "death"      "pm10median" "pm25median"
```

```r
subset <- select(chicago, death:pm25median)
```

Note that the : normally cannot be used with names or strings, but inside the select() function you can use it to specify a range of variable names.

You can also omit variables using the select() function by using the negative sign. With select() you can do


```r
select(chicago, -(death:pm25median))
```

```
##           o3median    so2median    time  tmpd city
## 1    -19.592337860  1.928042597 -2556.5  31.5 chic
## 2    -19.038613660 -0.985563116 -2555.5  33.0 chic
## 3    -20.217337860 -1.891416086 -2554.5  33.0 chic
## 4    -19.675671200  6.139341274 -2553.5  29.0 chic
## 5    -19.217337860  2.278464871 -2552.5  32.0 chic
## 6    -17.634004530  9.858583914 -2551.5  40.0 chic
## 7    -15.374397970 -5.818992059 -2550.5  34.5 chic
## 8    -12.170527250 -5.107941432 -2549.5  29.0 chic
## 9    -20.092337860  0.182237333 -2548.5  26.5 chic
## 10   -18.580280320 -2.046929333 -2547.5  32.5 chic
## 11    -5.712193917 -1.600998780 -2546.5  29.5 chic
## 12   -15.628860580  2.937930625 -2545.5  34.5 chic
## 13   -17.045527250  3.641800059 -2544.5  34.0 chic
## 14   -18.628860580  6.183466726 -2543.5  37.5 chic
## 15   -18.712193920 -1.141416086 -2542.5  32.5 chic
## 16   -15.003860580 -3.153813391 -2541.5  25.0 chic
## 17   -17.378860580  1.807237333 -2540.5  27.0 chic
## 18   -17.003860580  0.562691986 -2539.5  17.5 chic
## 19    -7.634004530 -0.718613289 -2538.5  23.0 chic
## 20   -18.413613660  0.681604228 -2537.5  20.5 chic
## 21   -16.993323800  3.653464871 -2536.5  22.0 chic
## 22   -12.628860580 -1.268914807 -2535.5  19.5 chic
## 23    -7.925671197 -2.268914807 -2534.5   2.5 chic
## 24   -12.259004530 -1.724749420 -2533.5   2.0 chic
## 25   -15.795527250 -0.804868462 -2532.5   9.5 chic
## 26   -18.759004530  0.722674607 -2531.5  16.0 chic
## 27   -19.837193920  8.972674607 -2530.5  17.5 chic
## 28   -16.717337860 -0.433082753 -2529.5  29.5 chic
## 29   -20.068254520  3.272751860 -2528.5  29.5 chic
## 30   -14.587193920  2.647751860 -2527.5  32.5 chic
## 31   -15.246946990 -0.352248140 -2526.5  27.5 chic
## 32   -17.580280320  1.689418527 -2525.5  41.0 chic
## 33   -19.955280320  2.320131538 -2524.5  36.5 chic
## 34   -15.170527250 -2.454214434 -2523.5  34.0 chic
## 35   -13.833737120  0.106085193 -2522.5  31.5 chic
## 36   -19.167070450  5.373942916 -2521.5  29.5 chic
## 37   -18.823773350 -1.016416086 -2520.5  37.0 chic
## 38   -19.229570450  3.647751860 -2519.5  40.5 chic
## 39     0.499596213 -1.185581473 -2518.5  32.5 chic
## 40   -15.312903790  3.356085193 -2517.5  24.5 chic
## 41   -16.500403790  2.931007941 -2516.5  35.0 chic
## 42   -16.886520390  4.568206406 -2515.5  39.5 chic
## 43   -17.479570450 -1.768914807 -2514.5  34.0 chic
## 44   -14.242251610  1.111798205 -2513.5  34.5 chic
## 45   -13.500403790 -4.316533274 -2512.5  31.5 chic
## 46     3.666262880 -4.652325393 -2511.5  23.5 chic
## 47    -0.250403787 -3.975953564 -2510.5  24.0 chic
## 48     0.499596213 -5.399866607 -2509.5  31.5 chic
## 49    -0.267613932 -0.929868462 -2508.5  31.5 chic
## 50    -8.915258859  3.985395508 -2507.5  34.0 chic
## 51   -16.208737120 10.939418527 -2506.5  32.5 chic
## 52   -20.854570450  8.891800059 -2505.5  38.0 chic
## 53   -13.792070450  0.850133393 -2504.5  35.5 chic
## 54   -11.354570450 -0.016416086 -2503.5  35.5 chic
## 55   -11.542070450 -0.927647970 -2502.5  31.5 chic
## 56    -7.250403787  2.611798205 -2501.5  34.5 chic
## 57    -8.354570453  5.514341274 -2500.5  36.0 chic
## 58   -10.062903790  8.639341274 -2499.5  39.5 chic
## 59   -17.417070450 -2.027325393 -2498.5  40.0 chic
## 60    -9.067070453 -4.518914807 -2497.5  39.0 chic
## 61    -3.625403787  1.439418527 -2496.5  41.0 chic
## 62    -4.708737120 -3.433082753 -2495.5  36.5 chic
## 63   -18.854570450  0.647751860 -2494.5  38.0 chic
## 64   -10.646237120  3.986798205 -2493.5  44.0 chic
## 65   -15.193338570  5.581767786 -2492.5  48.5 chic
## 66     1.145429546  3.519775960 -2491.5  59.5 chic
## 67     7.020429546  0.001799132 -2490.5  54.5 chic
## 68     5.395429546 -5.610658726 -2489.5  31.0 chic
## 69     3.937096213 -5.108199941 -2488.5  25.5 chic
## 70    -4.667070453  2.558466726 -2487.5  28.5 chic
## 71    -3.979570453  1.589263827 -2486.5  33.5 chic
## 72    -1.958737120 -0.886339049 -2485.5  34.0 chic
## 73    -6.479570453 -0.235658726 -2484.5  37.0 chic
## 74    12.457929547 -0.435581473 -2483.5  35.0 chic
## 75    11.934378822 -0.628127477 -2482.5  37.5 chic
## 76     3.374596213  1.072352030 -2481.5  38.5 chic
## 77    -4.128860583  3.278464871 -2480.5  39.5 chic
## 78     7.237691451  5.458829045 -2479.5  41.5 chic
## 79    10.544719676           NA -2478.5  43.0 chic
## 80    12.128053010  6.229358653 -2477.5  43.5 chic
## 81    11.586386343  2.291858653 -2476.5  48.0 chic
## 82     7.016262880  3.178464871 -2475.5  53.5 chic
## 83     1.187096213  8.139341274 -2474.5  57.5 chic
## 84    -9.562903787 -4.310581473 -2473.5  45.0 chic
## 85    -9.320479544 -3.012628207 -2472.5  45.0 chic
## 86    -7.812903787 -1.016416086 -2471.5  43.0 chic
## 87    -4.167070453  0.272751860 -2470.5  46.0 chic
## 88    -3.465681565 -4.143914807 -2469.5  39.5 chic
## 89     5.999596213 -7.303712761 -2468.5  29.0 chic
## 90     0.449328803 -3.058082753 -2467.5  30.0 chic
## 91    -1.775052323 -0.375151480 -2466.5  41.5 chic
## 92    -1.657578269 -3.141416086 -2465.5  29.5 chic
## 93    -7.357940167 -2.266416086 -2464.5  28.0 chic
## 94     2.838123278 -3.310581473 -2463.5  35.5 chic
## 95    12.661232079 -3.185581473 -2462.5  43.5 chic
## 96     7.567289945 -4.310581473 -2461.5  50.0 chic
## 97     0.113255065 -3.268914807 -2460.5  49.5 chic
## 98    -0.460698769 -4.977248140 -2459.5  40.0 chic
## 99    -8.336195947 15.439418527 -2458.5  50.0 chic
## 100   -1.782132779  7.566917247 -2457.5  59.5 chic
## 101    5.727096679 -2.804868462 -2456.5  51.0 chic
## 102    1.582359668 -4.227248140 -2455.5  49.5 chic
## 103   -6.566438978  0.733583914 -2454.5  48.0 chic
## 104  -10.233105640 -2.623300144 -2453.5  52.5 chic
## 105   -6.168531311 -3.152325393 -2452.5  51.5 chic
## 106   -2.710197978 -3.818992059 -2451.5  54.0 chic
## 107   -4.316438978  4.528464871 -2450.5  57.5 chic
## 108    7.956468689  8.481085194 -2449.5  61.5 chic
## 109    8.999026335  2.433466726 -2448.5  64.0 chic
## 110   17.820460345  1.350133393 -2447.5  71.0 chic
## 111   -3.128773500 -2.727325393 -2446.5  56.0 chic
## 112  -15.212106830 -4.636021045 -2445.5  47.0 chic
## 113  -12.920440170  0.403464871 -2444.5  52.0 chic
## 114    4.372634565 -2.727248140 -2443.5  49.0 chic
## 115    0.634088398  0.229358653 -2442.5  51.0 chic
## 116    8.008437566  4.348904000 -2441.5  57.0 chic
## 117   12.041610017  4.266800059 -2440.5  56.5 chic
## 118   -1.297101254 -1.816533274 -2439.5  53.5 chic
## 119   10.207251390 -1.143914807 -2438.5  64.5 chic
## 120   11.067660510 -4.852248140 -2437.5  46.0 chic
## 121   -5.585698769  7.850133393 -2436.5  53.5 chic
## 122    1.081468689 -3.517665446 -2435.5  53.0 chic
## 123    3.662806084 -5.803917201 -2434.5  45.0 chic
## 124    6.058726500 -3.560581473 -2433.5  48.0 chic
## 125   -1.595729101  8.356085194 -2432.5  50.5 chic
## 126    8.149635591  7.606085194 -2431.5  58.0 chic
## 127    3.912447677 -0.710256666 -2430.5  59.5 chic
## 128   -8.909594361  2.941917247 -2429.5  59.0 chic
## 129   19.193018455  5.847674607 -2428.5  69.5 chic
## 130   20.288690906  1.537891999 -2427.5  74.5 chic
## 131   21.580357573  3.817680558 -2426.5  71.0 chic
## 132    2.798400720 -4.352248140 -2425.5  51.5 chic
## 133    4.538219785  5.933466726 -2424.5  59.0 chic
## 134   16.048183553  0.939418527 -2423.5  69.0 chic
## 135   -0.689865435 -2.518914807 -2422.5  56.5 chic
## 136    9.157953133 -1.652325393 -2421.5  62.5 chic
## 137   27.210508169 -0.912030749 -2420.5  70.5 chic
## 138    1.786449708 -2.777325393 -2419.5  66.5 chic
## 139    1.468307016 -2.893914807 -2418.5  67.5 chic
## 140   -6.730776346 -1.685581473 -2417.5  66.5 chic
## 141    8.679711067 -1.308082753 -2416.5  74.0 chic
## 142  -10.505434590 -2.810581473 -2415.5  61.5 chic
## 143  -12.534151810 -4.263201795 -2414.5  51.0 chic
## 144   -3.661876722 -4.352248140 -2413.5  50.5 chic
## 145  -10.460698770 -2.516416086 -2412.5  57.5 chic
## 146    5.492211067 -0.433082753 -2411.5  73.0 chic
## 147    7.023268933 -2.152325393 -2410.5  78.5 chic
## 148    5.978843358 -0.477248140 -2409.5  78.5 chic
## 149    3.941444912 -0.066533274 -2408.5  78.5 chic
## 150   -1.451599280 -0.933082753 -2407.5  76.0 chic
## 151    8.448864171 -2.233199941 -2406.5  76.0 chic
## 152   10.158877734 -2.102248140 -2405.5  74.5 chic
## 153   -6.382788933 -0.141416086 -2404.5  72.0 chic
## 154   -0.845078269 -2.858199941 -2403.5  69.0 chic
## 155   -3.085848810 -0.393914807 -2402.5  63.0 chic
## 156    8.465197356 -1.849749420 -2401.5  70.0 chic
## 157   20.988255065  2.189418527 -2400.5  71.0 chic
## 158   26.152697356 -0.471535129 -2399.5  77.5 chic
## 159   24.978892394  1.141799479 -2398.5  74.5 chic
## 160    8.305836112 -6.691533274 -2397.5  57.5 chic
## 161    3.482515583 -1.066533274 -2396.5  61.0 chic
## 162    3.372473682  2.731085193 -2395.5  72.5 chic
## 163    4.390963511 -3.727248140 -2394.5  80.0 chic
## 164   14.890963511  0.358583914 -2393.5  77.0 chic
## 165   21.558726500  4.106085194 -2392.5  83.0 chic
## 166    8.248239837 -4.185581473 -2391.5  73.5 chic
## 167    8.650993844  2.016800059 -2390.5  75.0 chic
## 168   26.549181522  4.816917247 -2389.5  77.0 chic
## 169   38.570937566  8.139341274 -2388.5  80.5 chic
## 170   23.411796845  2.766800059 -2387.5  77.5 chic
## 171   25.296902101 -1.016416086 -2386.5  77.0 chic
## 172    8.932409655 -3.275474057 -2385.5  76.0 chic
## 173    2.182409654 -3.858199941 -2384.5  67.0 chic
## 174    6.441035257 -1.560581473 -2383.5  72.5 chic
## 175   25.828463511  2.147751860 -2382.5  76.0 chic
## 176   10.516614344 -0.852248140 -2381.5  70.5 chic
## 177   -1.963767921 -3.141416086 -2380.5  69.0 chic
## 178   -1.308555088 -3.268914807 -2379.5  64.5 chic
## 179    7.949619799 -3.231995796 -2378.5  72.0 chic
## 180    4.560430013  1.695131538 -2377.5  72.5 chic
## 181   -4.554539655 -1.610658726 -2376.5  70.5 chic
## 182   10.049757096 -4.268914807 -2375.5  68.5 chic
## 183   13.593277815  0.395587938 -2374.5  71.0 chic
## 184   19.967867221 -1.643914807 -2373.5  77.5 chic
## 185   10.371226500 -3.719247360 -2372.5  72.5 chic
## 186   -0.918085822 -4.049413369 -2371.5  72.5 chic
## 187    7.359891740 -2.795510323 -2370.5  80.0 chic
## 188   10.789151190 -1.967632514 -2369.5  80.0 chic
## 189    8.653793678 -2.602080476 -2368.5  80.5 chic
## 190    1.846435570 -3.032699863 -2367.5  80.5 chic
## 191    0.145930089 -2.901062426 -2366.5  80.0 chic
## 192    5.502057033 -3.808392024 -2365.5  80.0 chic
## 193    5.523748883 -3.829117306 -2364.5  79.5 chic
## 194  -13.170359800 -3.489947810 -2363.5  64.5 chic
## 195   -3.514099280 -2.011913767 -2362.5  67.0 chic
## 196   -0.220078269 -3.076520516 -2361.5  63.0 chic
## 197    9.308616255 -1.596730213 -2360.5  65.5 chic
## 198   19.197780199 -3.748126936 -2359.5  78.0 chic
## 199   23.902567386 -2.631651669 -2358.5  79.5 chic
## 200   22.515134697 -1.898541786 -2357.5  84.5 chic
## 201   16.701669445 -2.224483287 -2356.5  81.5 chic
## 202   33.831914178  1.534157164 -2355.5  81.0 chic
## 203   22.631864023  1.219492402 -2354.5  80.5 chic
## 204   34.755235435  2.077557349 -2353.5  82.0 chic
## 205   10.893763346  2.406643240 -2352.5  83.0 chic
## 206   17.038500358 -0.426477598 -2351.5  84.5 chic
## 207    3.461344226 -1.433853882 -2350.5  78.5 chic
## 208   -9.494376174 -0.555095712 -2349.5  71.0 chic
## 209   -6.131573335 -0.690936514 -2348.5  72.5 chic
## 210    0.891883477 -0.803608414 -2347.5  78.0 chic
## 211   25.548998060 -2.358108947 -2346.5  79.5 chic
## 212   15.076759998 -2.259328187 -2345.5  80.5 chic
## 213   16.970930294 -3.616934723 -2344.5  87.0 chic
## 214   22.993426665 -1.182449387 -2343.5  86.0 chic
## 215    7.499026335  1.636684734 -2342.5  83.0 chic
## 216   -2.050068324 -1.242784435 -2341.5  75.0 chic
## 217   -1.692590345 -3.170799535 -2340.5  72.0 chic
## 218    4.808281010 -1.589522275 -2339.5  72.0 chic
## 219   21.925645653 -0.061596991 -2338.5  79.5 chic
## 220    0.641614344 -2.568733707 -2337.5  73.5 chic
## 221    4.124704793 -1.014752736 -2336.5  72.5 chic
## 222    6.491435562 -3.260654417 -2335.5  71.0 chic
## 223    8.991999300  0.976862066 -2334.5  74.5 chic
## 224   11.509088398  0.482522174 -2333.5  76.0 chic
## 225   11.914151190  2.675010138 -2332.5  75.5 chic
## 226   -0.618004997 -3.621406235 -2331.5  74.0 chic
## 227    9.547290492 -5.122590223 -2330.5  79.5 chic
## 228    8.594909540 -2.247711928 -2329.5  80.0 chic
## 229    3.269505065 -4.018423556 -2328.5  73.5 chic
## 230  -10.669526450 -0.813293404 -2327.5  69.5 chic
## 231   -5.363448168 -2.892331631 -2326.5  70.0 chic
## 232    6.314379590 -0.846403072 -2325.5  70.0 chic
## 233   13.078136839  0.477764480 -2324.5  76.0 chic
## 234    0.508534180 -2.704471000 -2323.5  71.5 chic
## 235   -6.741465820 -2.964401237 -2322.5  63.0 chic
## 236  -12.193946850 -2.751918710 -2321.5  61.5 chic
## 237  -18.060581110 -2.856686994 -2320.5  59.0 chic
## 238  -15.304985140 -3.375585587 -2319.5  62.0 chic
## 239   -9.159932614 -5.187052850 -2318.5  63.5 chic
## 240   -1.787745502 -4.516112280 -2317.5  63.5 chic
## 241   -4.657919224 -2.948315670 -2316.5  65.5 chic
## 242   -1.427966112 -3.531824092 -2315.5  72.0 chic
## 243   -7.838472033 -3.581193840 -2314.5  62.0 chic
## 244   -5.808346970 -2.448064638 -2313.5  64.0 chic
## 245   -1.843747414 -4.120059386 -2312.5  61.5 chic
## 246   -3.448799445 -3.268073137 -2311.5  60.5 chic
## 247    7.924972482  8.733811414 -2310.5  69.0 chic
## 248   21.782092424 -0.850356050 -2309.5  73.0 chic
## 249   15.131864023  0.438939807 -2308.5  75.0 chic
## 250   -1.282132779 -4.636597705 -2307.5  70.5 chic
## 251    0.767059833 -2.358833683 -2306.5  72.0 chic
## 252   -6.047177611 -2.172792600 -2305.5  68.5 chic
## 253    1.110900720 -0.947124223 -2304.5  67.5 chic
## 254   -1.745403018 -0.575471611 -2303.5  69.0 chic
## 255   -8.700818478 -4.242439666 -2302.5  68.0 chic
## 256  -11.924667370 -3.272336088 -2301.5  59.0 chic
## 257   -4.355585822 -1.286468400 -2300.5  61.5 chic
## 258   -8.395901202 -0.209018487 -2299.5  72.0 chic
## 259  -14.229958560 -3.880171006 -2298.5  69.0 chic
## 260  -10.370150940 -4.028980530 -2297.5  67.5 chic
## 261  -16.413820380 -4.123284919 -2296.5  63.5 chic
## 262  -11.639012010 -3.746367055 -2295.5  61.0 chic
## 263  -16.740707270 -3.640537117 -2294.5  57.0 chic
## 264  -18.673240000 -4.316933470 -2293.5  55.5 chic
## 265  -16.463095070 -3.151821356 -2292.5  61.0 chic
## 266   -9.988555465 -2.350540181 -2291.5  63.0 chic
## 267   -6.750973665 -3.017350137 -2290.5  59.5 chic
## 268   -9.229958559  3.414395085 -2289.5  56.5 chic
## 269   -1.625973665  2.023360440 -2288.5  65.0 chic
## 270    3.081468689 -1.265266803 -2287.5  75.5 chic
## 271    5.582359668  1.374485568 -2286.5  69.0 chic
## 272  -10.738555470 -3.808438100 -2285.5  61.5 chic
## 273  -12.400406360 -2.706833847 -2284.5  55.0 chic
## 274   -7.786681966  0.557994541 -2283.5  55.5 chic
## 275   -7.983812118 -3.637175625 -2282.5  47.0 chic
## 276   -8.035645221 -2.168164483 -2281.5  42.5 chic
## 277   -5.583104944  0.508370508 -2280.5  52.0 chic
## 278   -9.334241307  5.177430206 -2279.5  60.0 chic
## 279  -15.870801110 -3.643958491 -2278.5  45.5 chic
## 280  -11.537664320 -4.015360534 -2277.5  39.5 chic
## 281  -13.464165550  0.224588851 -2276.5  41.5 chic
## 282  -12.233374130 -1.092560907 -2275.5  53.5 chic
## 283  -16.337978780 -4.410614044 -2274.5  42.5 chic
## 284   -9.069707849 -3.511182261 -2273.5  39.0 chic
## 285  -11.192621360 -0.665764410 -2272.5  42.5 chic
## 286   -8.387923790  3.358966872 -2271.5  49.5 chic
## 287  -14.484914620  6.421443722 -2270.5  55.5 chic
## 288    0.052744486  6.628072812 -2269.5  63.0 chic
## 289    3.097677885  1.427652465 -2268.5  62.5 chic
## 290  -15.286298840 -4.023949206 -2267.5  48.0 chic
## 291   -7.985547571 -3.160586099 -2266.5  53.5 chic
## 292   -7.653766376 -4.057272593 -2265.5  49.5 chic
## 293   -8.529145082 -2.709994691 -2264.5  45.5 chic
## 294  -17.260106960 -3.691544378 -2263.5  37.0 chic
## 295  -17.930217130 -1.590288215 -2262.5  43.0 chic
## 296  -15.483323520 -1.932350953 -2261.5  39.0 chic
## 297  -17.531664930 -4.432789503 -2260.5  38.5 chic
## 298  -13.390058410 -4.628739986 -2259.5  42.0 chic
## 299  -19.416372910  0.638312108 -2258.5  44.5 chic
## 300  -13.833996780 -2.854401892 -2257.5  44.5 chic
## 301  -19.073129100 -3.217298443 -2256.5  38.5 chic
## 302  -15.659261030  2.097715240 -2255.5  44.5 chic
## 303   -7.016590572  6.155248886 -2254.5  54.5 chic
## 304  -12.370271710  0.633086138 -2253.5  52.0 chic
## 305  -13.684561930 -3.061175196 -2252.5  58.0 chic
## 306  -12.017713320 -1.644299129 -2251.5  64.0 chic
## 307    7.272000180  0.632387088 -2250.5  66.0 chic
## 308   -7.116385483 -0.277447403 -2249.5  56.5 chic
## 309   -5.710478889 -3.229939671 -2248.5  40.5 chic
## 310  -16.488016120  2.056625057 -2247.5  39.5 chic
## 311  -19.973898920 -0.297672932 -2246.5  45.5 chic
## 312   -2.200570232 -1.496812002 -2245.5  49.5 chic
## 313    0.595694274 -2.499035784 -2244.5  35.0 chic
## 314    1.538769598 -1.163829792 -2243.5  34.0 chic
## 315  -17.127722390 -1.150800714 -2242.5  35.0 chic
## 316  -16.547423600  4.311555627 -2241.5  43.5 chic
## 317  -19.618385320  2.260050817 -2240.5  48.5 chic
## 318  -14.425598200  0.356245648 -2239.5  47.0 chic
## 319   -6.359815965  4.204648331 -2238.5  50.5 chic
## 320   -8.458831533  0.706919131 -2237.5  56.0 chic
## 321  -12.633992430 -2.315811114 -2236.5  50.0 chic
## 322  -16.128952170 -2.958212057 -2235.5  38.0 chic
## 323  -15.362563220  2.805492707 -2234.5  37.5 chic
## 324   -1.201928563 -3.436825066 -2233.5  27.5 chic
## 325  -15.109258550 -0.047443707 -2232.5  25.5 chic
## 326  -14.679427970 -1.623842412 -2231.5  42.0 chic
## 327  -14.592825640 -2.002781482 -2230.5  43.5 chic
## 328  -17.605366610 -2.290926016 -2229.5  39.5 chic
## 329  -13.350558920 -3.977053559 -2228.5  40.0 chic
## 330   -2.882495731 -4.026728582 -2227.5  40.0 chic
## 331  -15.298017710 -4.022318183 -2226.5  40.5 chic
## 332  -19.794529530 -0.783570168 -2225.5  47.0 chic
## 333  -21.133246760 -3.726241117 -2224.5  39.0 chic
## 334  -20.140219070 -2.216406063 -2223.5  37.0 chic
## 335  -13.319286750 -3.333786230 -2222.5  32.0 chic
## 336  -16.157610020 -2.506477212 -2221.5  30.0 chic
## 337  -20.422537790 -2.593161986 -2220.5  30.5 chic
## 338  -14.539701300 -1.448296091 -2219.5  27.0 chic
## 339  -12.953538950 -0.551877978 -2218.5  27.0 chic
## 340  -11.836666240  6.481846455 -2217.5  31.5 chic
## 341  -20.998027210 -3.036743217 -2216.5  38.5 chic
## 342  -21.322231330 -3.757165230 -2215.5  46.5 chic
## 343  -17.278809050 -2.745421750 -2214.5  49.5 chic
## 344  -19.467553940 -2.161251671 -2213.5  38.0 chic
## 345  -22.101725710  2.559494207 -2212.5  41.0 chic
## 346  -15.840886680 -2.446657139 -2211.5  35.0 chic
## 347  -13.726288390 -3.785463533 -2210.5  31.5 chic
## 348  -13.721022820 -0.634468237 -2209.5  30.0 chic
## 349  -17.481178370 -4.524857002 -2208.5  31.0 chic
## 350  -14.963124470 -3.323244049 -2207.5  27.5 chic
## 351  -19.456380760 -2.217669765 -2206.5  20.5 chic
## 352  -22.631080220  6.189208539 -2205.5  23.0 chic
## 353  -23.195675660  0.604241219 -2204.5  34.0 chic
## 354  -14.980123710 -0.582611178 -2203.5  34.0 chic
## 355  -20.261221300  1.419019338 -2202.5  33.0 chic
## 356  -24.543929540           NA -2201.5  35.0 chic
## 357  -21.415625500 -0.077783968 -2200.5  35.5 chic
## 358  -22.354230740 -4.117158936 -2199.5  41.0 chic
## 359   -9.735146269 -5.298701249 -2198.5  33.0 chic
## 360   -6.270720989 -2.728053620 -2197.5  27.5 chic
## 361  -15.242368650  1.545643735 -2196.5  29.0 chic
## 362   -6.705472990 -3.718897220 -2195.5  33.5 chic
## 363   -5.556665954 -1.659247950 -2194.5  24.5 chic
## 364  -20.753108970  4.240194822 -2193.5  25.5 chic
## 365  -10.872202970           NA -2192.5  23.5 chic
## 366   -4.920980937 -2.332397396 -2191.5   3.5 chic
## 367  -17.138349980  1.634292483 -2190.5  15.0 chic
## 368  -16.160584100  3.745480970 -2189.5  24.5 chic
## 369   -6.539574728 -1.656946453 -2188.5   5.0 chic
## 370  -11.787862670 -2.208038797 -2187.5  -6.5 chic
## 371  -17.885574650 -1.406166625 -2186.5  -5.5 chic
## 372  -23.097522370  5.793671872 -2185.5   3.0 chic
## 373  -21.086598580  8.161005334 -2184.5   8.5 chic
## 374  -17.713273310 -0.436365268 -2183.5   0.5 chic
## 375  -18.892668450  3.166463954 -2182.5   1.5 chic
## 376  -19.109302190  7.394298613 -2181.5  24.0 chic
## 377   -8.548085631  0.081470478 -2180.5  28.5 chic
## 378  -11.484514180 -2.480412373 -2179.5  12.0 chic
## 379  -22.667321290  3.897670807 -2178.5  11.0 chic
## 380  -21.674186580  3.584586609 -2177.5  28.0 chic
## 381  -11.404552770 -4.026628756 -2176.5  39.0 chic
## 382  -18.920235810 -3.511417749 -2175.5  37.0 chic
## 383  -16.826548310 -0.951488148 -2174.5  33.5 chic
## 384  -12.490236820  3.412729791 -2173.5  35.5 chic
## 385  -18.756814290 -1.368721846 -2172.5  34.0 chic
## 386   -9.201909400 -1.483968816 -2171.5  28.5 chic
## 387  -18.623716800 -2.481050657 -2170.5  27.0 chic
## 388  -17.287831860  0.519171847 -2169.5  21.0 chic
## 389  -15.935420820  0.106465726 -2168.5  24.5 chic
## 390  -17.036386540 -0.304222915 -2167.5  14.5 chic
## 391  -18.199625280 -2.209969448 -2166.5   9.0 chic
## 392  -18.079062290  3.851668703 -2165.5   7.0 chic
## 393  -14.504943730  7.508486884 -2164.5  21.5 chic
## 394  -14.537544810  3.212564030 -2163.5  35.5 chic
## 395   -1.246355408 -2.092219443 -2162.5  50.5 chic
## 396   -5.029183691 -3.636875767 -2161.5  46.0 chic
## 397   -3.398100128 -5.578773794 -2160.5  28.0 chic
## 398    1.211289220 -0.670256692 -2159.5  22.5 chic
## 399   -9.820735573  6.797191799 -2158.5  24.0 chic
## 400  -10.277421940 -1.922239184 -2157.5  16.5 chic
## 401   -6.646722718 -1.646947183 -2156.5   4.0 chic
## 402   -5.425992035 -1.308548482 -2155.5   3.0 chic
## 403   -7.302025748  2.961833509 -2154.5  16.5 chic
## 404  -13.257663810  0.575543588 -2153.5  14.5 chic
## 405  -12.466067990  0.618401099 -2152.5  17.5 chic
## 406   -3.218200482 -0.786565184 -2151.5  16.0 chic
## 407    3.701450574 -3.582733064 -2150.5  16.0 chic
## 408   -6.981171097 -0.172448827 -2149.5   9.0 chic
## 409  -13.518144390  5.873453007 -2148.5   7.0 chic
## 410  -16.105184260  9.868173255 -2147.5  27.5 chic
## 411   -6.456377709  0.223951305 -2146.5  26.0 chic
## 412  -18.407924920  2.940923654 -2145.5  26.5 chic
## 413  -14.514817840 -0.838001398 -2144.5  31.0 chic
## 414  -16.492075600  1.200659707 -2143.5  31.5 chic
## 415  -15.585969080  7.137949588 -2142.5  32.0 chic
## 416   -1.334688719 -2.986723905 -2141.5  21.5 chic
## 417   -7.504295059 -1.986723905 -2140.5  16.5 chic
## 418   -7.183716429 -0.461817015 -2139.5  40.0 chic
## 419   -1.203170314 -2.463446438 -2138.5  27.0 chic
## 420   -6.337072004           NA -2137.5  22.0 chic
## 421  -11.106585320 -2.672372119 -2136.5  20.5 chic
## 422  -11.341663370  2.890482994 -2135.5  39.5 chic
## 423    8.835448657 -2.752464240 -2134.5  32.0 chic
## 424   -8.343614703  3.739857183 -2133.5  34.5 chic
## 425   -8.359709946 -2.373263287 -2132.5  39.5 chic
## 426   -2.708353994 -0.130791791 -2131.5  41.0 chic
## 427   -0.400990810 -1.767745039 -2130.5  35.5 chic
## 428    8.692357668 -2.558179755 -2129.5  27.0 chic
## 429    5.721807303 -2.960883671 -2128.5  28.5 chic
## 430  -14.733938340  2.143922221 -2127.5  31.0 chic
## 431   -7.043866325  1.981703120 -2126.5  36.0 chic
## 432  -13.855901600  1.640906943 -2125.5  42.0 chic
## 433  -12.157806650  2.650665660 -2124.5  48.5 chic
## 434   -6.232279015 -2.435501645 -2123.5  34.0 chic
## 435   -3.514762945 -1.361395287 -2122.5  38.0 chic
## 436  -13.346510740  0.600978108 -2121.5  46.5 chic
## 437   -2.680384485 -3.115147139 -2120.5  43.0 chic
## 438   -1.273839936 -3.301176655 -2119.5  26.5 chic
## 439    0.103769215 -3.382052123 -2118.5  24.0 chic
## 440   -3.524906090 -3.299956579 -2117.5  23.5 chic
## 441   -4.414201822 -2.159572071 -2116.5  32.5 chic
## 442  -14.346585750  0.240985870 -2115.5  34.0 chic
## 443   -7.809105003 -1.660430858 -2114.5  34.5 chic
## 444   -3.160309748 -1.974632080 -2113.5  32.0 chic
## 445    8.320287470 -5.428541654 -2112.5  28.5 chic
## 446   11.591460829 -2.759846964 -2111.5  30.0 chic
## 447   -7.654876560  0.210197806 -2110.5  46.5 chic
## 448    5.773725316  0.706929903 -2109.5  56.0 chic
## 449   -4.640615840 -2.416205496 -2108.5  56.5 chic
## 450   -3.663002223 -2.524596281 -2107.5  53.0 chic
## 451   -5.526658340 -2.096435894 -2106.5  39.0 chic
## 452   -5.816503646 -1.813171979 -2105.5  40.0 chic
## 453  -15.602646000  1.899433238 -2104.5  48.5 chic
## 454  -13.647924640 -3.094463958 -2103.5  45.5 chic
## 455   -9.142786682 -2.360778512 -2102.5  40.5 chic
## 456   -8.612597110 -0.279120856 -2101.5  42.0 chic
## 457    8.820876054           NA -2100.5  42.0 chic
## 458  -17.140274230 -2.685609525 -2099.5  54.0 chic
## 459   -3.197073244 -3.164756000 -2098.5  53.5 chic
## 460    1.398247639  0.046748087 -2097.5  57.5 chic
## 461    4.656358129  2.442405300 -2096.5  69.5 chic
## 462    3.180920857 -4.537330982 -2095.5  50.0 chic
## 463   -4.427552896 -2.642365118 -2094.5  50.0 chic
## 464  -12.435129920  1.015385855 -2093.5  51.5 chic
## 465   -8.274971301  2.971764304 -2092.5  52.5 chic
## 466   -9.586607764 -2.427727889 -2091.5  43.0 chic
## 467    2.128432656 -3.249162909 -2090.5  46.0 chic
## 468   -0.304115896 -0.960591739 -2089.5  47.5 chic
## 469    1.585932182  4.336943472 -2088.5  53.0 chic
## 470   -0.539325607 -2.607657467 -2087.5  43.0 chic
## 471   -2.135040203 -2.969981410 -2086.5  41.5 chic
## 472   -4.602894317 -0.113476052 -2085.5  47.5 chic
## 473   12.320087791 -0.626436809 -2084.5  59.0 chic
## 474    0.709933652 -1.209511190 -2083.5  42.5 chic
## 475   -4.801280519  3.496450515 -2082.5  40.5 chic
## 476   -4.863029311  0.874786958 -2081.5  45.5 chic
## 477   -8.384428883  1.031192299 -2080.5  37.0 chic
## 478   -0.200696838  4.361620471 -2079.5  45.5 chic
## 479   -3.330506107 -1.504106800 -2078.5  44.0 chic
## 480   -2.806946917 -0.632049361 -2077.5  45.0 chic
## 481   -3.454882531 -0.212214519 -2076.5  54.0 chic
## 482   -4.276610080 -1.910155872 -2075.5  48.5 chic
## 483   -1.424818695 -2.075054386 -2074.5  39.5 chic
## 484    0.565085533 -0.104653117 -2073.5  48.5 chic
## 485   -4.053628764  0.469231721 -2072.5  49.0 chic
## 486   -3.534522940 -0.252795093 -2071.5  54.0 chic
## 487    6.442308288 -2.189699421 -2070.5  53.0 chic
## 488    6.451898191 -1.571225355 -2069.5  57.0 chic
## 489    9.618257492 -0.933283887 -2068.5  56.5 chic
## 490    6.308830035  0.140174766 -2067.5  52.5 chic
## 491   -1.089435466 -0.249457147 -2066.5  54.5 chic
## 492   -1.275899017 -0.805587825 -2065.5  61.5 chic
## 493    5.573051278  2.783184856 -2064.5  62.0 chic
## 494   24.350300735 -1.972614120 -2063.5  67.5 chic
## 495   -0.438168501 -4.125168451 -2062.5  56.0 chic
## 496   -6.168073208 -1.881171276 -2061.5  57.0 chic
## 497   -5.376406542  5.254376097 -2060.5  60.5 chic
## 498    7.081824338  5.303268460 -2059.5  67.0 chic
## 499    3.623593458 -2.827384227 -2058.5  58.0 chic
## 500    3.760822261  1.731989950 -2057.5  60.0 chic
## 501    9.940291553  2.794736017 -2056.5  71.0 chic
## 502   -3.483577783 -1.427972266 -2055.5  55.5 chic
## 503    1.221362240 -3.759124665 -2054.5  52.0 chic
## 504    2.955915719 -2.985442891 -2053.5  55.0 chic
## 505    2.922772858 -2.973279891 -2052.5  60.0 chic
## 506    0.665260125 -1.923413700 -2051.5  63.0 chic
## 507   11.665260125 -2.802533286 -2050.5  64.5 chic
## 508   16.790326247 -2.792881685 -2049.5  66.0 chic
## 509   -6.084739875 -3.210489972 -2048.5  53.5 chic
## 510    6.706926792 -4.566723676 -2047.5  53.0 chic
## 511    0.895189648 -1.201982415 -2046.5  48.5 chic
## 512    1.064776662  1.200829342 -2045.5  60.5 chic
## 513   15.247639299 -0.060367400 -2044.5  69.5 chic
## 514   14.032581493  0.149998220 -2043.5  73.0 chic
## 515   17.940129924 -1.196130273 -2042.5  74.0 chic
## 516   25.002090539 -0.528757392 -2041.5  75.5 chic
## 517   22.974755550  3.037006332 -2040.5  76.0 chic
## 518   22.026226138  7.437427197 -2039.5  76.5 chic
## 519    6.849755550 -0.321179082 -2038.5  63.0 chic
## 520    2.980190333 -3.632535558 -2037.5  58.5 chic
## 521   -1.264079640  1.482449585 -2036.5  62.5 chic
## 522   18.074248160  2.383889284 -2035.5  73.0 chic
## 523   21.080972632 -0.222117879 -2034.5  78.0 chic
## 524   25.081926792  3.666009567 -2033.5  79.0 chic
## 525    2.901144903 -0.252047418 -2032.5  63.0 chic
## 526    5.641422217 -3.209973069 -2031.5  56.0 chic
## 527    2.415260125 -3.337680225 -2030.5  56.0 chic
## 528    7.318384024  0.072492486 -2029.5  67.0 chic
## 529   21.069689042  1.997587085 -2028.5  72.0 chic
## 530   23.439985557  0.239069414 -2027.5  78.0 chic
## 531   26.923051278  2.820077338 -2026.5  82.0 chic
## 532   19.128668522  0.236119210 -2025.5  81.5 chic
## 533    2.256183974  0.339839830 -2024.5  71.0 chic
## 534    8.034397474  0.263268192 -2023.5  68.5 chic
## 535   13.050199712 -3.843000112 -2022.5  74.5 chic
## 536   32.081926792 -2.396851381 -2021.5  82.0 chic
## 537   32.288423915  0.753515632 -2020.5  88.5 chic
## 538   25.451251265  1.973972317 -2019.5  86.5 chic
## 539   26.604765731 -2.783411931 -2018.5  82.5 chic
## 540    4.373593458 -3.870126645 -2017.5  70.0 chic
## 541    8.336327753 -1.222116847 -2016.5  77.5 chic
## 542   33.116342843 -0.519769429 -2015.5  84.5 chic
## 543   -0.244871479 -7.110020452 -2014.5  63.5 chic
## 544   -6.931828373 -3.803490166 -2013.5  64.0 chic
## 545    8.448528358  1.139854984 -2012.5  72.5 chic
## 546    2.248593458 -6.605558563 -2011.5  63.0 chic
## 547   -1.651079191 -5.297347240 -2010.5  61.5 chic
## 548   -3.296912524 -1.583579124 -2009.5  66.0 chic
## 549   -2.914769899 -3.571023445 -2008.5  65.0 chic
## 550   12.369472985  2.315249264 -2007.5  69.5 chic
## 551   19.863143202  0.138228984 -2006.5  73.0 chic
## 552   29.050280361  2.955979670 -2005.5  79.5 chic
## 553   26.623505185  6.480528820 -2004.5  82.5 chic
## 554   24.095510390  2.879074442 -2003.5  85.0 chic
## 555   18.237455689 -0.268379498 -2002.5  83.0 chic
## 556   22.545449368 -0.744215380 -2001.5  82.5 chic
## 557   14.494921038 -1.780630373 -2000.5  75.5 chic
## 558    8.215481492 -1.520851568 -1999.5  76.5 chic
## 559    1.069219762 -5.261997032 -1998.5  71.5 chic
## 560    5.522136140  2.238456329 -1997.5  80.0 chic
## 561   20.547338294 -1.122990281 -1996.5  87.0 chic
## 562   15.464491320 -0.866067359 -1995.5  86.5 chic
## 563   10.421417534  0.533017419 -1994.5  85.0 chic
## 564    4.128355267 -4.364149902 -1993.5  78.0 chic
## 565   -1.588579191 -4.132874273 -1992.5  71.5 chic
## 566   -0.029285220 -3.315316250 -1991.5  75.0 chic
## 567    1.893225478  1.662122303 -1990.5  74.0 chic
## 568   -3.253242752 -0.498526439 -1989.5  71.5 chic
## 569   -1.030638309 -3.871617330 -1988.5  71.0 chic
## 570    1.475923915 -4.160496332 -1987.5  72.0 chic
## 571    7.728276071 -0.288055854 -1986.5  75.5 chic
## 572   12.565876900 -2.063580473 -1985.5  75.5 chic
## 573   -3.212604679 -3.736600477 -1984.5  73.5 chic
## 574    6.193171627 -0.018290618 -1983.5  76.0 chic
## 575   22.632030758  0.979823827 -1982.5  84.0 chic
## 576   30.846328046           NA -1981.5  83.0 chic
## 577   19.516620403           NA -1980.5  82.5 chic
## 578   14.278808530  6.089298454 -1979.5  79.0 chic
## 579   16.554114978  0.735317921 -1978.5  89.0 chic
## 580   23.015110396  1.313466373 -1977.5  89.5 chic
## 581   13.213075946  2.064118676 -1976.5  88.0 chic
## 582   15.433712158  4.894529296 -1975.5  87.0 chic
## 583   10.521695443 -1.843279948 -1974.5  76.5 chic
## 584   11.128668522 -2.218762984 -1973.5  76.5 chic
## 585   15.107835188  0.371468802 -1972.5  78.0 chic
## 586   26.623116378  0.427770913 -1971.5  84.0 chic
## 587    3.444530758 -0.411900445 -1970.5  79.0 chic
## 588    4.738652876 -1.952433407 -1969.5  76.5 chic
## 589    3.282581493 -1.398225736 -1968.5  82.0 chic
## 590   -0.185174665  2.369461743 -1967.5  83.5 chic
## 591   10.200957646 -1.267641139 -1966.5  83.5 chic
## 592   13.602283045 -0.041550498 -1965.5  83.0 chic
## 593   10.877060770  0.852297656 -1964.5  84.0 chic
## 594    7.958632359  2.049240866 -1963.5  86.0 chic
## 595   10.891110427 -0.380159289 -1962.5  88.0 chic
## 596    7.775484749 -1.165973493 -1961.5  80.5 chic
## 597   -9.295503585 -3.255336544 -1960.5  71.5 chic
## 598   11.767590581  0.140217626 -1959.5  73.0 chic
## 599   -0.204664812 -3.086794945 -1958.5  70.5 chic
## 600   -1.337053165 -0.271584768 -1957.5  70.0 chic
## 601    6.043588978 -1.452282595 -1956.5  75.0 chic
## 602   -4.256165660 -3.248659967 -1955.5  71.0 chic
## 603   -4.700062890 -3.191460362 -1954.5  69.5 chic
## 604   -9.651060026 -0.778358451 -1953.5  66.0 chic
## 605  -17.932317560           NA -1952.5  65.0 chic
## 606   -7.082684869           NA -1951.5  64.0 chic
## 607   -5.336269113 -3.606292749 -1950.5  60.0 chic
## 608   -7.265641633  1.121020518 -1949.5  63.0 chic
## 609    4.706576342 -1.324047968 -1948.5  70.5 chic
## 610   10.222646467  3.896643948 -1947.5  72.0 chic
## 611    8.388751265  4.072578252 -1946.5  73.0 chic
## 612    6.412194683 -2.389720517 -1945.5  72.0 chic
## 613    6.907581493 -2.364063817 -1944.5  63.0 chic
## 614   -1.804961988 -6.295027604 -1943.5  54.0 chic
## 615  -12.534033610 -0.245708382 -1942.5  55.0 chic
## 616    0.602667571  2.391822396 -1941.5  60.5 chic
## 617    9.186305771 -1.842040802 -1940.5  70.0 chic
## 618   -8.922389604  0.113794887 -1939.5  66.0 chic
## 619   -6.146455825 10.269703122 -1938.5  67.5 chic
## 620    5.299206752 -0.470670669 -1937.5  71.0 chic
## 621   -7.146628427 -1.623714947 -1936.5  75.5 chic
## 622  -11.761881400 -1.013699749 -1935.5  63.0 chic
## 623   -3.437521183 -1.398941518 -1934.5  66.0 chic
## 624   -6.389550088  0.045668508 -1933.5  64.5 chic
## 625  -10.930877820  3.336156763 -1932.5  65.5 chic
## 626   -0.221977625 -0.397931156 -1931.5  78.0 chic
## 627   -6.430469242 -2.658501357 -1930.5  76.5 chic
## 628  -12.093886800           NA -1929.5  67.0 chic
## 629  -12.654147810 -3.174661190 -1928.5  60.0 chic
## 630  -17.954443320  6.028045363 -1927.5  58.5 chic
## 631   -9.581296078 -1.731176736 -1926.5  70.0 chic
## 632   -8.470276167 -2.349609122 -1925.5  60.0 chic
## 633  -12.306960270 -2.673207633 -1924.5  56.5 chic
## 634   -2.466774084 -0.567881303 -1923.5  61.5 chic
## 635    9.196035273  1.271569800 -1922.5  64.5 chic
## 636    1.260300077  2.238066369 -1921.5  71.0 chic
## 637   -9.674482772  0.068531786 -1920.5  61.5 chic
## 638   -8.973446802 -0.234384723 -1919.5  67.5 chic
## 639  -13.255864880 -2.502970729 -1918.5  68.5 chic
## 640  -16.344076350 -3.209151406 -1917.5  62.0 chic
## 641   -7.603795875 -6.640937165 -1916.5  56.5 chic
## 642  -17.627549100 -1.909403374 -1915.5  53.5 chic
## 643  -12.969339570 -3.345875805 -1914.5  43.0 chic
## 644  -15.957712270 -4.406819431 -1913.5  43.0 chic
## 645  -17.588930920 -1.366827616 -1912.5  43.5 chic
## 646  -14.821250350  0.905346767 -1911.5  47.5 chic
## 647  -14.720125470 -2.118663250 -1910.5  52.0 chic
## 648  -14.902315060 -2.403495594 -1909.5  54.5 chic
## 649   -4.873112401 -3.112894268 -1908.5  58.0 chic
## 650  -11.982442880 -3.283703927 -1907.5  42.5 chic
## 651  -12.813209260 -2.848856131 -1906.5  39.0 chic
## 652  -16.553017490 -0.307699463 -1905.5  39.5 chic
## 653   -9.061213002  3.389980462 -1904.5  58.0 chic
## 654    9.346861499 -2.615938099 -1903.5  66.5 chic
## 655    0.263924991 -2.522902271 -1902.5  62.5 chic
## 656  -20.685280980 -2.986105751 -1901.5  53.0 chic
## 657  -14.655763230 -2.842855713 -1900.5  49.5 chic
## 658  -13.549088130 -2.349333479 -1899.5  43.5 chic
## 659  -21.194108390 -1.131799841 -1898.5  42.0 chic
## 660  -18.926965980 -1.686453338 -1897.5  46.5 chic
## 661  -16.299777710 -2.117901868 -1896.5  42.0 chic
## 662  -15.862068480 -3.010942834 -1895.5  43.5 chic
## 663  -20.284506530 -2.553345461 -1894.5  33.5 chic
## 664  -19.010600840 -3.343675678 -1893.5  35.0 chic
## 665  -20.889285740 -1.291329635 -1892.5  36.5 chic
## 666  -17.131910730  1.933155075 -1891.5  45.0 chic
## 667  -13.857190840 -3.151338288 -1890.5  37.0 chic
## 668  -15.578322060           NA -1889.5  33.5 chic
## 669  -16.193632470 -2.511241037 -1888.5  33.0 chic
## 670  -17.879546040  2.734737470 -1887.5  39.0 chic
## 671  -14.607514920 -1.733483310 -1886.5  40.0 chic
## 672  -14.192584150  1.950368216 -1885.5  43.5 chic
## 673  -17.716551800  0.740767784 -1884.5  50.5 chic
## 674  -19.110164590 -1.677607033 -1883.5  52.5 chic
## 675  -18.229787800 -4.061954999 -1882.5  41.5 chic
## 676   -6.858111634 -3.387207703 -1881.5  33.5 chic
## 677  -15.358436790 -0.769609479 -1880.5  39.0 chic
## 678  -16.584380730 -0.022624370 -1879.5  42.0 chic
## 679  -19.931187480 -1.853945393 -1878.5  43.5 chic
## 680  -13.867915870 -2.731786302 -1877.5  42.5 chic
## 681  -12.063666110 -3.594414384 -1876.5  39.5 chic
## 682  -17.432137150 -3.596777761 -1875.5  41.5 chic
## 683  -13.191040280 -2.350007632 -1874.5  45.0 chic
## 684  -14.681180350 -0.334674560 -1873.5  47.5 chic
## 685  -18.669672760  0.922072140 -1872.5  56.0 chic
## 686  -11.947709280 -3.328969067 -1871.5  48.0 chic
## 687  -13.014270010 -2.230129105 -1870.5  38.5 chic
## 688  -17.697669350  5.977849115 -1869.5  43.5 chic
## 689  -19.435540120 -1.728353537 -1868.5  44.0 chic
## 690  -14.165834520 -3.748595014 -1867.5  36.5 chic
## 691  -15.339742400 -0.419335731 -1866.5  33.0 chic
## 692  -16.190882000  3.037087805 -1865.5  34.5 chic
## 693  -18.655744300  6.838052713 -1864.5  41.5 chic
## 694  -15.476850660 11.516141924 -1863.5  46.5 chic
## 695  -10.814932330  6.188977038 -1862.5  49.0 chic
## 696  -10.279082380 -3.521131864 -1861.5  54.0 chic
## 697   -5.155506882 -3.488934894 -1860.5  37.0 chic
## 698  -12.622505930 -2.711770494 -1859.5  28.0 chic
## 699  -16.482445440 -0.224575951 -1858.5  33.5 chic
## 700  -16.115620840 -3.275721752 -1857.5  30.5 chic
## 701  -14.088035840 -3.093989435 -1856.5  23.5 chic
## 702  -17.550789070  3.846749095 -1855.5  34.0 chic
## 703  -10.503491490 -0.735014232 -1854.5  43.0 chic
## 704  -10.843591210 -2.499683177 -1853.5  32.5 chic
## 705  -15.911514470  1.873289582 -1852.5  38.5 chic
## 706  -11.957003370  2.867161525 -1851.5  42.0 chic
## 707  -11.387607330 -2.175735323 -1850.5  35.0 chic
## 708  -11.457882510 -2.252500252 -1849.5  27.0 chic
## 709  -13.459569370 -0.665885950 -1848.5  21.5 chic
## 710  -11.926481330 -1.659720973 -1847.5  18.5 chic
## 711  -13.594463210 -2.446179306 -1846.5  11.0 chic
## 712  -16.030082190  2.203651120 -1845.5  18.5 chic
## 713  -14.755114330 -0.351490606 -1844.5  34.5 chic
## 714  -13.427869050 -0.049095034 -1843.5  37.5 chic
## 715   -6.943469390 -3.247025378 -1842.5  18.0 chic
## 716  -15.003723750 -0.136715166 -1841.5  10.5 chic
## 717  -11.936371770 -2.263135620 -1840.5  16.0 chic
## 718  -16.049969170  1.074227564 -1839.5  22.0 chic
## 719  -12.758709500  1.084865310 -1838.5  39.0 chic
## 720   -6.582190036 -2.454863373 -1837.5  44.5 chic
## 721   -7.884924864 -2.025839183 -1836.5  33.0 chic
## 722  -14.687118740  0.941207739 -1835.5  36.0 chic
## 723   -8.021409632 -1.215471934 -1834.5  41.0 chic
## 724  -13.526449950  0.576627518 -1833.5  33.5 chic
## 725   -8.437237789 -3.029574498 -1832.5  23.0 chic
## 726   -9.995928266 -3.536419454 -1831.5  27.0 chic
## 727  -14.400843230 -0.874812593 -1830.5  27.5 chic
## 728   -5.141900140 -2.577922755 -1829.5  14.5 chic
## 729  -11.861363280  6.498833670 -1828.5  13.5 chic
## 730  -21.053144910           NA -1827.5  24.5 chic
## 731  -17.179890850  6.722161755 -1826.5  26.0 chic
## 732  -15.096997480  6.875739524 -1825.5  26.5 chic
## 733   -7.385486089 -1.609814189 -1824.5  22.0 chic
## 734  -11.041676560 -1.436625729 -1823.5  23.0 chic
## 735  -13.459771800  1.640041447 -1822.5  19.0 chic
## 736  -17.493700370  3.934575601 -1821.5  29.5 chic
## 737  -17.559569940 -1.075803910 -1820.5  35.0 chic
## 738  -16.850284230 -2.355042055 -1819.5  40.0 chic
## 739    0.168287203 -2.688618455 -1818.5  15.0 chic
## 740   -4.678379464 -0.717203327 -1817.5  16.5 chic
## 741  -14.194093750  2.371596478 -1816.5  31.5 chic
## 742  -11.178236140  4.694351970 -1815.5  34.5 chic
## 743  -11.550790830 -0.816563558 -1814.5  31.5 chic
## 744  -11.645791470 -0.114819621 -1813.5  26.0 chic
## 745  -16.803291470 -0.474327091 -1812.5  26.5 chic
## 746   -9.892458138 -1.100632884 -1811.5  29.5 chic
## 747  -12.774481950  2.222728939 -1810.5  31.5 chic
## 748  -11.424362900  0.090706856 -1809.5  37.0 chic
## 749   -8.204438498 -0.306401571 -1808.5  37.5 chic
## 750   -7.568662636 -0.186472204 -1807.5  38.0 chic
## 751   -1.969005758 -2.806547962 -1806.5  28.5 chic
## 752  -10.363648610 -3.210474900 -1805.5  26.5 chic
## 753  -10.358053380  3.268669110 -1804.5  40.0 chic
## 754  -13.330196230  3.914033458 -1803.5  41.5 chic
## 755  -12.525242040  0.041028746 -1802.5  38.0 chic
## 756  -14.762836380  0.149160667 -1801.5  42.5 chic
## 757  -13.372031780 -2.050981784 -1800.5  34.0 chic
## 758  -11.715231020  1.790779850 -1799.5  37.0 chic
## 759  -11.928678300  0.795635589 -1798.5  42.0 chic
## 760  -14.436195750 -2.359901098 -1797.5  40.0 chic
## 761  -11.426200150 -1.194624075 -1796.5  40.5 chic
## 762   -8.175451851  1.359830988 -1795.5  52.5 chic
## 763    1.047902918 -3.329893196 -1794.5  40.5 chic
## 764   -1.449618518 -4.496805014 -1793.5  25.5 chic
## 765   -7.316318191 -2.688625706 -1792.5  14.5 chic
## 766  -12.642054840 -2.709599158 -1791.5   4.5 chic
## 767  -11.326447100  1.766659033 -1790.5   9.0 chic
## 768   -7.347683710  3.110624254 -1789.5   2.0 chic
## 769   -5.781017043  5.862329903 -1788.5  12.0 chic
## 770   -0.656525794  0.583250260 -1787.5  11.5 chic
## 771   -1.656857531 -0.902632218 -1786.5   7.0 chic
## 772   -3.135858028  3.481411342 -1785.5  15.0 chic
## 773   -2.561944342  5.205537115 -1784.5  24.0 chic
## 774   -3.029946184 -0.041743322 -1783.5  26.5 chic
## 775  -10.894998580  0.088564641 -1782.5  32.0 chic
## 776    1.732375591 -2.536130377 -1781.5  27.5 chic
## 777   -8.584635331  0.517994333 -1780.5  27.5 chic
## 778   -5.811783954  0.048911125 -1779.5  17.5 chic
## 779   -1.264507405 -2.839652796 -1778.5  17.5 chic
## 780   -2.114430530 -2.450970020 -1777.5  22.0 chic
## 781   -6.854231555  2.148467433 -1776.5  28.0 chic
## 782   -0.547776356 -2.064685297 -1775.5  27.0 chic
## 783    1.504308961 -1.049973217 -1774.5  26.0 chic
## 784    3.908420225 -0.772721885 -1773.5  17.0 chic
## 785   -4.165653849 -2.260052133 -1772.5  13.5 chic
## 786  -13.300939660 -3.020736421 -1771.5  13.0 chic
## 787  -11.082822720           NA -1770.5  27.5 chic
## 788    4.952513381  1.415033537 -1769.5  28.5 chic
## 789   -3.499957067 -3.195975775 -1768.5  18.5 chic
## 790   -0.352218972 -0.855533437 -1767.5  23.0 chic
## 791   -4.806567711 -2.408904077 -1766.5  17.0 chic
## 792   -0.936610957 -0.636182170 -1765.5  21.5 chic
## 793   -9.038503714  7.014890851 -1764.5  32.0 chic
## 794  -11.551385640 -0.465030139 -1763.5  34.5 chic
## 795    7.004694300 -3.489412733 -1762.5  21.5 chic
## 796   12.226954889 -3.199158496 -1761.5  26.0 chic
## 797   11.234896878 -3.018324069 -1760.5  23.0 chic
## 798   -8.620881207  8.991707144 -1759.5  26.5 chic
## 799   -8.998078140 10.643949258 -1758.5  34.0 chic
## 800  -12.792036820           NA -1757.5  42.5 chic
## 801  -24.779375680           NA -1756.5  46.0 chic
## 802   10.015566140           NA -1755.5  35.0 chic
## 803   -5.394190290  3.879593482 -1754.5  42.5 chic
## 804   -9.271950900  1.374881462 -1753.5  47.0 chic
## 805   -3.047987060 -2.752224746 -1752.5  33.5 chic
## 806   -4.899358832 -0.759669002 -1751.5  35.5 chic
## 807    9.499130309 -3.520010252 -1750.5  32.0 chic
## 808    2.816653824 -4.748412553 -1749.5  24.5 chic
## 809   -1.689076115 -3.905629092 -1748.5  26.0 chic
## 810    4.723430369  0.296617737 -1747.5  32.5 chic
## 811    6.912824309 -1.783173816 -1746.5  28.5 chic
## 812   -5.629040205  2.921152460 -1745.5  32.0 chic
## 813   -4.894353596  1.769690454 -1744.5  41.5 chic
## 814   -6.781482536  9.563288118 -1743.5  48.5 chic
## 815    5.331777133  2.138832936 -1742.5  49.0 chic
## 816   16.366767925  0.798750024 -1741.5  55.5 chic
## 817   -3.514529009 -2.201767366 -1740.5  66.5 chic
## 818   -8.701830754 -1.909018958 -1739.5  60.5 chic
## 819    4.968375563 -2.867482308 -1738.5  44.0 chic
## 820   -5.205772647 -3.294671138 -1737.5  42.0 chic
## 821    1.686251501 -2.331070522 -1736.5  35.5 chic
## 822   -2.741504049 -2.321900431 -1735.5  36.5 chic
## 823   -3.813869727 -1.737036966 -1734.5  46.5 chic
## 824  -12.192684630 -3.033942419 -1733.5  47.0 chic
## 825   -6.182330259 -0.899079608 -1732.5  51.0 chic
## 826   -3.647758793 -2.568385287 -1731.5  41.0 chic
## 827   -4.271833570 -2.212182043 -1730.5  41.5 chic
## 828   -2.130312383 -2.262716029 -1729.5  40.0 chic
## 829   -2.616791632 -0.194827711 -1728.5  38.0 chic
## 830    3.347314709 -3.091626171 -1727.5  29.5 chic
## 831   -6.117523222 -2.878467037 -1726.5  28.5 chic
## 832   -1.917440680 -2.212573844 -1725.5  37.0 chic
## 833    4.327633486 -0.604734808 -1724.5  43.0 chic
## 834   -2.165470370 -0.326019265 -1723.5  41.0 chic
## 835    1.320927602  1.660983022 -1722.5  52.0 chic
## 836    1.276014636 -0.951540519 -1721.5  48.5 chic
## 837    3.888295203 -0.189007109 -1720.5  57.0 chic
## 838    6.460345273 -1.948362552 -1719.5  48.5 chic
## 839   -6.917241418 -1.849361251 -1718.5  39.5 chic
## 840   -4.792858883  1.936374327 -1717.5  43.5 chic
## 841    2.173765369  0.194795708 -1716.5  54.0 chic
## 842    9.840829734 -1.589593963 -1715.5  53.0 chic
## 843    7.060003915 -0.887171701 -1714.5  52.0 chic
## 844   10.429010899  1.328838867 -1713.5  50.5 chic
## 845    4.685873793  6.657437526 -1712.5  55.5 chic
## 846   11.771855760  5.997583080 -1711.5  62.5 chic
## 847   21.044834179  0.921326415 -1710.5  58.0 chic
## 848   14.372871449 -0.512995619 -1709.5  54.0 chic
## 849  -10.420092600 -2.531195767 -1708.5  49.5 chic
## 850   -4.264888905 -0.513422697 -1707.5  55.5 chic
## 851   12.287356278 -2.527938877 -1706.5  50.0 chic
## 852    8.335934477 -3.443626990 -1705.5  47.5 chic
## 853    3.352468299 -2.735013515 -1704.5  45.0 chic
## 854    1.680929871 -1.314320142 -1703.5  53.5 chic
## 855    1.862788514 -0.052227546 -1702.5  55.5 chic
## 856    8.358746473 -2.870579143 -1701.5  50.5 chic
## 857   -2.183063395 -2.933430267 -1700.5  35.5 chic
## 858   -4.940870870 -2.260542579 -1699.5  43.0 chic
## 859    2.071261620 -3.309543411 -1698.5  50.5 chic
## 860    7.692079806 -3.138735958 -1697.5  50.5 chic
## 861    9.612928287 -4.452966677 -1696.5  49.5 chic
## 862    8.910845075 -4.123477590 -1695.5  48.0 chic
## 863    4.237928287 -3.201296955 -1694.5  51.5 chic
## 864    6.816127954 -3.504237767 -1693.5  52.0 chic
## 865    9.029594954 -2.042810427 -1692.5  55.5 chic
## 866    1.651063133  0.416008445 -1691.5  56.0 chic
## 867   10.373889220  2.024032058 -1690.5  59.0 chic
## 868   20.759805017  1.248751159 -1689.5  63.5 chic
## 869    7.508673350 -0.390398871 -1688.5  68.0 chic
## 870    2.987928287 -2.413350901 -1687.5  65.5 chic
## 871    1.983603272 -2.319478447 -1686.5  64.0 chic
## 872    6.821261620  3.434683693 -1685.5  64.5 chic
## 873    2.737928287  0.974294961 -1684.5  63.0 chic
## 874    9.348083654  2.147273526 -1683.5  69.0 chic
## 875   17.268725388  0.982575252 -1682.5  75.0 chic
## 876    0.779594954 -1.112089770 -1681.5  67.5 chic
## 877    1.237928287 -2.266161384 -1680.5  62.0 chic
## 878    0.571261620 -1.883380536 -1679.5  56.0 chic
## 879    5.030790675 -1.151583223 -1678.5  57.0 chic
## 880   12.500174140 -1.919121696 -1677.5  67.5 chic
## 881   14.317079806  0.587435552 -1676.5  73.5 chic
## 882    1.423414140 -0.712477925 -1675.5  75.0 chic
## 883  -10.909919190 -2.625391032 -1674.5  68.5 chic
## 884    5.506747473 -1.518384848 -1673.5  67.5 chic
## 885   -5.277039387 -3.173601938 -1672.5  56.5 chic
## 886    2.587757079  0.128965717 -1671.5  62.5 chic
## 887   -0.909919193  1.606477264 -1670.5  66.0 chic
## 888    5.066936605  0.896741878 -1669.5  71.0 chic
## 889   14.923414140  1.773703156 -1668.5  71.5 chic
## 890   18.946261620  3.796299984 -1667.5  73.0 chic
## 891   -2.262071713 -2.088587207 -1666.5  61.0 chic
## 892   -5.137071713 -3.127671714 -1665.5  58.5 chic
## 893    2.747443033  5.699462388 -1664.5  60.0 chic
## 894   -3.849586861 -0.774156086 -1663.5  66.0 chic
## 895    0.298414140 -2.337974837 -1662.5  66.5 chic
## 896    5.548414140 -1.155374093 -1661.5  64.0 chic
## 897  -11.532993320 -2.598420847 -1660.5  54.5 chic
## 898   -9.722259744 -2.247861459 -1659.5  61.0 chic
## 899    3.692079806  1.013310427 -1658.5  65.5 chic
## 900    8.567079806 -3.370022906 -1657.5  72.5 chic
## 901   12.074461077  0.560736898 -1656.5  71.0 chic
## 902   24.328117795  1.067515386 -1655.5  68.5 chic
## 903   27.629091258           NA -1654.5  69.0 chic
## 904   24.982937950  0.887947695 -1653.5  79.0 chic
## 905   10.494178408  1.709605233 -1652.5  79.0 chic
## 906   11.332222554 -1.747647409 -1651.5  75.0 chic
## 907    8.066936605  0.660206424 -1650.5  73.0 chic
## 908    9.035845075 -0.188347785 -1649.5  77.5 chic
## 909   -3.053738380 -1.012947652 -1648.5  70.5 chic
## 910   -6.137071713 -2.808900298 -1647.5  64.5 chic
## 911   -2.755821592 -2.456372297 -1646.5  61.0 chic
## 912    4.400413139  3.113543753 -1645.5  65.5 chic
## 913   26.472014115  5.958291543 -1644.5  72.5 chic
## 914   21.019283022 -0.723067805 -1643.5  73.5 chic
## 915   16.785532673 -2.200150536 -1642.5  74.0 chic
## 916   10.587757079 -0.612991636 -1641.5  75.0 chic
## 917   16.103698347 -2.609003777 -1640.5  75.5 chic
## 918   25.896552989  3.302714209 -1639.5  79.0 chic
## 919    9.032720014 -1.838292634 -1638.5  78.5 chic
## 920    8.718129107  0.880190162 -1637.5  75.0 chic
## 921   24.113962440 -1.664138639 -1636.5  83.0 chic
## 922   22.854020649 -1.661237041 -1635.5  86.5 chic
## 923    2.418092546 -1.745078511 -1634.5  76.0 chic
## 924    1.648651680 -4.246144891 -1633.5  71.0 chic
## 925    0.142043245 -3.311044688 -1632.5  69.0 chic
## 926   -3.961157059 -3.848387289 -1631.5  68.0 chic
## 927   -1.631591246 -2.585439309 -1630.5  71.0 chic
## 928    9.144134152 -2.395720664 -1629.5  72.5 chic
## 929    2.988962440  1.757152021 -1628.5  71.0 chic
## 930    9.446759249 -1.273468495 -1627.5  70.0 chic
## 931   12.595396298 -3.490762132 -1626.5  67.5 chic
## 932    5.123026393 -2.881351206 -1625.5  67.5 chic
## 933    6.856985014 -2.611600517 -1624.5  69.0 chic
## 934   11.639242087 -1.693529356 -1623.5  74.0 chic
## 935   10.812089540  0.168053965 -1622.5  74.5 chic
## 936   14.123300819  0.036222516 -1621.5  77.0 chic
## 937    8.006747473  0.116850998 -1620.5  78.5 chic
## 938   -4.374825860 -0.673977546 -1619.5  79.5 chic
## 939    8.712841539 -2.203311837 -1618.5  82.5 chic
## 940    5.092271237 -2.621774481 -1617.5  67.5 chic
## 941   14.601717780 -4.211615733 -1616.5  69.0 chic
## 942   18.961416089 -3.730330816 -1615.5  71.5 chic
## 943   31.591264164 -0.630979657 -1614.5  71.0 chic
## 944   23.279653467 -0.650272694 -1613.5  72.5 chic
## 945    9.850376578  5.080593085 -1612.5  74.0 chic
## 946    0.452384547 -1.477340475 -1611.5  79.5 chic
## 947    3.478911446 -3.198739926 -1610.5  81.0 chic
## 948    7.238280854           NA -1609.5  77.0 chic
## 949   -4.458159193           NA -1608.5  63.0 chic
## 950   -1.637071713 -2.908080451 -1607.5  62.0 chic
## 951   -7.821991974 -1.182308617 -1606.5  65.0 chic
## 952   -4.271867151 -0.338160677 -1605.5  70.5 chic
## 953    1.872176274  0.268570719 -1604.5  72.5 chic
## 954    6.817732771 -0.384434144 -1603.5  70.5 chic
## 955    3.345396298  0.155100474 -1602.5  72.0 chic
## 956    9.444170713 -1.752219241 -1601.5  71.0 chic
## 957   -2.026253906 -1.677942082 -1600.5  71.0 chic
## 958   -4.589105836 -2.396623664 -1599.5  69.0 chic
## 959   -2.167305532 -1.261933661 -1598.5  66.5 chic
## 960   -1.000468320 -2.731032409 -1597.5  66.0 chic
## 961    0.320979106  1.283308466 -1596.5  66.5 chic
## 962   11.337841539 -1.606474426 -1595.5  67.5 chic
## 963    3.851711158 -2.919364370 -1594.5  74.5 chic
## 964    3.343129107  3.274509722 -1593.5  74.0 chic
## 965   -5.883111294 -2.734049333 -1592.5  77.5 chic
## 966    2.955509608 -2.892694549 -1591.5  70.5 chic
## 967    7.777680330 -2.825275314 -1590.5  70.0 chic
## 968    6.237292906 -1.309932311 -1589.5  70.0 chic
## 969    8.649614619  0.091530770 -1588.5  71.5 chic
## 970   11.625446353 -1.607788928 -1587.5  74.5 chic
## 971   -5.076662620 -2.846101759 -1586.5  77.0 chic
## 972   -8.440362389 -2.928405969 -1585.5  75.0 chic
## 973   -5.169152503  2.919611977 -1584.5  70.5 chic
## 974   -4.980817529  0.442236343 -1583.5  72.5 chic
## 975    0.142117538 -2.451578191 -1582.5  71.0 chic
## 976   -2.070539693 -3.305962477 -1581.5  64.0 chic
## 977   -2.514162620 -0.348911242 -1580.5  65.0 chic
## 978    4.971055373 -0.360431751 -1579.5  68.0 chic
## 979    0.056727497 -0.114272533 -1578.5  70.0 chic
## 980   -9.020693379 -2.874084789 -1577.5  71.5 chic
## 981   -5.344716902 -0.796141424 -1576.5  74.5 chic
## 982   -6.583228448 -0.437061651 -1575.5  76.5 chic
## 983   -6.101513718 -3.441958263 -1574.5  68.0 chic
## 984   -9.495576254 -3.055521745 -1573.5  63.0 chic
## 985  -10.066290090 -0.430391937 -1572.5  65.0 chic
## 986   -5.822938091 -1.879886711 -1571.5  60.5 chic
## 987   -4.503247371 -3.345779040 -1570.5  56.5 chic
## 988   -3.740204226 -3.103627609 -1569.5  58.0 chic
## 989   -1.472059229 -1.767084368 -1568.5  57.5 chic
## 990   -1.846456255 -1.725049585 -1567.5  59.5 chic
## 991    0.340080807 -0.644370915 -1566.5  63.5 chic
## 992    5.446614187 -1.607992588 -1565.5  65.0 chic
## 993   -1.714154925  1.594509081 -1564.5  65.0 chic
## 994    1.839439405  1.919150303 -1563.5  65.5 chic
## 995    0.140957565  1.745201990 -1562.5  66.0 chic
## 996   -1.219033984  0.359824356 -1561.5  61.5 chic
## 997   -8.818844567 -2.952964194 -1560.5  45.5 chic
## 998  -12.257601480 -1.510507437 -1559.5  48.0 chic
## 999   -9.466269710  0.973628604 -1558.5  54.5 chic
## 1000  -5.215347880 -2.182970489 -1557.5  51.5 chic
## 1001  -8.847391169  2.145974033 -1556.5  51.0 chic
## 1002  -8.622192286  2.494863966 -1555.5  58.0 chic
## 1003  -4.625604491 -0.478175965 -1554.5  59.5 chic
## 1004   0.858827053 -0.603497021 -1553.5  60.0 chic
## 1005  -3.907424207  0.142381508 -1552.5  65.5 chic
## 1006 -15.694075910 -3.142765069 -1551.5  56.5 chic
## 1007 -11.905814870 -2.605474331 -1550.5  44.5 chic
## 1008 -14.448998290 -1.234283195 -1549.5  47.0 chic
## 1009 -19.526326620 -2.150342652 -1548.5  52.5 chic
## 1010 -12.107751220 -3.110747609 -1547.5  51.5 chic
## 1011 -13.629821890 -2.944080942 -1546.5  45.0 chic
## 1012 -10.742621250 -2.867731510 -1545.5  44.0 chic
## 1013 -10.766486410 -1.973437003 -1544.5  45.5 chic
## 1014 -11.851897700 -0.862715690 -1543.5  51.0 chic
## 1015  -5.761591097  2.099598035 -1542.5  59.0 chic
## 1016  -7.236004797  2.598585353 -1541.5  61.0 chic
## 1017 -13.077647160 10.893760169 -1540.5  62.5 chic
## 1018   1.181287147  1.642276290 -1539.5  69.5 chic
## 1019   4.261932678 -0.361609926 -1538.5  70.5 chic
## 1020  -5.411392983 -2.336442798 -1537.5  58.5 chic
## 1021  -4.476553346 -4.176710414 -1536.5  43.0 chic
## 1022   1.012981904 -3.170443171 -1535.5  36.0 chic
## 1023  -1.137338651 -3.549220021 -1534.5  36.0 chic
## 1024 -13.285617420 -2.995135555 -1533.5  37.0 chic
## 1025 -12.273698240 -2.323286057 -1532.5  46.0 chic
## 1026 -13.211756280 -1.769846537 -1531.5  48.0 chic
## 1027 -13.873586630  0.324347363 -1530.5  58.5 chic
## 1028 -11.919565270  0.491578389 -1529.5  64.0 chic
## 1029  -7.854039208  1.345700087 -1528.5  65.5 chic
## 1030  -5.280235516  4.012020493 -1527.5  62.5 chic
## 1031  -5.852390589  4.790093510 -1526.5  62.5 chic
## 1032  -4.012791737  0.710312275 -1525.5  63.5 chic
## 1033   2.635009189  0.998400918 -1524.5  64.5 chic
## 1034  -5.230899327 -1.031942450 -1523.5  65.0 chic
## 1035 -17.416526490 -2.506215887 -1522.5  45.5 chic
## 1036 -17.526145480  2.599568728 -1521.5  43.0 chic
## 1037 -11.776037550 -2.569809520 -1520.5  35.0 chic
## 1038 -16.170981420 -1.251688733 -1519.5  31.0 chic
## 1039 -13.426131230  0.832967493 -1518.5  46.5 chic
## 1040  -4.774328795 -2.425211030 -1517.5  54.0 chic
## 1041 -14.289310070 -1.482221556 -1516.5  45.5 chic
## 1042 -11.444821730 -2.826807396 -1515.5  45.5 chic
## 1043 -15.959686430 -0.176753627 -1514.5  45.0 chic
## 1044 -12.442165870 -1.996540670 -1513.5  39.5 chic
## 1045 -11.356137270 -2.660737673 -1512.5  38.0 chic
## 1046 -12.434713820 -0.219865041 -1511.5  46.5 chic
## 1047  -7.952539106 -2.605603958 -1510.5  38.5 chic
## 1048  -2.157349788  0.843941851 -1509.5  59.0 chic
## 1049  -9.606011903 -3.071678332 -1508.5  54.0 chic
## 1050 -16.480031990 -3.361140778 -1507.5  40.5 chic
## 1051  -5.452849392 -3.049710806 -1506.5  26.0 chic
## 1052 -13.487544680 -1.550123757 -1505.5  24.5 chic
## 1053 -10.983137590 -3.026191226 -1504.5  21.0 chic
## 1054 -11.261385710 -0.152623891 -1503.5  37.0 chic
## 1055  -8.605455357 -0.866219264 -1502.5  45.5 chic
## 1056  -4.282651720 -1.918891903 -1501.5  30.0 chic
## 1057 -11.891445880 -2.112031723 -1500.5  26.5 chic
## 1058  -8.892083203 -1.430101796 -1499.5  18.5 chic
## 1059 -18.081690570           NA -1498.5  27.5 chic
## 1060 -16.686072000  3.407386662 -1497.5  44.0 chic
## 1061 -16.382963720  0.335795402 -1496.5  38.5 chic
## 1062 -10.752754620  3.310601787 -1495.5  51.0 chic
## 1063  -5.648241494 -2.899876777 -1494.5  28.5 chic
## 1064  -8.917085210 -1.078777978 -1493.5  20.5 chic
## 1065 -14.248802660 -0.422499123 -1492.5  34.0 chic
## 1066 -15.431974410  3.572956759 -1491.5  38.0 chic
## 1067  -7.331789735 -2.394798292 -1490.5  29.0 chic
## 1068  -2.212560741 -2.841733539 -1489.5  20.0 chic
## 1069 -12.516547250  3.207609808 -1488.5  33.5 chic
## 1070 -15.866847340  5.657653063 -1487.5  39.5 chic
## 1071 -10.926004180 -2.496886180 -1486.5  29.0 chic
## 1072  -7.892460405 -0.241767542 -1485.5  17.0 chic
## 1073  -7.172552997  1.274887821 -1484.5  21.5 chic
## 1074 -11.715027900 -0.814801134 -1483.5  28.5 chic
## 1075 -10.245152560 -0.841455576 -1482.5  31.0 chic
## 1076 -10.745037140 -2.609337767 -1481.5  19.5 chic
## 1077 -11.800900480 -1.360491968 -1480.5  11.0 chic
## 1078 -10.483495120  3.182653634 -1479.5  13.0 chic
## 1079 -11.547553300  0.862478644 -1478.5   8.0 chic
## 1080 -12.458218120 -0.850235208 -1477.5   3.5 chic
## 1081 -11.474665680 -0.583334564 -1476.5   2.5 chic
## 1082 -13.441660060  6.456671161 -1475.5   9.5 chic
## 1083 -14.544071110  8.376783343 -1474.5   9.5 chic
## 1084 -12.052498080 -2.287478792 -1473.5   7.5 chic
## 1085  -9.844707388 -0.720288891 -1472.5   0.0 chic
## 1086  -3.347490994 -2.158919328 -1471.5  -6.5 chic
## 1087 -11.733089980 -1.054343877 -1470.5  -4.5 chic
## 1088 -14.382249650  7.484952841 -1469.5  -1.0 chic
## 1089 -13.048332750 19.302411613 -1468.5   5.0 chic
## 1090 -11.000856280  2.213175502 -1467.5  24.5 chic
## 1091  -3.525340218 -1.950177832 -1466.5  12.0 chic
## 1092 -10.735891110 -0.075477308 -1465.5  20.5 chic
## 1093 -14.807319680 -1.937098635 -1464.5  27.0 chic
## 1094 -16.620578310  6.130163783 -1463.5  34.5 chic
## 1095 -17.527324350  5.387992418 -1462.5  32.5 chic
## 1096 -14.561287930  1.815654674 -1461.5  30.5 chic
## 1097  -6.029892039 -2.637489979 -1460.5  27.5 chic
## 1098 -14.963705420  2.910822536 -1459.5  33.0 chic
## 1099 -14.511091040 -0.130696115 -1458.5  39.0 chic
## 1100 -13.512775770 -2.793879467 -1457.5  36.0 chic
## 1101 -15.556193140 -2.494098795 -1456.5  29.5 chic
## 1102 -15.494335060  0.221059104 -1455.5  30.0 chic
## 1103 -13.951851400 -1.794543687 -1454.5  35.0 chic
## 1104 -15.087122180  1.826890435 -1453.5  37.0 chic
## 1105 -12.423490060 -1.062468961 -1452.5  38.0 chic
## 1106 -13.199400420 -2.302241389 -1451.5  32.5 chic
## 1107  -2.664279852  1.432342872 -1450.5  38.0 chic
## 1108  -1.215530869 -2.856228556 -1449.5  26.0 chic
## 1109 -12.196142600 -0.225971436 -1448.5  23.0 chic
## 1110 -13.357508170  1.932185354 -1447.5  36.5 chic
## 1111 -14.652118870  1.514477845 -1446.5  41.0 chic
## 1112 -17.723396960 -1.370575724 -1445.5  47.5 chic
## 1113  -9.704553326 -3.206783925 -1444.5  45.0 chic
## 1114  -7.019628232 -1.949312248 -1443.5  32.0 chic
## 1115  -6.711930859 -1.169545457 -1442.5  30.5 chic
## 1116 -14.238995360 -1.831328524 -1441.5  35.5 chic
## 1117 -10.532600880 -2.024824699 -1440.5  30.0 chic
## 1118 -12.661426380 -1.159367198 -1439.5  36.5 chic
## 1119 -14.824552470 -0.534827603 -1438.5  39.5 chic
## 1120 -12.014207970 -0.098352983 -1437.5  40.0 chic
## 1121  -8.151538906 -2.460300065 -1436.5  30.5 chic
## 1122 -13.185148400 -0.859397436 -1435.5  25.5 chic
## 1123  -1.666847293 -3.847669376 -1434.5  37.5 chic
## 1124 -12.010194440 -1.888553882 -1433.5  30.5 chic
## 1125 -17.090879340  4.881852734 -1432.5  34.0 chic
## 1126  -9.772715560  2.073232490 -1431.5  34.5 chic
## 1127  -8.215818804  5.114207856 -1430.5  30.5 chic
## 1128  -5.624950424  0.609468685 -1429.5  41.0 chic
## 1129   2.116680507 -2.656236055 -1428.5  30.0 chic
## 1130 -14.997117930  0.152697718 -1427.5  33.0 chic
## 1131   1.354262993 -1.150683926 -1426.5  34.0 chic
## 1132  -9.699587523  2.946912782 -1425.5  41.5 chic
## 1133 -15.610400190  5.260974198 -1424.5  38.5 chic
## 1134 -12.417677510  2.316258977 -1423.5  37.0 chic
## 1135  -0.477015020  2.453586443 -1422.5  50.0 chic
## 1136  -5.603189001 -0.468106023 -1421.5  41.5 chic
## 1137  -8.256018767 -0.651228600 -1420.5  32.0 chic
## 1138  -5.037696368 -0.665396368 -1419.5  28.5 chic
## 1139 -10.632486320  0.812602704 -1418.5  38.5 chic
## 1140   2.335432002 -0.328392532 -1417.5  41.0 chic
## 1141   7.790822729 -4.817035208 -1416.5  27.0 chic
## 1142   2.122199341 -1.765817253 -1415.5  29.5 chic
## 1143  -3.126601870 -2.677263492 -1414.5  27.5 chic
## 1144  -5.039261835 -2.657914925 -1413.5  21.0 chic
## 1145  -2.875038228  2.131273126 -1412.5  32.0 chic
## 1146  -0.757634914 -1.847829320 -1411.5  24.0 chic
## 1147  -9.533669702  2.445850524 -1410.5  20.5 chic
## 1148 -10.219976270  4.395755557 -1409.5  34.0 chic
## 1149  -7.700199370 -1.346135358 -1408.5  36.0 chic
## 1150  -0.212174909 -2.189120986 -1407.5  32.5 chic
## 1151   6.166623434 -2.446006076 -1406.5  21.0 chic
## 1152   0.741554373 -1.972633165 -1405.5  11.0 chic
## 1153  -7.075985159 -1.161730958 -1404.5  23.0 chic
## 1154  -2.629301981  2.225295248 -1403.5  32.5 chic
## 1155   1.142883212 -2.358758546 -1402.5  24.5 chic
## 1156  -6.401528968  1.220045620 -1401.5  30.5 chic
## 1157  -4.867371741  0.540710961 -1400.5  39.5 chic
## 1158   6.555665791 -2.395322252 -1399.5  29.0 chic
## 1159  -2.485263724  0.471741240 -1398.5  30.5 chic
## 1160   2.696484150 -2.795862461 -1397.5  33.0 chic
## 1161  11.309826717 -1.316657453 -1396.5  29.0 chic
## 1162   4.971184058  8.079381444 -1395.5  29.5 chic
## 1163 -13.638884770  1.165482998 -1394.5  40.5 chic
## 1164 -10.312854450 -1.016079216 -1393.5  46.0 chic
## 1165 -12.973718570 -2.204203272 -1392.5  51.0 chic
## 1166  -3.344101764 -0.462682751 -1391.5  57.5 chic
## 1167   7.149913995 -0.639767572 -1390.5  70.5 chic
## 1168  -7.252046789  0.519496993 -1389.5  67.0 chic
## 1169   1.320198776  0.720129263 -1388.5  65.0 chic
## 1170   6.934811287 -0.202327839 -1387.5  57.5 chic
## 1171   6.245307708 -1.322577437 -1386.5  51.0 chic
## 1172  12.950256354 -2.837488229 -1385.5  41.0 chic
## 1173   5.828248591 -2.212254802 -1384.5  36.0 chic
## 1174  -0.897549435 -2.709050867 -1383.5  26.5 chic
## 1175  -7.214470839 -1.127701660 -1382.5  33.0 chic
## 1176  -1.280026024  3.993711955 -1381.5  47.5 chic
## 1177  -1.927528266 -0.819646152 -1380.5  45.0 chic
## 1178   5.992474534 -1.402979486 -1379.5  31.0 chic
## 1179   2.207890651 -2.480515718 -1378.5  31.0 chic
## 1180  -0.207561180 -1.206631340 -1377.5  34.5 chic
## 1181   0.267321059 -3.451006370 -1376.5  33.0 chic
## 1182  -9.424235154  0.286399205 -1375.5  35.5 chic
## 1183  -6.173217977  1.263894070 -1374.5  41.5 chic
## 1184  -9.605623879 -1.743433742 -1373.5  41.5 chic
## 1185  -1.574392096 -2.350465471 -1372.5  41.5 chic
## 1186  -2.667274187 -2.285650271 -1371.5  41.5 chic
## 1187  -6.509019441 -1.188833079 -1370.5  48.0 chic
## 1188  -5.316757705 -2.901765267 -1369.5  38.0 chic
## 1189  -1.834087620 -2.092243045 -1368.5  41.5 chic
## 1190 -10.677165230 -0.628005316 -1367.5  43.0 chic
## 1191  -7.155126631 -2.176426403 -1366.5  38.0 chic
## 1192  -4.976605194 -1.299484189 -1365.5  31.0 chic
## 1193  -6.092936707 -2.368355553 -1364.5  33.5 chic
## 1194  -2.364282967  2.035257595 -1363.5  46.0 chic
## 1195  -4.320401949  1.571550324 -1362.5  51.0 chic
## 1196  -4.574673220 -3.044319870 -1361.5  41.5 chic
## 1197  -4.537483471 -1.873075739 -1360.5  36.0 chic
## 1198  -4.240030458  1.713543980 -1359.5  35.0 chic
## 1199  -6.052940913 -0.449640660 -1358.5  41.5 chic
## 1200   0.259732217 -1.968026864 -1357.5  48.5 chic
## 1201   0.990742443  1.527686749 -1356.5  48.5 chic
## 1202  -1.370075964 -0.284133793 -1355.5  48.0 chic
## 1203   3.048618715 -2.296284927 -1354.5  39.0 chic
## 1204  -0.737497700  1.183578681 -1353.5  41.5 chic
## 1205  -3.372779132  7.110025987 -1352.5  49.5 chic
## 1206 -15.307239910 -2.606550627 -1351.5  56.0 chic
## 1207  -5.501216221 -3.214895498 -1350.5  55.5 chic
## 1208   0.888963292  1.542362416 -1349.5  55.5 chic
## 1209   8.084028428  4.782502442 -1348.5  65.5 chic
## 1210   6.166682152  0.680647441 -1347.5  76.5 chic
## 1211   2.578412010  0.848763026 -1346.5  76.5 chic
## 1212   9.443520220  0.723159518 -1345.5  75.0 chic
## 1213  13.219105244 -0.218466571 -1344.5  73.5 chic
## 1214   1.729097235 -2.177893055 -1343.5  61.0 chic
## 1215  -5.323484784 -2.394270478 -1342.5  58.5 chic
## 1216  -7.190436404  0.537906094 -1341.5  55.0 chic
## 1217   3.350453351 -1.307281699 -1340.5  52.0 chic
## 1218   2.754612030  0.559737080 -1339.5  54.0 chic
## 1219   1.407778981 -0.528824537 -1338.5  51.5 chic
## 1220   8.970707108 -2.210958375 -1337.5  46.5 chic
## 1221  12.058151461 -1.118533595 -1336.5  51.0 chic
## 1222   3.238267392 -2.515645546 -1335.5  52.5 chic
## 1223  12.946907522 -0.596494247 -1334.5  62.5 chic
## 1224  22.974867524 -2.215110291 -1333.5  70.5 chic
## 1225   8.229733473 -2.635767568 -1332.5  62.5 chic
## 1226  -3.570243493 -2.887262288 -1331.5  43.5 chic
## 1227  -1.534376273  1.164824049 -1330.5  49.5 chic
## 1228   2.076752403 -4.131795911 -1329.5  50.5 chic
## 1229  20.575153693 -2.108304651 -1328.5  54.5 chic
## 1230   0.644242951 -1.735670794 -1327.5  56.5 chic
## 1231  -8.414881621 -1.973069693 -1326.5  55.5 chic
## 1232   1.721423174 -2.597814090 -1325.5  66.5 chic
## 1233   5.376547653 -2.705811463 -1324.5  56.0 chic
## 1234   8.903475902 -1.647227235 -1323.5  60.5 chic
## 1235   4.114354808 -1.746897695 -1322.5  62.5 chic
## 1236  -1.870604401 -3.277039442 -1321.5  54.5 chic
## 1237  -1.010136054 -2.857321132 -1320.5  52.5 chic
## 1238   0.204404449 -2.793353996 -1319.5  52.5 chic
## 1239  -1.594224227  2.076937846 -1318.5  57.0 chic
## 1240   7.446954224  2.326695087 -1317.5  61.5 chic
## 1241   2.911281886 -2.179780652 -1316.5  56.5 chic
## 1242  14.357370252 -2.713548439 -1315.5  59.5 chic
## 1243  25.572434779 -3.116521285 -1314.5  63.0 chic
## 1244  18.621484061 -4.697707272 -1313.5  62.5 chic
## 1245   8.339781162 -2.631752634 -1312.5  55.0 chic
## 1246   6.860841397 -2.469809499 -1311.5  53.5 chic
## 1247   4.187445622  3.085506037 -1310.5  57.5 chic
## 1248  13.264665349  0.153211005 -1309.5  74.0 chic
## 1249   7.582551850 -2.462891792 -1308.5  74.5 chic
## 1250   2.800412957 -2.163735371 -1307.5  61.0 chic
## 1251  -0.492560780 -3.090721432 -1306.5  53.0 chic
## 1252  -8.252237380  0.754336672 -1305.5  53.0 chic
## 1253  -2.192725139  3.045418133 -1304.5  69.0 chic
## 1254   1.093501643 -0.993326048 -1303.5  65.5 chic
## 1255  -5.521055654 -2.715817822 -1302.5  70.0 chic
## 1256   9.071391680 -2.922278496 -1301.5  72.5 chic
## 1257   8.936718516 -2.736380574 -1300.5  64.5 chic
## 1258   6.039429287  2.318293456 -1299.5  65.5 chic
## 1259  24.520464795 -0.469758357 -1298.5  78.0 chic
## 1260  26.624631462  0.020632079 -1297.5  81.5 chic
## 1261   8.513153924 -2.255815579 -1296.5  71.0 chic
## 1262  11.854696831  0.715382179 -1295.5  71.0 chic
## 1263  12.299984189  0.347287596 -1294.5  74.0 chic
## 1264  18.249922163 -1.293173074 -1293.5  78.5 chic
## 1265  11.872797890 -1.269881876 -1292.5  71.0 chic
## 1266  -4.809636331  0.765573236 -1291.5  63.0 chic
## 1267  -1.576532478 -1.506319420 -1290.5  69.0 chic
## 1268  -4.158318945  0.102309135 -1289.5  72.0 chic
## 1269  -2.677530701 -2.708332643 -1288.5  66.5 chic
## 1270  -3.950968201 -3.206213999 -1287.5  65.0 chic
## 1271   0.182787912 -2.659721532 -1286.5  64.0 chic
## 1272   1.018595953  3.194327432 -1285.5  69.5 chic
## 1273   7.854982324  1.358757752 -1284.5  75.5 chic
## 1274   8.654579623 -2.015182926 -1283.5  74.5 chic
## 1275   5.724722206  2.106990783 -1282.5  72.5 chic
## 1276   9.297855429 -1.249693360 -1281.5  73.5 chic
## 1277  12.841237910 -3.172551099 -1280.5  80.0 chic
## 1278  -2.748232343 -5.798521213 -1279.5  66.5 chic
## 1279   7.582300601 -0.556673039 -1278.5  69.5 chic
## 1280  23.607146451  0.295669472 -1277.5  77.0 chic
## 1281  29.741943818  0.433304189 -1276.5  86.5 chic
## 1282   5.612484188 -3.565443453 -1275.5  73.5 chic
## 1283  -2.996556666 -2.598339706 -1274.5  63.0 chic
## 1284   8.385639479 -5.112647669 -1273.5  67.0 chic
## 1285  21.785850857 -0.973339706 -1272.5  83.5 chic
## 1286  14.403737910 -1.140006372 -1271.5  81.0 chic
## 1287  -0.007138276 -3.787175771 -1270.5  70.5 chic
## 1288   4.650934200 -3.607683770 -1269.5  67.0 chic
## 1289   0.070286885 -3.952787348 -1268.5  66.0 chic
## 1290  -1.207390767 -2.890006372 -1267.5  62.5 chic
## 1291  -7.083701871 -3.046932568 -1266.5  62.0 chic
## 1292  -7.630032606 -3.093514965 -1265.5  65.0 chic
## 1293   2.350218178 -1.855586082 -1264.5  70.5 chic
## 1294  18.642285024 -1.677348165 -1263.5  78.0 chic
## 1295   3.071176944 -1.140006372 -1262.5  78.5 chic
## 1296  -4.638731056 -1.056673039 -1261.5  77.5 chic
## 1297  -5.011732608 -2.947972996 -1260.5  68.0 chic
## 1298   2.683571115 -2.954235939 -1259.5  69.5 chic
## 1299   6.512785676 -3.221468050 -1258.5  68.5 chic
## 1300  -2.574526789 -3.901120206 -1257.5  68.5 chic
## 1301  -4.072265126 -2.050717402 -1256.5  69.5 chic
## 1302   3.735608511 -4.054653334 -1255.5  72.0 chic
## 1303   9.343049504  1.910650358 -1254.5  75.5 chic
## 1304  23.839451492  7.081044552 -1253.5  74.5 chic
## 1305  27.065598607  2.237272562 -1252.5  79.0 chic
## 1306  12.183504750 -2.773795729 -1251.5  79.5 chic
## 1307  -5.663223332 -2.999568201 -1250.5  69.5 chic
## 1308  -3.534441619 -4.495804873 -1249.5  62.0 chic
## 1309  -1.124899147 -0.874395724 -1248.5  63.5 chic
## 1310   8.361859037 -1.570746222 -1247.5  69.0 chic
## 1311  15.211484523 -0.556460401 -1246.5  75.5 chic
## 1312   0.596972889           NA -1245.5  76.5 chic
## 1313  -2.831303598 -4.004491365 -1244.5  68.5 chic
## 1314  -5.061557433 -3.731319951 -1243.5  61.0 chic
## 1315  -2.561487129 -0.096972571 -1242.5  63.5 chic
## 1316  -1.499899147 -0.147973713 -1241.5  69.0 chic
## 1317   5.206337483           NA -1240.5  69.0 chic
## 1318   9.603258271           NA -1239.5  72.0 chic
## 1319  19.838033912           NA -1238.5  73.0 chic
## 1320   2.903967978           NA -1237.5  68.5 chic
## 1321  -5.361446355 -3.437478729 -1236.5  63.0 chic
## 1322  -5.438653395 -0.610299352 -1235.5  65.5 chic
## 1323   7.849041633 -0.147675605 -1234.5  74.5 chic
## 1324   9.732728734  2.526660294 -1233.5  73.0 chic
## 1325  -8.792543702  0.161410366 -1232.5  72.0 chic
## 1326  11.948032193 -2.056673039 -1231.5  80.5 chic
## 1327   8.227208765 -3.691692212 -1230.5  75.5 chic
## 1328  -7.744535600 -5.804519928 -1229.5  71.5 chic
## 1329 -11.974243730 -3.910559254 -1228.5  68.0 chic
## 1330   1.191062067 -3.593094296 -1227.5  69.0 chic
## 1331   2.094246915 -3.079232547 -1226.5  70.5 chic
## 1332   3.569945795 -3.434927842 -1225.5  75.0 chic
## 1333   7.122762620 -2.832972535 -1224.5  76.5 chic
## 1334  19.369224851  1.693326961 -1223.5  78.5 chic
## 1335  13.371852997 -0.974134639 -1222.5  82.5 chic
## 1336  11.649395400 -0.397935730 -1221.5  82.5 chic
## 1337   7.131715826 -2.848339706 -1220.5  75.5 chic
## 1338  12.622748291           NA -1219.5  69.0 chic
## 1339  22.337828652           NA -1218.5  70.0 chic
## 1340  16.866414793  0.888532030 -1217.5  78.0 chic
## 1341   1.389252403  1.019885427 -1216.5  73.0 chic
## 1342  -1.041565813  0.845472282 -1215.5  71.0 chic
## 1343   5.232728734  1.074815005 -1214.5  79.0 chic
## 1344   7.068014423  0.852514536 -1213.5  77.0 chic
## 1345   6.530514423 -1.011203076 -1212.5  83.0 chic
## 1346   5.317460804 -3.196156704 -1211.5  72.0 chic
## 1347  -3.681997952  1.401660294 -1210.5  72.0 chic
## 1348  -1.678340765 -2.265006372 -1209.5  73.0 chic
## 1349  13.908248698 -1.587269376 -1208.5  75.5 chic
## 1350   5.721060984  0.874559552 -1207.5  74.0 chic
## 1351   5.993111744  6.987738391 -1206.5  74.5 chic
## 1352   3.265618152  0.539012504 -1205.5  74.5 chic
## 1353  -6.753501110 -0.237228594 -1204.5  65.5 chic
## 1354  -7.824232608 -1.887251541 -1203.5  63.0 chic
## 1355  -1.142821121 -2.307429443 -1202.5  57.0 chic
## 1356  -3.598146069 -1.981237611 -1201.5  56.0 chic
## 1357 -11.705993720 -0.935910038 -1200.5  55.5 chic
## 1358 -17.761504640 -2.727127584 -1199.5  57.5 chic
## 1359  -6.664888999 -3.084255777 -1198.5  59.5 chic
## 1360 -13.661515790  0.371136989 -1197.5  62.0 chic
## 1361 -14.207278060 -1.520477138 -1196.5  56.5 chic
## 1362  -7.941799143 -3.256198112 -1195.5  50.5 chic
## 1363  -7.244630071 -0.496333211 -1194.5  53.5 chic
## 1364  -2.821298394 -1.806673039 -1193.5  67.0 chic
## 1365  -6.702072875 -1.864110097 -1192.5  67.5 chic
## 1366  -2.820560901 -0.727818377 -1191.5  65.5 chic
## 1367  -4.815746436 -1.101834276 -1190.5  61.0 chic
## 1368  -6.396737303 -5.014607062 -1189.5  58.5 chic
## 1369 -11.338529600 -3.306673039 -1188.5  52.5 chic
## 1370 -12.173349190 -0.159895499 -1187.5  57.0 chic
## 1371  -6.440391058  0.737182327 -1186.5  56.5 chic
## 1372  -1.466360706 -1.707311033 -1185.5  67.0 chic
## 1373 -11.244350740 -2.172542019 -1184.5  57.5 chic
## 1374   2.553393165 -1.806673039 -1183.5  68.5 chic
## 1375  12.726581823 -2.512429660 -1182.5  75.0 chic
## 1376  -2.049694039 -3.091198386 -1181.5  55.0 chic
## 1377  -9.375464201 -3.458122544 -1180.5  50.5 chic
## 1378  -2.678728105 -3.825124825 -1179.5  47.0 chic
## 1379 -11.773566070 -2.990074791 -1178.5  41.5 chic
## 1380 -12.912147070 -1.098339706 -1177.5  46.5 chic
## 1381 -12.526183820  1.482901054 -1176.5  48.5 chic
## 1382  -7.384845594  2.077463053 -1175.5  52.0 chic
## 1383  -2.946343654  0.213866615 -1174.5  53.5 chic
## 1384 -17.165232740 -2.761953631 -1173.5  51.0 chic
## 1385  -8.041132892 -0.120596570 -1172.5  56.0 chic
## 1386  11.158475582 -2.176645863 -1171.5  65.0 chic
## 1387 -10.518148760 -3.265006372 -1170.5  43.0 chic
## 1388 -14.731899400  0.026660294 -1169.5  40.5 chic
## 1389  -7.715021231  5.135244017 -1168.5  54.5 chic
## 1390  -8.971036179  1.806504190 -1167.5  49.0 chic
## 1391 -14.870967800  1.355128278 -1166.5  43.5 chic
## 1392 -16.231899400  1.964912922 -1165.5  46.5 chic
## 1393 -13.440232740 -2.777441553 -1164.5  46.5 chic
## 1394  -9.902174128 -3.085999999 -1163.5  40.0 chic
## 1395 -15.881012390 -2.152128276 -1162.5  41.0 chic
## 1396  -9.315232735  2.021361811 -1161.5  52.5 chic
## 1397 -11.093070970 -2.863491221 -1160.5  41.0 chic
## 1398 -13.148566070  3.095321105 -1159.5  45.5 chic
## 1399  -5.338444177  6.818434411 -1158.5  60.0 chic
## 1400 -11.481899400  2.314651457 -1157.5  58.0 chic
## 1401  -0.358314458  2.082586177 -1156.5  60.0 chic
## 1402   6.739547474  0.535718265 -1155.5  65.5 chic
## 1403   1.670328590  0.480725638 -1154.5  59.5 chic
## 1404   1.591137689 -3.373319135 -1153.5  42.0 chic
## 1405  -4.463213028 -2.793111562 -1152.5  39.5 chic
## 1406 -13.977723190 -2.947839572 -1151.5  36.5 chic
## 1407 -12.151316890 -1.794174656 -1150.5  34.0 chic
## 1408 -14.081481360 -0.306673039 -1149.5  31.5 chic
## 1409 -16.272307690 -1.342853277 -1148.5  37.0 chic
## 1410 -10.801057250 -0.675284492 -1147.5  40.5 chic
## 1411 -12.173729510 -0.140006372 -1146.5  37.5 chic
## 1412  -8.133575875  0.714706705 -1145.5  38.0 chic
## 1413  -8.834773767  4.701163367 -1144.5  38.0 chic
## 1414 -11.551639120  7.996777514 -1143.5  51.0 chic
## 1415   3.224274704  0.231370439 -1142.5  61.0 chic
## 1416  -3.752186868  0.036762030 -1141.5  47.5 chic
## 1417  -6.950774891 -2.904674815 -1140.5  42.0 chic
## 1418  -8.951849203  3.824226100 -1139.5  45.5 chic
## 1419 -16.112096630  3.213693314 -1138.5  44.0 chic
## 1420 -13.699281510  6.029421014 -1137.5  44.5 chic
## 1421  -6.478926700  4.032264666 -1136.5  59.5 chic
## 1422  -9.850892153 -2.812767081 -1135.5  43.5 chic
## 1423  -3.389982238 -0.144440884 -1134.5  42.0 chic
## 1424 -13.714454240  0.319559111 -1133.5  42.0 chic
## 1425  -4.867025956 -1.052900559 -1132.5  44.5 chic
## 1426 -11.301172270 -0.298701183 -1131.5  53.0 chic
## 1427 -10.403996730 -3.181673039 -1130.5  61.0 chic
## 1428  -8.059431606 -2.716050716 -1129.5  39.5 chic
## 1429 -14.168068300 -0.199164440 -1128.5  31.5 chic
## 1430  -8.935644527 -0.975998703 -1127.5  41.0 chic
## 1431  -9.106232762 -0.417768548 -1126.5  40.5 chic
## 1432   2.768522140 -2.196425584 -1125.5  33.0 chic
## 1433  -5.794893784 -2.723339706 -1124.5  33.0 chic
## 1434  -2.031327069 -3.098339706 -1123.5  22.0 chic
## 1435 -12.218561360  2.484993628 -1122.5  28.5 chic
## 1436 -11.684907750 -1.902672990 -1121.5  29.5 chic
## 1437 -13.717044080  3.324845480 -1120.5  31.0 chic
## 1438 -15.588659390  1.106481782 -1119.5  37.0 chic
## 1439 -11.930279380  0.817685855 -1118.5  43.0 chic
## 1440  -5.097558132 -0.701734022 -1117.5  39.5 chic
## 1441 -11.918141190  2.965385785 -1116.5  44.0 chic
## 1442  -7.186166850 -0.519607068 -1115.5  45.5 chic
## 1443  -1.306136564 -2.758596116 -1114.5  30.5 chic
## 1444  -8.719845202 -2.253432933 -1113.5  30.5 chic
## 1445 -15.925844270 -2.306673039 -1112.5  39.5 chic
## 1446  -9.985601505  1.277401288 -1111.5  34.5 chic
## 1447 -16.466927370  2.193326961 -1110.5  36.5 chic
## 1448 -17.107333530 -0.771873057 -1109.5  34.0 chic
## 1449 -16.798743430  1.916274755 -1108.5  31.0 chic
## 1450 -16.212492260  3.281263647 -1107.5  40.0 chic
## 1451 -16.887912430 -0.806673039 -1106.5  38.0 chic
## 1452 -10.922021090 -2.098339706 -1105.5  17.0 chic
## 1453  -2.507682170 -3.431673039 -1104.5   7.0 chic
## 1454  -3.203622046 -1.671011832 -1103.5   8.0 chic
## 1455  -3.852664997  4.011885248 -1102.5  14.5 chic
## 1456 -11.918958180  2.269772057 -1101.5   3.5 chic
## 1457 -10.164379000 10.051095280 -1100.5  17.5 chic
## 1458 -15.795125310 11.984993628 -1099.5  31.0 chic
## 1459 -18.490844590 -2.598339706 -1098.5  33.5 chic
## 1460  -9.913405602 -3.504265207 -1097.5  15.0 chic
## 1461 -10.081056640  1.717126281 -1096.5   8.0 chic
## 1462  -9.152692583 18.502486754 -1095.5  20.5 chic
## 1463  -9.652614332 -0.140038573 -1094.5  16.0 chic
## 1464 -13.611827690  0.780756586 -1093.5   6.5 chic
## 1465 -12.872448600  3.287469235 -1092.5  14.0 chic
## 1466 -15.513555040  4.064542357 -1091.5  25.5 chic
## 1467  -9.019710114 -0.742747951 -1090.5  20.5 chic
## 1468   4.891386504 -1.081476282 -1089.5  21.5 chic
## 1469 -14.949245990  1.814542357 -1088.5  25.5 chic
## 1470 -16.615562510  3.282842761 -1087.5  24.0 chic
## 1471 -11.988228250  3.939542357 -1086.5  22.0 chic
## 1472 -12.670259660  0.606209024 -1085.5  29.5 chic
## 1473 -14.688998030 -2.415622915 -1084.5  25.5 chic
## 1474  -7.207882775  2.322499151 -1083.5  24.5 chic
## 1475 -10.728541040  8.606209024 -1082.5  31.5 chic
## 1476 -17.177237910  4.522875691 -1081.5  30.5 chic
## 1477 -11.488427220 -3.577289391 -1080.5  31.0 chic
## 1478  -8.235828012 -2.965570191 -1079.5  26.0 chic
## 1479  -8.795142966  0.735103110 -1078.5  21.5 chic
## 1480  -3.870189651  2.776769776 -1077.5  36.5 chic
## 1481  -3.182480335 -1.848024939 -1076.5  26.5 chic
## 1482  -4.356232937 -1.807277881 -1075.5  10.5 chic
## 1483 -17.524151240  3.698808261 -1074.5  11.0 chic
## 1484  -8.686514898  0.413929984 -1073.5  21.5 chic
## 1485 -11.530439480 -3.853148331 -1072.5   9.0 chic
## 1486 -13.980084330  2.655825891 -1071.5   7.5 chic
## 1487  -7.505177704 -2.560376692 -1070.5  15.5 chic
## 1488  -5.676746332 12.895254654 -1069.5  22.0 chic
## 1489 -10.201606280  5.663974875 -1068.5  25.5 chic
## 1490   0.567325884 -3.510874671 -1067.5  19.0 chic
## 1491  -6.668380057  4.779118878 -1066.5  15.0 chic
## 1492  -3.978075249  3.417461575 -1065.5  17.0 chic
## 1493  -9.427244360 10.564251136 -1064.5  29.0 chic
## 1494  -5.567151495  7.405791996 -1063.5  39.0 chic
## 1495  -1.734596472  4.260869577 -1062.5  42.5 chic
## 1496  -5.038669733  7.814542357 -1061.5  44.0 chic
## 1497  -9.373994712 -1.146263117 -1060.5  39.0 chic
## 1498  -4.782456283 -7.474473236 -1059.5  35.5 chic
## 1499 -11.304608580 -3.151874935 -1058.5  32.5 chic
## 1500 -11.634559260 -0.772192015 -1057.5  34.0 chic
## 1501 -10.085458630 -2.368100301 -1056.5  37.5 chic
## 1502  -1.224378505 -5.095603526 -1055.5  30.0 chic
## 1503  -2.029080553 -4.337963520 -1054.5  22.0 chic
## 1504  -9.362804342 -0.382680371 -1053.5  24.5 chic
## 1505 -12.465568900  0.751105818 -1052.5  32.5 chic
## 1506  -3.125888504 -1.982143549 -1051.5  22.0 chic
## 1507   0.539148740 -4.036038426 -1050.5   8.5 chic
## 1508  -8.329567348  2.600961704 -1049.5  16.0 chic
## 1509  -8.140381414 -2.422103387 -1048.5  35.0 chic
## 1510 -13.532672950 -3.098879689 -1047.5  38.5 chic
## 1511  -8.430923408 -3.102109907 -1046.5  35.5 chic
## 1512  -4.312708003  2.246396325 -1045.5  36.5 chic
## 1513  -7.986862635  0.219951284 -1044.5  47.0 chic
## 1514   1.324454810 -2.009967697 -1043.5  34.5 chic
## 1515  -2.878560352 -2.767184860 -1042.5  26.5 chic
## 1516  -4.805023797 -1.881255028 -1041.5  28.5 chic
## 1517  -0.417340658 -4.975918958 -1040.5  21.0 chic
## 1518  -6.512510204 -1.892104694 -1039.5  19.0 chic
## 1519  -4.699656148 -3.114269178 -1038.5  23.5 chic
## 1520  -6.917623392  0.705382010 -1037.5  39.0 chic
## 1521  -2.134442880 -0.911689363 -1036.5  48.0 chic
## 1522  -2.546782126 -2.944076650 -1035.5  37.5 chic
## 1523   1.448639119 -5.149340289 -1034.5  25.5 chic
## 1524  -7.521302935 -1.927347052 -1033.5  31.0 chic
## 1525 -10.586294470  2.333445752 -1032.5  39.0 chic
## 1526  -3.540713145 -1.679906464 -1031.5  39.5 chic
## 1527  -1.296798059 -3.910712535 -1030.5  28.0 chic
## 1528  -9.653477478 -2.205761178 -1029.5  32.5 chic
## 1529  -1.281489086 -2.529078398 -1028.5  33.5 chic
## 1530   0.516244430 -0.301859600 -1027.5  34.5 chic
## 1531   0.110646816 -1.662051877 -1026.5  40.0 chic
## 1532  -1.603807045 -0.415722504 -1025.5  32.0 chic
## 1533   5.551615309 -3.853832002 -1024.5  32.5 chic
## 1534   6.610795712 -2.780467980 -1023.5  34.5 chic
## 1535  -3.158091650 -2.985575027 -1022.5  34.0 chic
## 1536   2.631934296 -0.476016675 -1021.5  39.0 chic
## 1537  -8.997404031 -2.760224307 -1020.5  39.0 chic
## 1538  -7.979286963  0.762447644 -1019.5  42.5 chic
## 1539  -8.809630422 -1.497856287 -1018.5  44.0 chic
## 1540  -5.963270321  1.143915615 -1017.5  47.5 chic
## 1541  -1.018186680 -0.734093602 -1016.5  60.0 chic
## 1542  -2.000459352 -4.118740525 -1015.5  55.0 chic
## 1543   0.942420685 -3.832619706 -1014.5  47.0 chic
## 1544  -0.332253747 -3.563826735 -1013.5  45.0 chic
## 1545 -10.179714420  1.680515505 -1012.5  49.0 chic
## 1546  -2.364039916  1.569840227 -1011.5  63.0 chic
## 1547  -9.377003181 -4.184339386 -1010.5  56.0 chic
## 1548   2.185382302 -3.525566449 -1009.5  42.0 chic
## 1549   6.762537663 -3.150189570 -1008.5  30.5 chic
## 1550   1.799506862 -0.900486531 -1007.5  33.0 chic
## 1551  -1.456311410 -1.856608881 -1006.5  43.5 chic
## 1552  -1.253368371 -0.812288223 -1005.5  41.0 chic
## 1553  -4.583045263  0.333169495 -1004.5  39.0 chic
## 1554  -5.361852059  0.273187232 -1003.5  51.0 chic
## 1555  -9.502382203 -1.830635265 -1002.5  52.5 chic
## 1556   2.034100132  0.684873994 -1001.5  60.0 chic
## 1557  14.885865160  1.047996695 -1000.5  70.0 chic
## 1558   2.084407352 -2.842561800  -999.5  69.0 chic
## 1559  -9.329916380  0.460925344  -998.5  65.5 chic
## 1560 -14.877455590 -3.281279589  -997.5  44.0 chic
## 1561   4.062318726 -2.483007189  -996.5  43.5 chic
## 1562   0.017280234 -2.783333089  -995.5  38.0 chic
## 1563  -5.415880484  1.319622055  -994.5  44.0 chic
## 1564 -10.184474250 -2.573290688  -993.5  45.5 chic
## 1565  -6.040844329 -1.405939911  -992.5  58.5 chic
## 1566   1.753552375 -2.110795504  -991.5  53.0 chic
## 1567  -6.341038767 -1.083242536  -990.5  53.0 chic
## 1568  -2.082510995 -1.907561786  -989.5  47.5 chic
## 1569   0.918036961 -1.502099855  -988.5  46.0 chic
## 1570   1.808849833 -5.246749306  -987.5  46.5 chic
## 1571   6.505375348 -4.551969815  -986.5  40.5 chic
## 1572   5.727791550 -3.443415809  -985.5  44.5 chic
## 1573  -0.569748501 -0.432200908  -984.5  48.0 chic
## 1574  -4.045953036 -2.700024198  -983.5  50.0 chic
## 1575  -6.679560234  1.713116226  -982.5  50.5 chic
## 1576   2.722447893  2.093235704  -981.5  53.5 chic
## 1577   3.921572260  4.966889760  -980.5  59.5 chic
## 1578 -13.009645340 -3.942724233  -979.5  64.5 chic
## 1579  -7.236146327 -1.022240675  -978.5  64.5 chic
## 1580  -3.420218720 -1.671401657  -977.5  64.0 chic
## 1581  -0.565947699 -3.211665537  -976.5  57.5 chic
## 1582  -3.146726434 -3.812806221  -975.5  52.5 chic
## 1583   0.027124778 -2.114775086  -974.5  55.0 chic
## 1584  -1.846063627 -1.970190830  -973.5  46.5 chic
## 1585  -7.056208555  0.093973601  -972.5  52.0 chic
## 1586  -4.038069648 -2.326877208  -971.5  56.0 chic
## 1587  -5.924528858 -3.898824311  -970.5  45.0 chic
## 1588   2.482812967 -1.488764281  -969.5  53.0 chic
## 1589  -4.931208555  1.761601523  -968.5  55.0 chic
## 1590   4.800388770  1.722418189  -967.5  62.0 chic
## 1591   3.518089773  0.842956280  -966.5  65.0 chic
## 1592   6.675388770  0.998590526  -965.5  70.5 chic
## 1593   4.257289324  0.529015399  -964.5  74.5 chic
## 1594   9.425388770  3.591461665  -963.5  76.0 chic
## 1595  -1.642187033  2.886454853  -962.5  74.0 chic
## 1596   9.117768725  5.243701517  -961.5  72.5 chic
## 1597  -2.150095197  5.153407734  -960.5  73.5 chic
## 1598  -8.578561112 -4.520406487  -959.5  60.0 chic
## 1599  -4.695476448 -5.490624675  -958.5  50.0 chic
## 1600   5.378597019 -2.821833352  -957.5  54.5 chic
## 1601   5.110458112 -0.649554037  -956.5  60.5 chic
## 1602   9.415792232  1.213023319  -955.5  68.5 chic
## 1603 -11.472327050 -1.958272096  -954.5  74.5 chic
## 1604  -7.620818462 -0.439978005  -953.5  74.0 chic
## 1605   3.261009216 -1.308819440  -952.5  77.5 chic
## 1606  -9.737018582 -4.185808868  -951.5  67.0 chic
## 1607   2.435069773 -1.522012062  -950.5  76.0 chic
## 1608   7.300388770 -0.898993829  -949.5  77.5 chic
## 1609   3.767473933  0.598230340  -948.5  80.5 chic
## 1610   3.998138930 -0.730412221  -947.5  79.0 chic
## 1611   6.182551145 -2.944803641  -946.5  77.5 chic
## 1612   0.712389165 -1.391054762  -945.5  77.5 chic
## 1613   9.438618209  2.298688540  -944.5  74.5 chic
## 1614  13.839858032 -1.494296028  -943.5  72.5 chic
## 1615   8.574322993 -0.459471907  -942.5  69.0 chic
## 1616   5.340475087 -3.173505993  -941.5  61.5 chic
## 1617  -0.182898565 -2.928884809  -940.5  61.0 chic
## 1618   2.850513305 -4.677052121  -939.5  62.0 chic
## 1619   4.870545931 -1.684399666  -938.5  65.5 chic
## 1620   9.021140014 -0.557520703  -937.5  67.5 chic
## 1621  22.352055411  1.763587678  -936.5  72.5 chic
## 1622  17.467440802  1.180231573  -935.5  75.0 chic
## 1623   1.215172945 -1.845983638  -934.5  73.0 chic
## 1624  -0.915996173 -2.792426699  -933.5  71.5 chic
## 1625  16.657047083 -1.676195722  -932.5  73.5 chic
## 1626  18.061243760 -2.645821688  -931.5  83.0 chic
## 1627  11.108222498 -2.977124309  -930.5  77.5 chic
## 1628   7.383387918 -3.223920206  -929.5  69.5 chic
## 1629   3.057027565 -2.926447717  -928.5  69.0 chic
## 1630  19.435334926 -0.009794360  -927.5  71.0 chic
## 1631  25.972306892 -2.857507123  -926.5  72.5 chic
## 1632  39.820304240  1.190046320  -925.5  78.5 chic
## 1633  22.721190219  0.676201553  -924.5  78.0 chic
## 1634  -3.984905592 -5.431931231  -923.5  64.5 chic
## 1635   5.885789495 -5.467378245  -922.5  68.0 chic
## 1636  18.192468873 -0.188636009  -921.5  69.0 chic
## 1637  29.119442083 -3.691789405  -920.5  71.5 chic
## 1638  23.739956161 -1.732730434  -919.5  78.5 chic
## 1639  23.723427742 -1.312013290  -918.5  81.5 chic
## 1640  19.015360899  2.325620052  -917.5  79.5 chic
## 1641  18.026992895 -0.054701069  -916.5  84.0 chic
## 1642   9.751412662 -4.777113876  -915.5  74.0 chic
## 1643   5.784657429  1.291574138  -914.5  81.0 chic
## 1644   6.474934202  0.916969214  -913.5  82.0 chic
## 1645  -1.794617160  1.124956936  -912.5  78.5 chic
## 1646  -0.597112791 -1.938408807  -911.5  73.0 chic
## 1647   6.968406438 -0.797429504  -910.5  75.5 chic
## 1648  15.840926637 -2.292927338  -909.5  81.0 chic
## 1649  13.168876345 -2.082728337  -908.5  80.0 chic
## 1650   3.812929957 -2.367928328  -907.5  71.0 chic
## 1651  -0.581449290 -0.536109783  -906.5  71.5 chic
## 1652  10.795251661 -0.767514030  -905.5  70.5 chic
## 1653  19.852001593  0.458967701  -904.5  76.0 chic
## 1654   9.612509453 -0.986884069  -903.5  79.0 chic
## 1655   5.968436075 -2.693511739  -902.5  72.0 chic
## 1656  -0.407624178 -2.386982231  -901.5  69.5 chic
## 1657   6.348427742  2.698956232  -900.5  70.5 chic
## 1658  15.844122828  0.828092099  -899.5  76.0 chic
## 1659  25.637943505  4.491287176  -898.5  78.5 chic
## 1660  28.005732189 -0.052043288  -897.5  83.0 chic
## 1661  34.714367303 -1.665868962  -896.5  85.5 chic
## 1662  28.024479633  0.103218207  -895.5  85.0 chic
## 1663  22.883722104  1.823255395  -894.5  83.0 chic
## 1664  12.406796279  0.893600533  -893.5  88.0 chic
## 1665   4.923963386 -4.123635137  -892.5  74.5 chic
## 1666   0.828057216 -1.615915620  -891.5  70.5 chic
## 1667  -2.147052403 -3.156400287  -890.5  68.0 chic
## 1668  -0.870403103 -3.851691396  -889.5  66.0 chic
## 1669   4.213286920 -2.862569481  -888.5  66.5 chic
## 1670  15.848820628 -3.297063849  -887.5  73.5 chic
## 1671  11.042579315  2.305585086  -886.5  70.0 chic
## 1672   0.757289324 -1.428049266  -885.5  66.5 chic
## 1673  10.836073836 -0.173671743  -884.5  75.5 chic
## 1674   9.840622658  5.250557799  -883.5  78.5 chic
## 1675  14.085767517  7.201954631  -882.5  83.5 chic
## 1676   7.125822338 -0.861881734  -881.5  71.0 chic
## 1677  -2.032944563 -2.672429504  -880.5  69.0 chic
## 1678   5.342055437 -5.508530054  -879.5  68.0 chic
## 1679   5.193791445  0.858200157  -878.5  71.5 chic
## 1680   2.384285899  2.910380804  -877.5  72.5 chic
## 1681   0.980023329 -2.542567971  -876.5  67.5 chic
## 1682   3.721190219 -3.572243604  -875.5  69.5 chic
## 1683  -2.975520367 -1.640403299  -874.5  70.0 chic
## 1684   4.017331932 -3.071683370  -873.5  68.0 chic
## 1685  -1.240714101 -3.816935312  -872.5  67.5 chic
## 1686   2.764741016 -2.326193479  -871.5  70.5 chic
## 1687   4.003597019  6.393459428  -870.5  71.5 chic
## 1688   6.045263686 -1.625003693  -869.5  77.0 chic
## 1689   8.461930352  6.904127811  -868.5  76.5 chic
## 1690   7.193791445 -0.144483649  -867.5  75.0 chic
## 1691   1.392331932 -0.549104227  -866.5  74.0 chic
## 1692  -8.039486250 -0.353530545  -865.5  64.5 chic
## 1693  -4.982668068 -0.771281305  -864.5  67.0 chic
## 1694  -2.104480407  1.290416492  -863.5  68.5 chic
## 1695   4.789998593  1.310017465  -862.5  78.0 chic
## 1696  -0.127147461 -2.491484324  -861.5  70.5 chic
## 1697  13.196395162  4.123362674  -860.5  75.5 chic
## 1698  29.285110405  4.168975635  -859.5  81.0 chic
## 1699  20.446325386  4.218442635  -858.5  80.0 chic
## 1700   2.441146300  7.120846966  -857.5  81.0 chic
## 1701   8.250822338  7.156933414  -856.5  81.5 chic
## 1702   9.130498020  4.012181787  -855.5  82.5 chic
## 1703  -7.116277896  0.854666178  -854.5  79.0 chic
## 1704  -2.087503909 -4.210271733  -853.5  71.5 chic
## 1705   7.383226952  3.879031927  -852.5  66.5 chic
## 1706   6.298955991  2.939417781  -851.5  73.5 chic
## 1707  -7.594093562 -0.926325507  -850.5  77.0 chic
## 1708  -2.996712679 -4.034957769  -849.5  67.0 chic
## 1709  -4.784377342  2.754713774  -848.5  66.0 chic
## 1710   0.846278760  3.018873897  -847.5  70.0 chic
## 1711   8.097328738  3.195260178  -846.5  75.5 chic
## 1712  15.887310628  2.279161716  -845.5  76.5 chic
## 1713   1.958171675  0.260505594  -844.5  82.0 chic
## 1714  -3.805660388 -1.087361785  -843.5  73.5 chic
## 1715  -6.433853700 -0.188239793  -842.5  67.5 chic
## 1716  -5.907944563  2.930925592  -841.5  70.5 chic
## 1717   1.108079375 -3.099742496  -840.5  73.5 chic
## 1718  -5.511756199 -3.165953401  -839.5  79.0 chic
## 1719  -1.136946375 -2.125172271  -838.5  81.5 chic
## 1720  -4.937885497 -2.760227474  -837.5  69.0 chic
## 1721 -11.679273050 -1.669885644  -836.5  63.5 chic
## 1722  -9.313342501 -1.802323489  -835.5  56.5 chic
## 1723 -11.903809780 -2.425472621  -834.5  47.0 chic
## 1724 -13.278809780 -3.024673653  -833.5  47.5 chic
## 1725  -7.907944563  2.161071767  -832.5  54.5 chic
## 1726  -4.558974304 -1.895443715  -831.5  58.5 chic
## 1727 -14.528809780 -2.753560333  -830.5  53.5 chic
## 1728 -20.574611230  1.771899954  -829.5  48.0 chic
## 1729 -15.376100450 -0.865546405  -828.5  54.0 chic
## 1730 -11.505713810 -2.743753142  -827.5  50.5 chic
## 1731 -16.918065580 -1.327564526  -826.5  46.5 chic
## 1732 -11.095271500 -0.026596268  -825.5  49.5 chic
## 1733  -3.116277896 -0.758567696  -824.5  53.5 chic
## 1734   2.673184272  1.138741559  -823.5  67.5 chic
## 1735  -8.130236403  3.656843485  -822.5  59.0 chic
## 1736  -5.270645916 -0.388290329  -821.5  69.0 chic
## 1737  -7.272431630 -2.927556710  -820.5  62.5 chic
## 1738  -8.323702915 -3.813089927  -819.5  56.0 chic
## 1739  -6.899711198 -2.178953521  -818.5  48.0 chic
## 1740 -12.909940900  0.142888853  -817.5  44.0 chic
## 1741 -16.678810710 -0.642237773  -816.5  45.0 chic
## 1742  -1.732744354  1.589059658  -815.5  62.5 chic
## 1743  -7.112068254  3.342466034  -814.5  61.5 chic
## 1744 -10.015406350 -0.787143096  -813.5  53.5 chic
## 1745 -15.234037080 -1.703419075  -812.5  56.0 chic
## 1746 -10.542023500 -4.995379104  -811.5  48.5 chic
## 1747 -13.164677920 -6.059793160  -810.5  46.0 chic
## 1748 -19.241000850  0.952059871  -809.5  50.5 chic
## 1749 -17.851589410 -2.152840060  -808.5  41.5 chic
## 1750 -16.103719540  2.007236762  -807.5  45.5 chic
## 1751 -11.072858280  1.174094606  -806.5  61.5 chic
## 1752 -10.709458530 -3.152758989  -805.5  44.5 chic
## 1753 -13.512673090 -1.729806192  -804.5  38.0 chic
## 1754 -15.183624330  0.871914962  -803.5  41.5 chic
## 1755 -13.468993550  1.851878872  -802.5  50.5 chic
## 1756  -1.634208651  0.576380546  -801.5  64.0 chic
## 1757  -5.104379514  1.376859929  -800.5  66.5 chic
## 1758 -13.809881130 -0.957046796  -799.5  68.0 chic
## 1759 -17.587944590 -2.370669528  -798.5  58.5 chic
## 1760 -14.677932280 -4.846770775  -797.5  50.5 chic
## 1761 -15.424790920 -4.598417410  -796.5  50.0 chic
## 1762 -16.833182180 -3.898655088  -795.5  55.0 chic
## 1763 -18.708103270 -1.080505049  -794.5  63.5 chic
## 1764 -17.702411200 -4.842931776  -793.5  46.5 chic
## 1765 -16.088357470 -3.768752978  -792.5  48.0 chic
## 1766  -7.982207735  4.148780593  -791.5  43.5 chic
## 1767  -1.202793350 -5.072457380  -790.5  25.0 chic
## 1768  -2.372012070 -4.965913625  -789.5  18.5 chic
## 1769  -7.969195284 -3.675462096  -788.5  18.5 chic
## 1770 -11.707767340 -3.410056513  -787.5  28.0 chic
## 1771  -4.771138573 -3.866234248  -786.5  28.5 chic
## 1772 -10.084301450 -1.754723594  -785.5  20.0 chic
## 1773 -13.915583020  4.523239643  -784.5  22.0 chic
## 1774 -12.395312540  0.847456840  -783.5  26.0 chic
## 1775 -15.133074800  8.960215205  -782.5  33.0 chic
## 1776 -17.536862090 -1.209653288  -781.5  34.5 chic
## 1777 -16.078328420 -1.609244657  -780.5  35.0 chic
## 1778 -14.622479980  3.168630819  -779.5  45.5 chic
## 1779 -14.508102370 -2.187234581  -778.5  48.0 chic
## 1780  -9.164874540  1.015817321  -777.5  50.0 chic
## 1781   2.177307730 -4.042952512  -776.5  41.0 chic
## 1782  -3.071299151 -0.582927069  -775.5  42.5 chic
## 1783  -9.591491738 -1.508401770  -774.5  57.0 chic
## 1784  -9.297103380 -2.308686301  -773.5  57.0 chic
## 1785 -11.239650330 -0.317411238  -772.5  40.5 chic
## 1786 -15.262798450  9.234112870  -771.5  40.0 chic
## 1787 -16.944533300  6.378262302  -770.5  43.5 chic
## 1788 -12.600320340  0.285227442  -769.5  34.5 chic
## 1789  -3.789382837 -8.206096759  -768.5  23.0 chic
## 1790 -11.346777760 -1.632461236  -767.5  17.5 chic
## 1791 -14.222309620  2.146327623  -766.5  23.0 chic
## 1792 -11.736995410  0.127195194  -765.5  38.5 chic
## 1793  -3.973100787 -3.432803849  -764.5  36.0 chic
## 1794  -8.089697277 -3.095288913  -763.5  49.0 chic
## 1795  -4.274178650  3.532823899  -762.5  43.5 chic
## 1796 -11.420574970 -2.288306840  -761.5  27.0 chic
## 1797  -4.298919224 -3.775493706  -760.5  30.5 chic
## 1798  -9.797700387 -2.912939689  -759.5  22.0 chic
## 1799  -9.684714703 -1.209161681  -758.5   8.0 chic
## 1800 -12.324339260  5.911619244  -757.5  20.5 chic
## 1801 -12.315654720 -0.088036130  -756.5  24.0 chic
## 1802 -12.954957880 -2.561326671  -755.5  44.0 chic
## 1803 -11.772920130 -3.209147152  -754.5  49.0 chic
## 1804 -12.647583420  1.597214730  -753.5  35.5 chic
## 1805 -14.424831410  0.423067366  -752.5  38.5 chic
## 1806 -15.437909400 -1.392643914  -751.5  40.0 chic
## 1807 -14.650470200 -1.763944770  -750.5  49.0 chic
## 1808 -15.241053220 -2.519874802  -749.5  37.5 chic
## 1809  -5.135621604 -6.155801334  -748.5  29.0 chic
## 1810  -6.080102445 -6.241146881  -747.5  18.0 chic
## 1811 -13.202192040 -4.079981796  -746.5  21.0 chic
## 1812  -9.248576677 -5.494815325  -745.5  28.5 chic
## 1813  -7.806993158 -5.903935378  -744.5  15.5 chic
## 1814 -14.308929630 -2.107437059  -743.5  19.0 chic
## 1815 -18.220318740  2.119890375  -742.5  32.5 chic
## 1816 -19.347666840 -0.823455880  -741.5  31.0 chic
## 1817 -14.897189030 -1.596998194  -740.5  32.5 chic
## 1818 -10.894797230 -6.004758803  -739.5  30.5 chic
## 1819 -14.090953730 -3.301426200  -738.5  30.0 chic
## 1820 -14.879657870 -4.445919405  -737.5  30.0 chic
## 1821 -14.343531200  0.040866751  -736.5  35.5 chic
## 1822  -6.214747949 -3.725900049  -735.5  35.5 chic
## 1823  -7.991152063 -2.272304671  -734.5  35.0 chic
## 1824 -13.986634900 -4.588889110  -733.5  32.5 chic
## 1825 -11.507569300 -1.068297913  -732.5  34.0 chic
## 1826 -10.559116800 -0.593265401  -731.5  33.0 chic
## 1827 -15.275677400 -2.725037567  -730.5  33.5 chic
## 1828 -18.832640580  0.760609201  -729.5  39.5 chic
## 1829 -19.479112260 -2.690084203  -728.5  38.5 chic
## 1830 -10.071295700 -3.984467468  -727.5  35.0 chic
## 1831  -9.981123012 -1.218847549  -726.5  35.0 chic
## 1832 -15.072980230 -3.228701639  -725.5  35.0 chic
## 1833 -13.283156250  2.406356196  -724.5  34.5 chic
## 1834 -14.919192590 -3.486483201  -723.5  38.5 chic
## 1835 -16.423167430 -4.966223889  -722.5  38.0 chic
## 1836 -12.651381700 -4.255612502  -721.5  30.0 chic
## 1837 -13.834607640  1.889155687  -720.5  36.0 chic
## 1838 -12.126035600 -3.547259436  -719.5  36.5 chic
## 1839  -8.853130818 -4.886976655  -718.5  32.5 chic
## 1840  -3.668369810 -4.085265619  -717.5  20.5 chic
## 1841  -6.236611987 -5.964081082  -716.5   6.0 chic
## 1842  -9.091048628  8.550759101  -715.5   7.5 chic
## 1843  -4.295135173 -0.471856335  -714.5  21.0 chic
## 1844  -4.107843098 -5.865522509  -713.5   5.5 chic
## 1845 -11.230995700  3.790989815  -712.5  14.5 chic
## 1846  -9.821438826  1.404574289  -711.5  24.0 chic
## 1847 -10.045119670  3.838864408  -710.5  33.5 chic
## 1848 -13.777763480 -0.361718642  -709.5  35.5 chic
## 1849  -8.954260659 -4.175257123  -708.5  28.5 chic
## 1850  -4.831515994 -5.731036410  -707.5  18.5 chic
## 1851  -8.287558850 -1.632771767  -706.5  25.0 chic
## 1852  -9.254637549  1.445571325  -705.5  19.5 chic
## 1853 -12.631750660  3.933092471  -704.5  30.0 chic
## 1854  -9.881829660  2.843841511  -703.5  29.5 chic
## 1855  -9.981201868  8.419348788  -702.5  30.0 chic
## 1856 -13.726761790 -0.615230917  -701.5  33.5 chic
## 1857  -3.553837415 -5.953985622  -700.5  32.0 chic
## 1858   2.621002903 -3.396763910  -699.5  30.5 chic
## 1859 -13.136044850  3.868435290  -698.5  37.5 chic
## 1860 -14.894428260  2.456553904  -697.5  43.5 chic
## 1861  -4.189483738 -5.393131822  -696.5  36.0 chic
## 1862  -6.635140817 -1.727536857  -695.5  32.5 chic
## 1863 -10.003473010 -0.867363155  -694.5  34.5 chic
## 1864  -4.402933193 -5.973365277  -693.5  28.0 chic
## 1865  -1.950937953 -6.328673850  -692.5  18.5 chic
## 1866  -7.277149796 -3.821906320  -691.5  17.5 chic
## 1867 -14.167456860  2.419030136  -690.5  31.0 chic
## 1868  -3.645026371 -4.377608371  -689.5  27.0 chic
## 1869  -0.133508705  2.204660596  -688.5  23.5 chic
## 1870 -13.211422320 -3.101575927  -687.5  30.0 chic
## 1871  -9.641881102 -3.592765557  -686.5  33.0 chic
## 1872  -9.924793809 -2.929743881  -685.5  35.0 chic
## 1873  -6.979067649 -4.402793839  -684.5  34.0 chic
## 1874  -6.117084574 -1.809232831  -683.5  37.5 chic
## 1875 -10.773768780 -3.248005051  -682.5  40.5 chic
## 1876 -11.234968460 -3.547026239  -681.5  37.5 chic
## 1877  -7.596225940 -1.453157199  -680.5  39.5 chic
## 1878  -0.996106334 -2.490561874  -679.5  31.5 chic
## 1879  -4.940531385 -4.062765714  -678.5  42.0 chic
## 1880   1.286990040 -5.295332026  -677.5  37.0 chic
## 1881  -3.819031874 -2.444351277  -676.5  36.0 chic
## 1882  -6.533031203 -4.163365564  -675.5  33.5 chic
## 1883  -4.130100408 -3.687976960  -674.5  33.5 chic
## 1884  -8.056281570 -3.866206594  -673.5  39.5 chic
## 1885  -3.064350420 -2.687750719  -672.5  39.5 chic
## 1886   6.270723861 -3.289891213  -671.5  30.0 chic
## 1887   0.700969914 -1.147885985  -670.5  54.0 chic
## 1888  -3.509805012  0.597731795  -669.5  45.5 chic
## 1889  -8.083390644 -3.412530151  -668.5  38.0 chic
## 1890  -7.502699966  4.908198631  -667.5  49.0 chic
## 1891 -10.708562500  5.793343336  -666.5  53.0 chic
## 1892  -7.712089930 -1.478357540  -665.5  55.5 chic
## 1893 -11.531637940 -3.487791327  -664.5  48.0 chic
## 1894  -4.896560567 -1.631560230  -663.5  52.5 chic
## 1895   1.431343115 -1.073959791  -662.5  44.0 chic
## 1896   5.417511633 -4.634172931  -661.5  20.5 chic
## 1897   4.755903222 -2.915622044  -660.5  19.5 chic
## 1898   2.991782470 -2.465094804  -659.5  22.5 chic
## 1899  -0.072929352 -1.964681514  -658.5  25.5 chic
## 1900   4.283309847 -3.579782761  -657.5  29.0 chic
## 1901   3.092383199 -2.543513825  -656.5  29.0 chic
## 1902  -2.079327685 -0.848228436  -655.5  34.5 chic
## 1903   4.840678477 -1.238910729  -654.5  38.0 chic
## 1904  10.158510620 -1.871499302  -653.5  34.0 chic
## 1905  12.752883614 -3.871552837  -652.5  36.0 chic
## 1906   0.675920912 -1.411461049  -651.5  35.0 chic
## 1907  13.954485775 -3.903091479  -650.5  30.5 chic
## 1908   9.619522302 -3.313773538  -649.5  27.5 chic
## 1909  -1.448891247  2.166283039  -648.5  30.5 chic
## 1910   1.048794653  2.369885187  -647.5  43.0 chic
## 1911  -3.311547807  1.951169024  -646.5  44.5 chic
## 1912  -1.250854639 -2.116844629  -645.5  41.5 chic
## 1913   8.203462763 -2.831772883  -644.5  34.0 chic
## 1914   0.110357028  6.671263625  -643.5  36.0 chic
## 1915  12.055324126 -0.431955964  -642.5  39.5 chic
## 1916   3.545045424 -0.304259107  -641.5  42.0 chic
## 1917  -0.106922438  2.973223147  -640.5  41.5 chic
## 1918   2.926942327 -3.043090881  -639.5  33.5 chic
## 1919  -1.678711595 -1.533167332  -638.5  31.5 chic
## 1920  -5.646447664  0.879079757  -637.5  38.0 chic
## 1921   8.985083396 -2.666466713  -636.5  36.5 chic
## 1922  -2.548421427  4.819795998  -635.5  38.5 chic
## 1923  -1.000670368  3.922780185  -634.5  49.5 chic
## 1924  -4.417604605  7.233061099  -633.5  52.0 chic
## 1925  -2.198864582  3.320320960  -632.5  50.5 chic
## 1926   7.443480549 -3.374161658  -631.5  48.0 chic
## 1927  -0.511325940  2.151217975  -630.5  57.5 chic
## 1928  -3.999442401 -1.020582282  -629.5  49.5 chic
## 1929  12.106834679 -4.665828325  -628.5  37.0 chic
## 1930  -5.937170013  1.681537780  -627.5  38.0 chic
## 1931  -9.633427643 -0.201891217  -626.5  42.0 chic
## 1932 -10.717697730  1.755405569  -625.5  48.5 chic
## 1933  -0.255827083 -1.005602298  -624.5  53.0 chic
## 1934   4.428853558 -2.924745643  -623.5  41.5 chic
## 1935 -11.143177770 -1.413795979  -622.5  51.0 chic
## 1936   4.447563549 -1.090949938  -621.5  65.5 chic
## 1937   1.243520754 -1.699069951  -620.5  66.0 chic
## 1938  -7.117403184  1.621669320  -619.5  48.0 chic
## 1939  -3.770935090 -3.210713653  -618.5  43.0 chic
## 1940 -10.684170460  1.224020199  -617.5  48.5 chic
## 1941 -10.048937650 -1.003547453  -616.5  44.5 chic
## 1942  -2.314506755 -0.586795828  -615.5  43.5 chic
## 1943   0.012675232 -2.204177705  -614.5  46.5 chic
## 1944   4.732445957 -3.707494992  -613.5  40.5 chic
## 1945  -1.730459772  4.681938167  -612.5  44.0 chic
## 1946  -4.262310493 -0.406884397  -611.5  58.5 chic
## 1947  -3.181823802 -1.483835797  -610.5  49.5 chic
## 1948  13.176227671  2.741868906  -609.5  64.5 chic
## 1949   7.454816502 -2.374161658  -608.5  64.0 chic
## 1950  -2.377250959 -1.207494992  -607.5  48.5 chic
## 1951   0.807759921 -3.396761165  -606.5  44.0 chic
## 1952  11.542261240 -3.946168039  -605.5  43.5 chic
## 1953   0.174567914 -0.076439282  -604.5  44.5 chic
## 1954   1.427857960  7.000100061  -603.5  52.0 chic
## 1955  13.887153567  7.333547660  -602.5  56.0 chic
## 1956  25.349495560 -1.517915787  -601.5  58.5 chic
## 1957  21.899769046  1.504473075  -600.5  65.5 chic
## 1958  14.033465501 -2.700522321  -599.5  72.0 chic
## 1959   7.167309015  0.173707799  -598.5  69.0 chic
## 1960   3.917265121 -4.652859580  -597.5  55.0 chic
## 1961  -5.366360259 -0.913864222  -596.5  54.0 chic
## 1962  -1.626625728  9.250838342  -595.5  60.5 chic
## 1963  20.170499616  2.299366867  -594.5  73.0 chic
## 1964  15.172510653  2.699753083  -593.5  67.5 chic
## 1965   2.605090302 -3.028114762  -592.5  52.0 chic
## 1966   0.690418121  5.209171675  -591.5  57.5 chic
## 1967  16.788395174  2.850123087  -590.5  67.0 chic
## 1968  14.052743909  5.640453023  -589.5  71.0 chic
## 1969   4.559666446  6.377952022  -588.5  73.5 chic
## 1970  -0.961850164 -2.343795595  -587.5  55.0 chic
## 1971  10.455252967 -3.213198349  -586.5  40.5 chic
## 1972   9.246483169 -2.526044204  -585.5  41.0 chic
## 1973   4.658347435 -2.984894800  -584.5  44.0 chic
## 1974  -3.104702306 -0.091386317  -583.5  48.0 chic
## 1975   1.402191611  5.748984494  -582.5  50.5 chic
## 1976  11.606213370  3.375838342  -581.5  55.0 chic
## 1977  19.965934879 -2.609992935  -580.5  58.0 chic
## 1978  15.329816502 -1.449734982  -579.5  60.0 chic
## 1979   1.550169864  1.487495649  -578.5  59.0 chic
## 1980   2.977220314  0.749442244  -577.5  61.5 chic
## 1981  23.841879554  2.847107474  -576.5  65.0 chic
## 1982   6.593200478  0.072080587  -575.5  65.5 chic
## 1983  13.583921504 -1.767906423  -574.5  67.5 chic
## 1984  13.528644953 -0.164688114  -573.5  67.5 chic
## 1985  13.182433309 -4.991688262  -572.5  66.5 chic
## 1986   4.661118032 -3.364771865  -571.5  62.0 chic
## 1987   6.570061131 -3.360098969  -570.5  65.0 chic
## 1988   8.077719661 -2.882210946  -569.5  64.0 chic
## 1989   4.719524627 -2.317605468  -568.5  67.0 chic
## 1990  18.207347333  0.082556652  -567.5  69.0 chic
## 1991  26.384806312  0.500838342  -566.5  73.5 chic
## 1992  22.481358875  0.684106861  -565.5  75.5 chic
## 1993  12.038657461  1.581175445  -564.5  71.0 chic
## 1994  26.040184051  3.123056772  -563.5  75.5 chic
## 1995   7.816493859 -2.635930156  -562.5  75.5 chic
## 1996   8.118383448 -2.295921029  -561.5  73.0 chic
## 1997   0.208929943 -5.200248615  -560.5  59.5 chic
## 1998   1.126970513 -5.432466823  -559.5  51.5 chic
## 1999  -0.260844616 -5.204727097  -558.5  49.0 chic
## 2000  -4.910029335  4.000838342  -557.5  52.5 chic
## 2001   9.693714083  0.454313767  -556.5  66.0 chic
## 2002  12.338926384 -0.233569953  -555.5  62.5 chic
## 2003   1.736162117  0.228616485  -554.5  64.0 chic
## 2004   6.013289683 -3.288631207  -553.5  61.5 chic
## 2005   1.643442791 -5.124161658  -552.5  58.0 chic
## 2006   5.852620312  3.447053037  -551.5  62.0 chic
## 2007  12.511191294  3.118925639  -550.5  71.5 chic
## 2008   9.147205006 -2.890551671  -549.5  66.5 chic
## 2009  30.744002472  6.138605012  -548.5  74.5 chic
## 2010   3.702589693 -2.990373315  -547.5  77.0 chic
## 2011  -1.492208570 -2.891077670  -546.5  67.0 chic
## 2012   9.855118527 -1.874161658  -545.5  71.0 chic
## 2013   1.661599975 -4.267308991  -544.5  68.5 chic
## 2014   0.328371066 -0.962856937  -543.5  66.0 chic
## 2015  -2.030475373 -2.947800359  -542.5  67.5 chic
## 2016   8.986015106 -2.827975410  -541.5  79.5 chic
## 2017  -6.320245521 -0.005340041  -540.5  74.0 chic
## 2018   4.618069191 -3.479191515  -539.5  76.5 chic
## 2019   6.468198290 -2.751202852  -538.5  73.0 chic
## 2020  -1.268236450 -4.248469177  -537.5  73.5 chic
## 2021   1.380593051 -2.640015638  -536.5  69.5 chic
## 2022  -2.242124744 -2.871294176  -535.5  65.0 chic
## 2023   1.674070082 -2.165828325  -534.5  68.0 chic
## 2024  -6.417207737  0.849057318  -533.5  68.0 chic
## 2025  -3.792441819 -0.899629946  -532.5  74.0 chic
## 2026  -4.558710546 -1.457494992  -531.5  68.0 chic
## 2027   6.327546156 -1.290828325  -530.5  72.5 chic
## 2028  -1.740025126 -2.207494992  -529.5  68.0 chic
## 2029   0.174567914 -3.332494992  -528.5  62.0 chic
## 2030   0.588926384 -1.907960895  -527.5  63.5 chic
## 2031   0.827335806 -4.109114178  -526.5  62.5 chic
## 2032   6.424348279 -4.699581886  -525.5  67.0 chic
## 2033  -1.655475373 -3.068349989  -524.5  67.5 chic
## 2034  -1.283765419 -4.423570384  -523.5  74.0 chic
## 2035  -2.772910572 -3.463760377  -522.5  71.5 chic
## 2036   0.452335806 -1.116650578  -521.5  71.5 chic
## 2037  -4.825651721 -3.783590224  -520.5  66.5 chic
## 2038  -6.045183498 -2.459232291  -519.5  61.5 chic
## 2039  -0.439619250 -3.364182697  -518.5  67.0 chic
## 2040   4.091014946 -2.710551410  -517.5  68.0 chic
## 2041   3.519205136 -2.211250255  -516.5  69.0 chic
## 2042  -8.025239309 -2.783013351  -515.5  65.5 chic
## 2043  -6.167600420 -3.783505849  -514.5  62.5 chic
## 2044   3.341234581 -2.970725140  -513.5  63.0 chic
## 2045  10.354737543 -1.486894629  -512.5  67.5 chic
## 2046  12.095762301  0.771552859  -511.5  68.5 chic
## 2047  11.692855726 -4.329271727  -510.5  75.5 chic
## 2048   8.243692960 -3.210778576  -509.5  80.0 chic
## 2049   4.601888143 -3.064963620  -508.5  79.0 chic
## 2050   4.393714083 -3.883230149  -507.5  70.0 chic
## 2051 -10.343063380 -3.385723178  -506.5  62.0 chic
## 2052  -0.240980701 -4.353639540  -505.5  60.5 chic
## 2053   0.126047770 -3.153126217  -504.5  61.0 chic
## 2054   6.924348279 -3.811802016  -503.5  62.0 chic
## 2055   3.743383448 -4.374161658  -502.5  63.0 chic
## 2056   2.663149836  1.023093330  -501.5  63.0 chic
## 2057   2.904654392 -0.354157368  -500.5  66.0 chic
## 2058  -6.170183498 -4.120378549  -499.5  63.0 chic
## 2059  -5.045183498 -2.857986678  -498.5  62.0 chic
## 2060   3.268714083  0.553701794  -497.5  64.5 chic
## 2061  12.715485878 -2.387127903  -496.5  67.0 chic
## 2062  12.044695473 -1.790828325  -495.5  75.0 chic
## 2063   1.452335806 -2.749161658  -494.5  78.5 chic
## 2064   5.185380750 -2.924954686  -493.5  79.0 chic
## 2065  -6.605281576 -4.162065520  -492.5  66.5 chic
## 2066 -11.113808710 -5.669331299  -491.5  59.5 chic
## 2067  -7.505997528 -4.278677161  -490.5  59.5 chic
## 2068   3.549156998 -0.652322167  -489.5  65.0 chic
## 2069   2.716234581 -3.249161658  -488.5  66.0 chic
## 2070  -6.755997528 -2.246402853  -487.5  63.5 chic
## 2071  -3.128516831  1.068991193  -486.5  63.0 chic
## 2072  -2.738808706 -2.954148651  -485.5  70.0 chic
## 2073  -3.272952584 -2.915445122  -484.5  67.5 chic
## 2074   1.209316393 -0.560079825  -483.5  65.5 chic
## 2075   5.231545988 -3.211032709  -482.5  70.5 chic
## 2076   9.481763550 -2.860193980  -481.5  75.0 chic
## 2077  11.377596883 -4.069822663  -480.5  73.5 chic
## 2078  -2.964640373  0.084171675  -479.5  64.0 chic
## 2079 -14.589290240 -3.825391454  -478.5  63.0 chic
## 2080  -8.940780136 -2.478548211  -477.5  60.0 chic
## 2081  -9.935475274 -4.080285240  -476.5  58.0 chic
## 2082   1.466392511 -3.207494992  -475.5  62.0 chic
## 2083  14.369002472  0.418027132  -474.5  67.5 chic
## 2084   3.946469171 -1.878458820  -473.5  72.5 chic
## 2085   0.252719171 -1.207494992  -472.5  76.5 chic
## 2086  14.024594171 -0.999891893  -471.5  77.0 chic
## 2087   1.610828975 -1.132083674  -470.5  71.5 chic
## 2088   0.618383449 -1.939497912  -469.5  62.0 chic
## 2089  -4.461850164  0.582997778  -468.5  53.5 chic
## 2090  -1.481549420 -2.671809702  -467.5  59.0 chic
## 2091 -14.257834320 -3.232307612  -466.5  64.0 chic
## 2092  -8.245751145 -3.790828325  -465.5  51.5 chic
## 2093  -7.956141125 -2.350666164  -464.5  49.5 chic
## 2094  -6.435307792 -0.123280761  -463.5  53.5 chic
## 2095  -3.327284568  3.693419153  -462.5  59.5 chic
## 2096  -5.531573160 -3.199759368  -461.5  63.0 chic
## 2097  -6.537321193 -3.528515237  -460.5  59.0 chic
## 2098  -8.921170264 -1.146500249  -459.5  49.5 chic
## 2099 -12.198189410 -0.332955480  -458.5  49.5 chic
## 2100  -9.453398879  3.061764901  -457.5  53.5 chic
## 2101  -3.942835488  6.459171675  -456.5  58.5 chic
## 2102   2.006899380  4.913831556  -455.5  63.5 chic
## 2103   9.647140706  0.278012255  -454.5  66.0 chic
## 2104   3.677372022 -2.634653112  -453.5  59.0 chic
## 2105  -6.675238011 -2.031227810  -452.5  53.0 chic
## 2106  -8.411094443  1.058247550  -451.5  53.0 chic
## 2107   1.037862721  1.709171675  -450.5  58.0 chic
## 2108  -2.639778531 -0.832494992  -449.5  57.5 chic
## 2109 -12.054752440 -2.532633037  -448.5  49.0 chic
## 2110  -7.990479588 -0.817198188  -447.5  51.5 chic
## 2111  -6.032756967 -2.553389463  -446.5  51.5 chic
## 2112  -4.905673634 -1.113440205  -445.5  53.5 chic
## 2113  -9.867370294  1.705093219  -444.5  47.0 chic
## 2114  -8.858189435  1.640940492  -443.5  55.5 chic
## 2115 -13.424153490 -5.481440032  -442.5  50.0 chic
## 2116 -12.544886340 -3.636742199  -441.5  40.5 chic
## 2117 -10.517894920 -1.807820763  -440.5  38.5 chic
## 2118  -4.758210389 -2.764099302  -439.5  35.0 chic
## 2119 -10.622353960  2.173211095  -438.5  33.0 chic
## 2120  -9.664217169 -1.384072493  -437.5  45.5 chic
## 2121 -10.149262550  1.006604446  -436.5  47.0 chic
## 2122  -7.733743013  0.871849402  -435.5  57.5 chic
## 2123  -2.353324624  0.734262368  -434.5  67.0 chic
## 2124  -0.135716976 -4.494961377  -433.5  51.0 chic
## 2125  -9.402623850 -1.853984154  -432.5  49.0 chic
## 2126  -3.308956494 -2.120661198  -431.5  49.0 chic
## 2127 -14.005997530  2.539627664  -430.5  46.5 chic
## 2128 -11.912579410  3.425352900  -429.5  50.5 chic
## 2129  -2.301538447 -4.961425014  -428.5  45.0 chic
## 2130  -4.933208493 -1.220964569  -427.5  45.0 chic
## 2131  -6.629288077  1.078328461  -426.5  44.0 chic
## 2132  -7.770791598 -4.029823398  -425.5  45.5 chic
## 2133 -12.610409790 -2.811736784  -424.5  48.0 chic
## 2134 -10.578163180 -4.352833835  -423.5  37.0 chic
## 2135 -11.203282450 -4.898267625  -422.5  35.5 chic
## 2136 -12.414759560 -4.815103107  -421.5  32.5 chic
## 2137 -10.844164550 -4.844199547  -420.5  34.0 chic
## 2138  -8.734214404 -1.960747883  -419.5  34.0 chic
## 2139 -11.325231470 -0.183059451  -418.5  38.0 chic
## 2140  -7.485975089 -1.628991215  -417.5  48.0 chic
## 2141  -9.851998748 -2.339604975  -416.5  49.5 chic
## 2142 -10.133052030 -4.061072902  -415.5  44.5 chic
## 2143 -10.613500540 -4.336094524  -414.5  40.0 chic
## 2144  -6.048859910 -3.198638130  -413.5  30.5 chic
## 2145 -11.129695480 -3.172283656  -412.5  28.0 chic
## 2146  -9.746371580 -4.186647155  -411.5  29.0 chic
## 2147 -11.670397640 -0.589211558  -410.5  36.5 chic
## 2148 -11.885929090 -1.073285147  -409.5  38.5 chic
## 2149  -5.785249595 -2.240532995  -408.5  39.0 chic
## 2150  -5.484795992  1.782941183  -407.5  40.5 chic
## 2151 -10.496176330  3.810012154  -406.5  52.0 chic
## 2152 -11.456899670 -4.177759186  -405.5  48.5 chic
## 2153  -4.999017751 -4.637363981  -404.5  41.0 chic
## 2154 -12.315042210 -4.757514496  -403.5  38.0 chic
## 2155  -9.657368693 -3.682423121  -402.5  39.5 chic
## 2156 -14.195691570 -2.820209540  -401.5  43.5 chic
## 2157  -9.108654530 -4.289729057  -400.5  36.5 chic
## 2158 -10.025765550 -2.354612399  -399.5  29.5 chic
## 2159 -10.909040130 -1.938275850  -398.5  30.0 chic
## 2160  -9.362956085 -2.021609183  -397.5  35.0 chic
## 2161 -11.932844340 -3.702298629  -396.5  33.5 chic
## 2162 -12.029226190 -2.264323100  -395.5  32.0 chic
## 2163 -10.501532880 -2.674005541  -394.5  32.5 chic
## 2164 -10.052324010 -2.706097396  -393.5  28.0 chic
## 2165 -11.041675870 -0.109360589  -392.5  25.5 chic
## 2166 -10.271421240 -1.969634561  -391.5  19.5 chic
## 2167 -12.270606020  3.955479895  -390.5  23.0 chic
## 2168 -11.433672400 -0.882931924  -389.5  27.0 chic
## 2169 -11.192333450 -3.098159042  -388.5  26.5 chic
## 2170 -13.682170030 -0.086456894  -387.5  27.5 chic
## 2171 -14.265593940  5.589859055  -386.5  32.0 chic
## 2172 -14.265470450  3.045311835  -385.5  33.0 chic
## 2173  -8.085388416 -1.241902777  -384.5  32.0 chic
## 2174  -6.700368578 -3.343347720  -383.5  32.0 chic
## 2175 -13.275908890 -2.740647069  -382.5  29.5 chic
## 2176 -12.763939000  2.013469165  -381.5  44.0 chic
## 2177 -13.456810390 -2.878795407  -380.5  37.5 chic
## 2178 -10.398336730 -1.096468987  -379.5  30.5 chic
## 2179 -13.280076350  1.975659469  -378.5  30.0 chic
## 2180 -14.001184190  3.289032222  -377.5  31.0 chic
## 2181 -10.649396840 -2.511445532  -376.5  19.5 chic
## 2182 -13.827509160  7.348081200  -375.5  26.0 chic
## 2183 -13.748422840  2.989971189  -374.5  33.0 chic
## 2184 -12.304932640  1.082972961  -373.5  21.5 chic
## 2185  -8.227302287  2.379776614  -372.5  10.0 chic
## 2186  -1.836806887 -1.425002789  -371.5  18.0 chic
## 2187  -7.475296042  0.033355133  -370.5  14.5 chic
## 2188 -10.637311730  5.382556046  -369.5  30.5 chic
## 2189 -13.926541520  1.431456005  -368.5  38.5 chic
## 2190 -13.918126210 -2.165968541  -367.5  41.5 chic
## 2191 -13.867381330 -0.799290305  -366.5  45.5 chic
## 2192 -13.827870360 -0.972602417  -365.5  26.0 chic
## 2193 -10.066535950  0.780479774  -364.5  14.0 chic
## 2194 -13.355691180  1.281913192  -363.5  25.0 chic
## 2195 -13.751845510 -0.966552189  -362.5  40.5 chic
## 2196 -13.935271950 -4.096607935  -361.5  38.5 chic
## 2197 -13.969713410  1.671772615  -360.5  27.5 chic
## 2198 -13.364281100  4.601464503  -359.5  21.5 chic
## 2199 -13.496845810  2.644275859  -358.5  24.5 chic
## 2200  -0.603442455 -2.244013426  -357.5  21.5 chic
## 2201  10.108985233 -5.069168662  -356.5  24.5 chic
## 2202   9.221703323 -3.618838780  -355.5  24.5 chic
## 2203  -1.229340774  6.267650421  -354.5  26.5 chic
## 2204 -13.432107510  8.659096951  -353.5  30.5 chic
## 2205 -12.127977460  7.509038028  -352.5  27.5 chic
## 2206  -9.768298655  6.780677403  -351.5  21.5 chic
## 2207  -9.067550925 -0.068696090  -350.5  18.0 chic
## 2208 -12.190080180 14.304128424  -349.5  22.0 chic
## 2209   3.187713119  3.445517586  -348.5  20.5 chic
## 2210  -8.506420959  5.608302974  -347.5  18.0 chic
## 2211 -10.203789930  9.471712131  -346.5  18.5 chic
## 2212 -11.787708300 18.128785958  -345.5  27.0 chic
## 2213 -14.475924260  0.923702796  -344.5  35.5 chic
## 2214  -8.964619572  3.412662995  -343.5  34.0 chic
## 2215  -6.114515891  5.282662712  -342.5  36.5 chic
## 2216   0.083587644  0.322617049  -341.5  28.5 chic
## 2217  -5.862562990  3.818621616  -340.5  23.0 chic
## 2218  -8.742218484  6.951793837  -339.5  30.0 chic
## 2219  -6.141132511 -3.033292647  -338.5  31.5 chic
## 2220  -8.799056026  0.323602936  -337.5  30.5 chic
## 2221  -4.316183228 -0.269251552  -336.5  15.5 chic
## 2222  -0.997467759  4.434790150  -335.5  25.5 chic
## 2223  -1.387600791  0.760475082  -334.5  38.5 chic
## 2224   4.597283855 -2.959592331  -333.5  30.5 chic
## 2225  -8.477728010  0.159447684  -332.5  28.5 chic
## 2226 -12.263295150 11.944005592  -331.5  35.5 chic
## 2227  -8.635819508 15.313754451  -330.5  38.0 chic
## 2228  -6.932450543  7.160319866  -329.5  36.5 chic
## 2229   3.283319334 -3.680887847  -328.5  29.0 chic
## 2230  -7.496539757 -1.374626530  -327.5  32.0 chic
## 2231   0.458954342 -3.874440723  -326.5  30.0 chic
## 2232 -14.202022510  1.432431786  -325.5  32.5 chic
## 2233  -7.434515821  0.313148528  -324.5  35.0 chic
## 2234   0.575869114 -2.603355507  -323.5  32.0 chic
## 2235  -1.100763659 -3.868944425  -322.5  29.0 chic
## 2236  -6.260219040 -1.715335206  -321.5  28.5 chic
## 2237   3.921828710 -1.483029270  -320.5  27.0 chic
## 2238  -4.455007837  4.778776731  -319.5  24.5 chic
## 2239  -1.351128131 -3.050005617  -318.5  25.5 chic
## 2240  -1.146567704 -1.971615673  -317.5  10.5 chic
## 2241  -1.099309079 -1.426934885  -316.5   7.5 chic
## 2242  -7.018917991  5.586742846  -315.5  15.0 chic
## 2243  -2.532946719  2.807731877  -314.5  25.5 chic
## 2244  -4.654435483  1.728206255  -313.5  29.0 chic
## 2245  -3.272520830  0.283133771  -312.5  22.0 chic
## 2246  -1.903334843  0.489526465  -311.5  12.0 chic
## 2247  -6.310156067 -0.758635850  -310.5   5.0 chic
## 2248  -2.595739171  3.755312069  -309.5  10.0 chic
## 2249   4.101944184 -1.448809236  -308.5  22.0 chic
## 2250  -6.557076636  4.937001269  -307.5  19.0 chic
## 2251   9.376802309  8.243557132  -306.5  20.0 chic
## 2252   8.486770710  9.867896090  -305.5  34.5 chic
## 2253  -5.742586936  4.336617235  -304.5  38.5 chic
## 2254  15.763360342 -0.222654995  -303.5  38.5 chic
## 2255  16.909171330 -4.318386962  -302.5  34.5 chic
## 2256   2.391077892 -2.582891121  -301.5  34.5 chic
## 2257  -5.936124430 -1.237397772  -300.5  36.0 chic
## 2258  -3.991034029  2.542024919  -299.5  34.0 chic
## 2259  -3.755311550 -5.100131271  -298.5  38.0 chic
## 2260   4.369733968 -1.592179594  -297.5  36.5 chic
## 2261  -7.052147027 -2.511269071  -296.5  32.5 chic
## 2262  -5.321007031 -4.369238785  -295.5  26.0 chic
## 2263  -3.326928203 -2.858253458  -294.5  22.0 chic
## 2264   2.337394583 -5.262503625  -293.5  17.0 chic
## 2265   1.738637677 -4.186823088  -292.5  14.5 chic
## 2266  -0.933629750 -2.328569648  -291.5  30.5 chic
## 2267   2.354160066 -4.173987275  -290.5  37.5 chic
## 2268  12.490128989 -4.357252483  -289.5  21.5 chic
## 2269  10.518695699 -3.854942136  -288.5  23.0 chic
## 2270  -8.791348722  0.077953813  -287.5  29.5 chic
## 2271 -10.571607720  2.635945223  -286.5  35.0 chic
## 2272  -5.699212632 -1.642465156  -285.5  35.0 chic
## 2273   5.424585387 -3.926383634  -284.5  35.0 chic
## 2274  -9.603922302 -3.655647376  -283.5  39.5 chic
## 2275  -9.498636689  1.259367737  -282.5  38.0 chic
## 2276  -7.163070827  0.913818654  -281.5  40.0 chic
## 2277   7.432082908 -3.859431384  -280.5  39.0 chic
## 2278  13.352663019 -4.099257440  -279.5  37.5 chic
## 2279  10.914932816 -3.179936110  -278.5  41.5 chic
## 2280   8.435075542 -1.855915360  -277.5  50.0 chic
## 2281   7.483648286  3.370023607  -276.5  50.0 chic
## 2282   8.656810894 -3.715831771  -275.5  44.0 chic
## 2283   9.770035010 -4.078028932  -274.5  31.0 chic
## 2284  -0.767856878 -3.514539065  -273.5  31.0 chic
## 2285   1.254586257 -2.000608155  -272.5  33.5 chic
## 2286   5.819854147 -0.123420393  -271.5  31.5 chic
## 2287   4.962845143 -2.355593429  -270.5  36.5 chic
## 2288   4.766796526 -2.007169667  -269.5  40.0 chic
## 2289   3.239140928  1.478692892  -268.5  47.5 chic
## 2290  -3.408610053 -4.071250495  -267.5  55.5 chic
## 2291   2.133056613 -2.711975086  -266.5  48.5 chic
## 2292   6.557202459 -2.795308419  -265.5  46.0 chic
## 2293   3.108189195 -3.345471997  -264.5  43.5 chic
## 2294  11.029033445 -4.799720219  -263.5  41.0 chic
## 2295   7.560701605 -1.686851282  -262.5  39.5 chic
## 2296   1.759698618 -0.343003046  -261.5  44.5 chic
## 2297  -7.325276720 -3.694930691  -260.5  43.5 chic
## 2298  -0.690742776 -3.486966689  -259.5  38.0 chic
## 2299   4.086371859 -0.266827937  -258.5  43.5 chic
## 2300   8.307593358 -0.614034843  -257.5  50.5 chic
## 2301  -1.978499833 -1.954279721  -256.5  51.0 chic
## 2302   8.023052571 -5.023518078  -255.5  38.0 chic
## 2303   2.360922120 -3.029302805  -254.5  42.0 chic
## 2304   0.344273975  2.395827822  -253.5  48.0 chic
## 2305   5.959141713 -0.034674109  -252.5  52.5 chic
## 2306  17.834141713  0.073656242  -251.5  64.5 chic
## 2307  13.748588863 -4.088744172  -250.5  50.0 chic
## 2308   4.026771807 -4.295884711  -249.5  40.0 chic
## 2309   5.911949464 -2.220555728  -248.5  49.5 chic
## 2310   4.952969353  3.161647398  -247.5  64.5 chic
## 2311  -8.862756829 -4.068405138  -246.5  52.5 chic
## 2312  -5.205687886  2.820070134  -245.5  57.0 chic
## 2313  12.357898931  0.595657386  -244.5  59.0 chic
## 2314   6.574723280 -3.191511564  -243.5  61.0 chic
## 2315   4.559394922 -3.445434353  -242.5  61.5 chic
## 2316  -1.516917212 -3.542518051  -241.5  60.5 chic
## 2317  -5.907129247 -1.370678408  -240.5  61.0 chic
## 2318  -0.648656572  7.492652830  -239.5  62.5 chic
## 2319  14.486331857  2.741811392  -238.5  66.5 chic
## 2320  27.478286576  7.611331940  -237.5  72.5 chic
## 2321  20.047566989  1.816354771  -236.5  74.5 chic
## 2322  17.955847201  1.974100230  -235.5  75.0 chic
## 2323  12.235490316 -2.192338126  -234.5  63.5 chic
## 2324   2.964633093 -2.772310117  -233.5  62.5 chic
## 2325  -1.881988394 -3.889778573  -232.5  48.5 chic
## 2326   4.940443343  2.550326158  -231.5  61.0 chic
## 2327  10.869862718 -2.940473427  -230.5  62.0 chic
## 2328  10.699398797  0.666777540  -229.5  56.0 chic
## 2329   0.314431350 -0.734437548  -228.5  52.0 chic
## 2330   0.425232917 -1.875047250  -227.5  54.5 chic
## 2331  -2.070480651 -2.977001617  -226.5  49.5 chic
## 2332  -6.923517319 -0.642205381  -225.5  50.5 chic
## 2333  -0.675281279  1.048520472  -224.5  56.0 chic
## 2334   9.613656483 -0.045474914  -223.5  60.5 chic
## 2335  11.883353155 -3.924932899  -222.5  63.5 chic
## 2336   1.095187695 -3.607296703  -221.5  60.0 chic
## 2337  -1.572067958 -2.922278236  -220.5  58.5 chic
## 2338   1.659708828 -1.175611901  -219.5  61.5 chic
## 2339   7.345761716  4.807491763  -218.5  65.0 chic
## 2340   1.146978567 -1.929036558  -217.5  57.0 chic
## 2341   5.110490316 -4.903872349  -216.5  51.0 chic
## 2342  -1.900768736 -4.025764894  -215.5  58.5 chic
## 2343   7.982821738 -3.984752004  -214.5  53.5 chic
## 2344  -4.203041754 -2.643041420  -213.5  51.0 chic
## 2345   0.506404380 -3.465503699  -212.5  54.5 chic
## 2346   1.823415936 -4.023959720  -211.5  50.5 chic
## 2347   1.952342208 -4.583652488  -210.5  49.5 chic
## 2348   4.132674268 -3.046693737  -209.5  58.5 chic
## 2349  10.066157832  1.820776667  -208.5  61.0 chic
## 2350 -10.888528150 -2.566286929  -207.5  64.0 chic
## 2351  -4.300228741 -2.975443119  -206.5  72.0 chic
## 2352   8.037204689 -2.368006088  -205.5  71.0 chic
## 2353   6.462103667  2.092753985  -204.5  68.5 chic
## 2354  20.164527426 -1.089728069  -203.5  68.0 chic
## 2355  15.862371249  0.459714827  -202.5  67.0 chic
## 2356  14.714555526 -0.980302035  -201.5  70.5 chic
## 2357   7.217434248 -3.031768143  -200.5  72.0 chic
## 2358   2.058015708 -3.868072290  -199.5  62.5 chic
## 2359  11.853698735 -3.187836486  -198.5  63.5 chic
## 2360  24.872513868 -1.033003472  -197.5  78.5 chic
## 2361  10.858209568 -2.868882787  -196.5  69.5 chic
## 2362   7.301615983 -3.151782488  -195.5  70.5 chic
## 2363   5.793521514 -3.792898658  -194.5  69.5 chic
## 2364  -3.745269686 -1.469141086  -193.5  70.5 chic
## 2365   7.110163001 -0.653810061  -192.5  72.0 chic
## 2366  21.069842776 -2.505278009  -191.5  71.5 chic
## 2367  11.976680535 -2.153004443  -190.5  78.5 chic
## 2368  -1.037579552 -2.176566099  -189.5  71.5 chic
## 2369   1.278507601 -1.019087072  -188.5  70.5 chic
## 2370   8.637282609 -3.251936048  -187.5  74.5 chic
## 2371   1.648431409 -4.057625372  -186.5  65.5 chic
## 2372   5.287846246 -1.684100178  -185.5  64.5 chic
## 2373  -0.844770635 -1.859989373  -184.5  64.0 chic
## 2374  -5.853941360 -2.156234022  -183.5  68.5 chic
## 2375  -6.866202983 -1.607863311  -182.5  73.0 chic
## 2376   9.773375772 -2.465363299  -181.5  79.0 chic
## 2377  11.200057283 -2.727539183  -180.5  81.5 chic
## 2378  16.792155340 -3.148375776  -179.5  81.0 chic
## 2379   7.718774062 -2.324494503  -178.5  73.5 chic
## 2380  -1.155148273  1.293609870  -177.5  75.0 chic
## 2381   5.179519349 -0.480929453  -176.5  79.5 chic
## 2382   4.475604954 -1.053626808  -175.5  81.0 chic
## 2383   3.021284634 -0.907245896  -174.5  78.5 chic
## 2384   7.617515914 -1.569077416  -173.5  73.5 chic
## 2385   8.222295046 -1.884375770  -172.5  72.0 chic
## 2386  13.813645234 -1.705738569  -171.5  71.0 chic
## 2387  11.995884301 -3.158721617  -170.5  70.0 chic
## 2388  13.482309967 -3.637389792  -169.5  69.5 chic
## 2389  18.442672900 -2.473893844  -168.5  72.5 chic
## 2390  11.083144278 -0.412374915  -167.5  74.5 chic
## 2391   7.286524471 -2.732850155  -166.5  79.0 chic
## 2392  11.516272509 -2.220245564  -165.5  76.0 chic
## 2393   5.348485443 -0.423803194  -164.5  71.5 chic
## 2394   0.814318428 -3.224713145  -163.5  69.0 chic
## 2395   6.621644744  1.585635277  -162.5  66.0 chic
## 2396   7.897657609  2.587960925  -161.5  72.5 chic
## 2397   4.600211862  0.284339755  -160.5  77.5 chic
## 2398  12.600940131 -2.310734257  -159.5  79.5 chic
## 2399   2.249499815 -1.637571310  -158.5  76.0 chic
## 2400   6.148647542  1.500342567  -157.5  78.5 chic
## 2401   5.551834299 -1.779295506  -156.5  76.5 chic
## 2402   0.123585059 -3.021276316  -155.5  72.0 chic
## 2403   0.992335453 -0.998405970  -154.5  69.5 chic
## 2404   5.938194123  4.965565789  -153.5  72.0 chic
## 2405  10.290013824 -1.590990074  -152.5  75.5 chic
## 2406  -1.542124065 -1.527624423  -151.5  68.5 chic
## 2407  -2.788102907  0.368151200  -150.5  70.5 chic
## 2408  -3.418138480 -0.816520947  -149.5  63.5 chic
## 2409  -6.274602899 -0.596296282  -148.5  63.0 chic
## 2410  -3.641456196 -2.418125392  -147.5  66.0 chic
## 2411   5.730202341 -2.232193600  -146.5  65.0 chic
## 2412  18.256149642  0.546430592  -145.5  68.5 chic
## 2413   1.394554193 -0.739926749  -144.5  68.0 chic
## 2414   8.209027456  0.204369598  -143.5  77.0 chic
## 2415  14.954518345  4.619491534  -142.5  76.5 chic
## 2416   6.926486065 -1.188922863  -141.5  76.0 chic
## 2417  23.020518277 -3.282185571  -140.5  77.0 chic
## 2418  13.670314756  2.066328122  -139.5  77.0 chic
## 2419   1.695869803 -1.345054414  -138.5  74.0 chic
## 2420   1.475133486 -2.403110739  -137.5  76.0 chic
## 2421  21.341389947 -4.120886333  -136.5  75.0 chic
## 2422  20.432379741 -0.453263294  -135.5  76.5 chic
## 2423  -5.003289120 -2.881091516  -134.5  78.0 chic
## 2424   0.438596680 -2.326618580  -133.5  71.5 chic
## 2425   1.724561441 -2.581841318  -132.5  71.5 chic
## 2426   3.702784423 -3.138858862  -131.5  70.0 chic
## 2427  12.415773853 -0.498507985  -130.5  80.0 chic
## 2428   0.353526501 -1.623349704  -129.5  77.0 chic
## 2429   6.507561123 -1.967691349  -128.5  79.0 chic
## 2430   6.412353537 -1.859826964  -127.5  84.0 chic
## 2431  11.041755006 -2.162012003  -126.5  81.5 chic
## 2432   0.319311351 -3.223283593  -125.5  72.0 chic
## 2433   0.870179596 -2.813374097  -124.5  71.0 chic
## 2434   8.472827918 -2.512256942  -123.5  79.0 chic
## 2435  -3.293271024 -3.859000921  -122.5  64.0 chic
## 2436  -1.038904582  0.533092966  -121.5  64.5 chic
## 2437 -15.391790430 -3.277019583  -120.5  67.0 chic
## 2438  -6.237868857 -2.612658492  -119.5  66.0 chic
## 2439  -5.247401924 -1.717157254  -118.5  64.0 chic
## 2440  -6.072524144 -4.284821273  -117.5  67.0 chic
## 2441  -5.932494461 -4.968347145  -116.5  59.5 chic
## 2442  -7.044684040  1.144523882  -115.5  62.5 chic
## 2443  -5.768235257  1.493030762  -114.5  64.0 chic
## 2444  -3.927531365 -0.407688766  -113.5  64.5 chic
## 2445  -5.673517319 -4.376594096  -112.5  55.5 chic
## 2446  -4.930494782  0.367081477  -111.5  55.0 chic
## 2447  15.987239728 -3.865063947  -110.5  70.5 chic
## 2448   9.237963683 -2.697027886  -109.5  73.5 chic
## 2449 -11.237147320 -3.250296877  -108.5  62.0 chic
## 2450 -10.291323740 -4.221457109  -107.5  53.0 chic
## 2451 -13.822739370  3.125206883  -106.5  55.0 chic
## 2452  -7.960100464  0.323494675  -105.5  60.5 chic
## 2453  -8.438531950 -0.251822406  -104.5  59.5 chic
## 2454   0.534002120 -3.502849803  -103.5  59.0 chic
## 2455 -10.045380170 -2.891434278  -102.5  61.0 chic
## 2456 -10.112594440 -2.548177188  -101.5  57.0 chic
## 2457 -11.181262860 -2.427006922  -100.5  65.0 chic
## 2458  -8.231404022 -3.244383031   -99.5  54.5 chic
## 2459 -10.687072110 -1.457531974   -98.5  53.5 chic
## 2460  -8.716374017 -3.253936438   -97.5  56.5 chic
## 2461   0.440930963 -3.090792663   -96.5  59.0 chic
## 2462 -13.989700900 -3.262569035   -95.5  49.5 chic
## 2463 -11.336947270 -3.535541521   -94.5  51.5 chic
## 2464 -15.404011480 -5.245342565   -93.5  46.5 chic
## 2465  -6.498531922 -0.859551405   -92.5  46.5 chic
## 2466  -2.533496468  1.210762042   -91.5  56.5 chic
## 2467  -5.954859545 -0.971202632   -90.5  45.0 chic
## 2468   0.968972930  0.312061640   -89.5  52.0 chic
## 2469  -3.653907592 -1.784508341   -88.5  50.5 chic
## 2470 -10.918849100  5.261530147   -87.5  49.0 chic
## 2471   0.318459269  4.444891444   -86.5  61.0 chic
## 2472   7.608119764  2.354671480   -85.5  70.0 chic
## 2473   4.629239017  1.188201046   -84.5  62.5 chic
## 2474   2.069124784 -5.092061014   -83.5  40.5 chic
## 2475  -8.045024972 -2.781968094   -82.5  41.5 chic
## 2476  -6.819309157  0.078773776   -81.5  44.5 chic
## 2477  -6.474014917 -2.540269320   -80.5  46.5 chic
## 2478  -6.491210027  0.751686949   -79.5  40.5 chic
## 2479 -12.768700520 -1.522604892   -78.5  50.5 chic
## 2480  -4.582513085  2.746793295   -77.5  61.0 chic
## 2481  -8.835672733 -4.341895868   -76.5  58.0 chic
## 2482  -1.442222253 -5.328889423   -75.5  52.5 chic
## 2483 -10.101281270 -0.135561842   -74.5  49.5 chic
## 2484 -10.001985920 -2.060370191   -73.5  55.5 chic
## 2485 -18.410678590 -3.180375893   -72.5  55.0 chic
## 2486  -6.388872215 -2.610088943   -71.5  46.5 chic
## 2487 -12.844234030 -0.824592672   -70.5  45.5 chic
## 2488  -4.107280924  2.656816487   -69.5  53.0 chic
## 2489  -2.771767123  3.598133810   -68.5  58.5 chic
## 2490  -7.696611597  3.141979942   -67.5  57.0 chic
## 2491 -10.897911030 -0.584547389   -66.5  54.0 chic
## 2492 -10.942460320 -4.771233686   -65.5  40.5 chic
## 2493  -9.019339138 -0.598184079   -64.5  46.0 chic
## 2494  -7.096214804 -2.886350641   -63.5  35.0 chic
## 2495  -7.257801909 -4.750151672   -62.5  34.5 chic
## 2496  -6.470592733 -4.002900826   -61.5  33.5 chic
## 2497  -9.423462687 -1.781689010   -60.5  35.5 chic
## 2498 -11.421976630 -0.335670861   -59.5  41.5 chic
## 2499  -9.628844685 -0.596943317   -58.5  45.0 chic
## 2500  -8.975219767 -3.159817705   -57.5  51.5 chic
## 2501 -12.834729800 -3.398422677   -56.5  39.0 chic
## 2502  -3.330697125 -3.370400745   -55.5  27.0 chic
## 2503  -5.439856905 -2.233844805   -54.5  28.5 chic
## 2504 -11.175155330  2.367939567   -53.5  38.5 chic
## 2505 -12.888020860 -0.795654794   -52.5  39.0 chic
## 2506 -12.366549780 -2.079084386   -51.5  40.0 chic
## 2507  -7.013717298  0.379114548   -50.5  46.0 chic
## 2508  -5.948516905 -3.186712281   -49.5  41.0 chic
## 2509  -3.982467995 -2.898392330   -48.5  54.5 chic
## 2510  -7.947051626 -3.266766937   -47.5  44.5 chic
## 2511 -12.420143760 -3.423860324   -46.5  41.0 chic
## 2512 -10.065466510 -1.571244760   -45.5  40.5 chic
## 2513  -7.568948869 -4.027089293   -44.5  37.5 chic
## 2514 -11.265557090 -1.492265258   -43.5  37.5 chic
## 2515  -8.524866672 -1.958373824   -42.5  39.0 chic
## 2516  -7.762230803 -1.218450714   -41.5  31.5 chic
## 2517  -5.080971998  0.445314374   -40.5  43.5 chic
## 2518 -12.857253950  2.571322306   -39.5  45.5 chic
## 2519 -13.393525240  5.912597631   -38.5  46.5 chic
## 2520 -11.657538720 -3.626536807   -37.5  42.5 chic
## 2521  -7.284588139 -2.839480808   -36.5  39.5 chic
## 2522  -9.258969933 -2.033933799   -35.5  31.5 chic
## 2523  -8.827366578 -0.532024293   -34.5  25.5 chic
## 2524  -5.460474563  0.173993310   -33.5  31.0 chic
## 2525  -9.162183908 -3.888447288   -32.5  27.0 chic
## 2526 -11.707036090 -0.836961081   -31.5  29.5 chic
## 2527 -13.941197150  9.246776984   -30.5  36.5 chic
## 2528  -7.454024841 -1.687135133   -29.5  41.5 chic
## 2529 -13.893510640 -2.982138448   -28.5  36.0 chic
## 2530 -10.524726110 -3.122958658   -27.5  39.0 chic
## 2531 -13.531977490 -2.144081613   -26.5  37.0 chic
## 2532 -12.834463090 -2.989865811   -25.5  34.5 chic
## 2533 -14.298667180  1.527020713   -24.5  30.5 chic
## 2534 -15.375889680  0.813000373   -23.5  34.0 chic
## 2535 -14.388791490  1.174509298   -22.5  42.0 chic
## 2536  -9.362707233 -1.173753020   -21.5  37.5 chic
## 2537  -6.505059292 -3.453584609   -20.5  25.5 chic
## 2538 -12.141467160  4.318144851   -19.5  30.5 chic
## 2539 -14.302375560  7.421603528   -18.5  41.0 chic
## 2540 -14.139505280  9.329429443   -17.5  41.0 chic
## 2541  -2.517981065 -2.832071670   -16.5  42.5 chic
## 2542  -6.870563927 -3.251026405   -15.5  36.5 chic
## 2543 -15.206456120 -1.924423958   -14.5  38.5 chic
## 2544 -13.463868320 -1.987224212   -13.5  38.0 chic
## 2545 -12.308148110 -2.435773936   -12.5  35.0 chic
## 2546 -12.292701890 -1.414193965   -11.5  32.5 chic
## 2547 -13.311287700 -2.398883862   -10.5  27.0 chic
## 2548 -14.175468060 -0.090527508    -9.5  24.5 chic
## 2549 -10.641021880  1.253271789    -8.5  21.5 chic
## 2550  -9.731582660 -0.452521063    -7.5  15.0 chic
## 2551  -1.354826894 -1.417530271    -6.5  11.5 chic
## 2552  -4.832689563 -3.597410137    -5.5   9.0 chic
## 2553  -8.332217870 -2.049151256    -4.5  13.5 chic
## 2554 -12.760696950  3.911954676    -3.5  13.5 chic
## 2555  -6.727824476  0.871829462    -2.5  16.5 chic
## 2556 -11.123535900  4.075509243    -1.5  14.0 chic
## 2557 -13.762978610  3.624909286    -0.5  32.0 chic
## 2558  -7.067044820 -1.130805949     0.5  35.0 chic
## 2559   1.368600528 -3.247116339     1.5  30.0 chic
## 2560  -5.333209028  0.914822199     2.5  29.5 chic
## 2561  -3.796872728 -3.323469068     3.5  26.0 chic
## 2562  -8.099657941 -0.405335517     4.5  18.5 chic
## 2563 -12.882899580  1.352531217     5.5  29.5 chic
## 2564 -11.989659140 -0.045942081     6.5  12.0 chic
## 2565 -10.199013670  7.663724787     7.5   1.0 chic
## 2566 -13.305414580  6.005340690     8.5   8.0 chic
## 2567 -14.769497220  6.225778026     9.5  26.0 chic
## 2568  -6.250576253 -0.995186529    10.5  28.5 chic
## 2569 -11.801967050  0.911509284    11.5  25.5 chic
## 2570 -10.015984920 -2.842610052    12.5  13.5 chic
## 2571  -1.531412713 -2.350660221    13.5  -4.0 chic
## 2572  -3.443089669 -1.778633041    14.5 -10.5 chic
## 2573 -12.457117840  2.434994951    15.5  -3.5 chic
## 2574  -6.317351544  2.831161503    16.5  -0.5 chic
## 2575  -1.960524873 -2.074976843    17.5 -16.0 chic
## 2576 -10.547485170 10.677100069    18.5  -8.0 chic
## 2577 -13.460702160 28.903402150    19.5   2.5 chic
## 2578 -11.324727400 20.056755181    20.5   8.0 chic
## 2579 -11.805638030 11.764945308    21.5  24.5 chic
## 2580  -9.476748252  1.850050230    22.5  36.0 chic
## 2581  -9.167879219 -2.059870530    23.5  34.0 chic
## 2582  -5.123752592 -3.207474266    24.5  31.0 chic
## 2583   3.513587215 -0.909735358    25.5  23.5 chic
## 2584 -14.576927050  2.016065208    26.5  26.5 chic
## 2585 -13.532613930 -1.792429481    27.5  29.0 chic
## 2586  -8.564313439  0.038563302    28.5  23.0 chic
## 2587   3.053744112 -3.499568840    29.5  16.0 chic
## 2588  -8.459166084 -2.168003869    30.5   3.5 chic
## 2589 -10.074982410  7.006595544    31.5   1.5 chic
## 2590  -6.268892772  2.203917521    32.5  17.0 chic
## 2591  -4.841354924 -0.627340262    33.5   9.5 chic
## 2592  -4.721790521 -0.804943012    34.5  15.5 chic
## 2593  -6.546374513  5.595311944    35.5  19.5 chic
## 2594  -2.085018377  1.848937215    36.5  23.0 chic
## 2595  -0.272974183 -3.288368611    37.5  11.5 chic
## 2596   2.648058015 -3.411916902    38.5  15.5 chic
## 2597  -5.222784930 -2.782543053    39.5   6.5 chic
## 2598  -4.310116893  0.253505569    40.5   6.0 chic
## 2599  -6.333350633  4.543298939    41.5  12.5 chic
## 2600 -11.980835190 12.269053743    42.5  21.0 chic
## 2601  -2.456785293  5.541929715    43.5  20.5 chic
## 2602  -8.362678332  4.793646368    44.5  29.5 chic
## 2603  -3.015062560  1.284628103    45.5  33.5 chic
## 2604  -8.922274726  0.170769043    46.5  29.0 chic
## 2605  -9.411718431  2.126164791    47.5  42.5 chic
## 2606  -1.745601323  3.596539078    48.5  47.0 chic
## 2607  -0.000764509  1.105602889    49.5  55.5 chic
## 2608  -0.768979765 -2.143141403    50.5  46.0 chic
## 2609   0.655312276 -3.106546212    51.5  33.0 chic
## 2610   3.633141170 -3.499699005    52.5  27.0 chic
## 2611  -2.760950289 -3.486748709    53.5  22.5 chic
## 2612  -3.363727981 -3.389955158    54.5  15.5 chic
## 2613  -6.733979949 -0.689678914    55.5  19.5 chic
## 2614  -5.948231122 -1.683146494    56.5  11.0 chic
## 2615  -2.851934658  1.069549152    57.5  13.0 chic
## 2616  -5.262925601  5.495236355    58.5  25.0 chic
## 2617   8.157961501 -2.274890858    59.5  26.0 chic
## 2618   1.860949254  1.969667448    60.5  23.5 chic
## 2619  -8.483421608  4.061053600    61.5  30.5 chic
## 2620  -5.039936066 -1.646617449    62.5  43.0 chic
## 2621  -4.211189471  0.479453151    63.5  40.0 chic
## 2622  -6.012728500 -0.260699740    64.5  48.5 chic
## 2623  -3.671372035 -3.558528899    65.5  40.5 chic
## 2624   4.940781659 -4.403880183    66.5  31.0 chic
## 2625   1.923081421 -3.260583694    67.5  29.0 chic
## 2626  -4.068648039 -1.857024641    68.5  29.5 chic
## 2627   8.037632448 -0.557956684    69.5  29.5 chic
## 2628  -1.717643622  2.242055804    70.5  44.0 chic
## 2629  -5.719010411 -1.337732790    71.5  40.0 chic
## 2630  -9.567203357  0.103653699    72.5  41.5 chic
## 2631   2.298543412 -2.198850519    73.5  38.5 chic
## 2632   6.676175317 -3.379765837    74.5  29.0 chic
## 2633  -3.264315604 -1.549381428    75.5  29.5 chic
## 2634  -4.239246922  0.081737132    76.5  34.0 chic
## 2635  -5.362961387  1.747278290    77.5  38.0 chic
## 2636   6.755095850  4.212623671    78.5  49.5 chic
## 2637  -1.244022936 -1.578765151    79.5  48.0 chic
## 2638   1.623271825  4.150593756    80.5  53.5 chic
## 2639   2.511552181  4.215047494    81.5  61.0 chic
## 2640   3.979150160 -2.230326051    82.5  49.0 chic
## 2641  10.623284355 -3.600458956    83.5  36.5 chic
## 2642  -0.961579340 -0.414915566    84.5  39.0 chic
## 2643  -2.898946397 -2.185321447    85.5  43.0 chic
## 2644  -4.871147869 -2.303650567    86.5  38.0 chic
## 2645  -1.994903993 -2.894786592    87.5  36.0 chic
## 2646  -6.542003625 -0.067072297    88.5  33.5 chic
## 2647  -1.190059462  0.238974746    89.5  40.0 chic
## 2648  -4.223683160  6.638204275    90.5  51.5 chic
## 2649   3.913718440  1.377813658    91.5  55.0 chic
## 2650   7.939452779 -1.110437271    92.5  42.0 chic
## 2651  -2.068659603  2.376809985    93.5  47.0 chic
## 2652   6.113571501 -3.096480557    94.5  40.5 chic
## 2653   6.591806613 -4.342412931    95.5  31.0 chic
## 2654  -2.082935500  0.485910994    96.5  36.5 chic
## 2655  -1.332189903  1.096889711    97.5  47.5 chic
## 2656   3.661624096  1.211328159    98.5  57.5 chic
## 2657  16.042810097 -3.376685540    99.5  46.0 chic
## 2658   4.887507066 -1.659315410   100.5  43.5 chic
## 2659  -4.472950264 -2.874923816   101.5  48.5 chic
## 2660  -7.957189903 -3.188759313   102.5  46.5 chic
## 2661   0.292810097  2.050073655   103.5  57.0 chic
## 2662   6.519892333 -2.558217206   104.5  57.5 chic
## 2663  10.689802058 -2.911870174   105.5  52.5 chic
## 2664   7.966671217 -1.476710356   106.5  55.5 chic
## 2665  11.814023242  1.851485991   107.5  67.5 chic
## 2666  14.778955734 -1.247581752   108.5  58.0 chic
## 2667   2.102169175 -0.942726834   109.5  51.0 chic
## 2668   8.340539043 -4.313175237   110.5  48.5 chic
## 2669   6.783897063 -3.421176642   111.5  45.5 chic
## 2670   6.639285207  0.454137782   112.5  53.0 chic
## 2671  25.972115516  4.121262655   113.5  68.5 chic
## 2672  13.705469761  0.280332526   114.5  71.0 chic
## 2673   6.132500339 -1.447293044   115.5  75.0 chic
## 2674  -0.151319908 -2.730436602   116.5  53.5 chic
## 2675 -10.358584900 -3.932231370   117.5  41.5 chic
## 2676  -3.822339461 -2.395892785   118.5  47.5 chic
## 2677   0.924625765 -5.254961679   119.5  38.5 chic
## 2678   5.714518299 -3.155701478   120.5  45.5 chic
## 2679   1.989238668  0.170946979   121.5  51.0 chic
## 2680  10.086798399 -2.122347389   122.5  49.0 chic
## 2681  -0.929540902  4.095616496   123.5  51.0 chic
## 2682   0.570582914 -0.456260327   124.5  57.5 chic
## 2683   8.298563290 -3.165284268   125.5  45.5 chic
## 2684   4.063688478 -4.494442135   126.5  46.0 chic
## 2685   7.867791827 -1.837130493   127.5  53.5 chic
## 2686   4.884118363 -1.383861616   128.5  61.5 chic
## 2687  -2.363805208  0.961774853   129.5  56.5 chic
## 2688  12.079608559  0.468802846   130.5  62.5 chic
## 2689   4.750692502 -2.670199646   131.5  52.0 chic
## 2690   2.085192055 -2.172467346   132.5  59.5 chic
## 2691  -0.674033534  0.079841056   133.5  60.5 chic
## 2692   8.362829312 -2.939330429   134.5  62.5 chic
## 2693   5.913634295 -3.566699437   135.5  50.5 chic
## 2694   5.421156631 -5.004644817   136.5  48.5 chic
## 2695   7.455166078 -4.200029045   137.5  50.5 chic
## 2696   5.125232148 -2.276855458   138.5  54.0 chic
## 2697   5.994072970  0.061320144   139.5  59.5 chic
## 2698  16.674421150  4.877915413   140.5  68.0 chic
## 2699  23.851960455  1.541132378   141.5  73.0 chic
## 2700  15.666693734 -0.023510148   142.5  68.5 chic
## 2701   7.382754483  0.989102628   143.5  66.5 chic
## 2702   4.643703393 -0.863415172   144.5  65.5 chic
## 2703   4.444425912 -5.318242312   145.5  49.0 chic
## 2704  -1.544928443 -0.861802204   146.5  51.0 chic
## 2705  11.368325157  1.030312673   147.5  63.0 chic
## 2706  22.917351412  1.977192889   148.5  69.5 chic
## 2707  26.644145982  2.829932616   149.5  77.0 chic
## 2708   8.091162963 -0.151993263   150.5  75.0 chic
## 2709   3.720890737 -2.964231888   151.5  56.0 chic
## 2710   6.412723013 -3.020194134   152.5  55.0 chic
## 2711   4.772158569 -0.389930398   153.5  57.0 chic
## 2712  12.458308697  1.696418581   154.5  61.0 chic
## 2713  21.175785030  1.988652775   155.5  71.0 chic
## 2714  18.288245086 -0.948596128   156.5  77.0 chic
## 2715   0.739118277 -1.093995627   157.5  68.5 chic
## 2716   1.956492918 -4.221789640   158.5  55.5 chic
## 2717   7.108214460 -1.468623753   159.5  56.0 chic
## 2718   2.916898814  1.284416132   160.5  61.5 chic
## 2719   4.271292607  0.539893743   161.5  67.0 chic
## 2720   9.104851412 -0.992276269   162.5  72.5 chic
## 2721  12.765420528 -3.016052815   163.5  78.0 chic
## 2722   9.741500341 -2.951141538   164.5  80.0 chic
## 2723  10.839633533 -1.229019444   165.5  86.0 chic
## 2724  11.528659127  1.158126255   166.5  84.0 chic
## 2725   8.114299334  3.168218985   167.5  81.5 chic
## 2726  32.488452941  3.116916210   168.5  82.0 chic
## 2727  11.713664824 -3.180079834   169.5  75.5 chic
## 2728   9.836249515 -0.318420029   170.5  78.0 chic
## 2729   5.070414413 -2.247648595   171.5  74.0 chic
## 2730   6.970014615 -0.816032554   172.5  73.5 chic
## 2731   2.378230983 -2.392663679   173.5  67.5 chic
## 2732   9.658234561 -5.101661890   174.5  63.5 chic
## 2733   4.383928337 -1.136873332   175.5  70.5 chic
## 2734  11.246533681 -2.845200097   176.5  68.5 chic
## 2735   4.583565481 -0.239476484   177.5  69.5 chic
## 2736   5.512794611  1.254907490   178.5  74.5 chic
## 2737   5.534991824 -2.924965333   179.5  70.5 chic
## 2738  -4.801834596  0.728931717   180.5  70.0 chic
## 2739  10.279813323 -0.861450408   181.5  77.0 chic
## 2740  -2.491334235 -3.842916992   182.5  63.5 chic
## 2741   6.034458494 -2.296546749   183.5  66.5 chic
## 2742  16.695459098 -2.695086296   184.5  82.0 chic
## 2743  13.028844280 -2.472802457   185.5  85.5 chic
## 2744   5.543715292 -2.093578712   186.5  82.5 chic
## 2745  -0.803896880 -2.560151077   187.5  79.5 chic
## 2746   0.796838547 -1.640516374   188.5  74.0 chic
## 2747  -5.290070639 -2.018598565   189.5  66.5 chic
## 2748  -1.328130533 -3.640516374   190.5  66.0 chic
## 2749   3.123451145 -1.965897785   191.5  69.5 chic
## 2750  12.077223964  2.102935090   192.5  81.0 chic
## 2751  -4.670134426 -3.623730853   193.5  66.5 chic
## 2752   5.712823297 -1.297185010   194.5  73.5 chic
## 2753   2.431186736 -2.454731184   195.5  74.0 chic
## 2754   6.748900038 -0.519174503   196.5  73.5 chic
## 2755   4.448156251 -2.651525772   197.5  76.0 chic
## 2756  -1.153872056 -1.630014091   198.5  75.0 chic
## 2757   0.472683720 -1.811995371   199.5  77.0 chic
## 2758   2.631697060 -0.410106672   200.5  79.5 chic
## 2759  -4.247894589 -0.777023072   201.5  74.5 chic
## 2760  -8.118302940 -1.407788559   202.5  72.5 chic
## 2761   0.651268356  0.607051623   203.5  75.5 chic
## 2762   2.906203245 -0.958815957   204.5  74.5 chic
## 2763  -5.136866954 -0.442281635   205.5  72.5 chic
## 2764  -6.177347506 -1.540665272   206.5  66.0 chic
## 2765  -6.114735613 -4.063202036   207.5  67.5 chic
## 2766  -7.997616872 -3.724582608   208.5  66.0 chic
## 2767  -2.995753853 -2.492805937   209.5  67.5 chic
## 2768   8.026268356  0.352970318   210.5  70.0 chic
## 2769  11.045209901 -0.947197170   211.5  75.0 chic
## 2770  13.098753862 -0.013966170   212.5  77.0 chic
## 2771   4.887794106 -3.066146941   213.5  69.0 chic
## 2772   1.423363727 -0.884877936   214.5  71.5 chic
## 2773  -0.298794107 -3.107238352   215.5  69.5 chic
## 2774  -0.699214970 -4.662709650   216.5  59.0 chic
## 2775  -0.411335176 -2.451406056   217.5  60.0 chic
## 2776  12.594951261  1.555795464   218.5  65.0 chic
## 2777   8.163634295 -0.180619842   219.5  74.0 chic
## 2778  -0.676480105 -4.653070976   220.5  58.5 chic
## 2779 -15.186796910 -3.491808507   221.5  61.0 chic
## 2780   1.216955199 -4.105439424   222.5  63.5 chic
## 2781  -3.311147618 -3.181615252   223.5  65.5 chic
## 2782   3.634219277 -3.099207404   224.5  75.0 chic
## 2783  -1.588652303 -1.851090966   225.5  64.5 chic
## 2784  -7.440892984  1.176699534   226.5  66.5 chic
## 2785  -1.067590341  0.037155017   227.5  66.5 chic
## 2786   5.617650331  0.662737045   228.5  70.0 chic
## 2787   3.342836427 -1.442816857   229.5  71.0 chic
## 2788  13.023798662 -1.396191430   230.5  77.0 chic
## 2789   9.288634295 -2.579261710   231.5  70.0 chic
## 2790  -2.709237454 -3.485904405   232.5  66.0 chic
## 2791  -7.782380392 -0.788806082   233.5  68.0 chic
## 2792   1.209850163 -0.578707850   234.5  69.5 chic
## 2793  12.349356355 -0.489231118   235.5  77.5 chic
## 2794   6.384286275  1.879583524   236.5  78.5 chic
## 2795   1.798929353 -1.029969653   237.5  76.5 chic
## 2796  12.542369758  0.020336948   238.5  77.0 chic
## 2797  -0.715008176 -1.759115107   239.5  67.5 chic
## 2798  -2.384392234  1.364564663   240.5  66.0 chic
## 2799 -11.170636840 -2.241858494   241.5  64.0 chic
## 2800  -6.484009840 -3.551277430   242.5  61.5 chic
## 2801  -8.585913107 -4.089062818   243.5  59.0 chic
## 2802 -12.697541200  0.336278197   244.5  59.0 chic
## 2803  -4.371810602 -2.039292579   245.5  62.0 chic
## 2804   1.625373117 -4.198539154   246.5  59.5 chic
## 2805   6.096442649 -4.327236695   247.5  61.5 chic
## 2806  -5.570538722 -3.600936415   248.5  67.0 chic
## 2807  -8.286911353 -2.734155593   249.5  65.0 chic
## 2808 -10.955459520 -1.054956568   250.5  69.5 chic
## 2809   0.509245981  0.124053756   251.5  72.0 chic
## 2810   5.753134785 -0.879791646   252.5  76.5 chic
## 2811   3.696576996 -0.035834919   253.5  74.5 chic
## 2812   6.902530394  3.143467732   254.5  75.5 chic
## 2813   8.244047601  2.017811094   255.5  77.0 chic
## 2814   6.326658491  2.399497969   256.5  81.0 chic
## 2815  12.740024838  0.306053103   257.5  80.5 chic
## 2816  12.143253560  1.480519545   258.5  74.0 chic
## 2817 -11.790009730 -4.017510329   259.5  65.5 chic
## 2818  -9.515501843 -2.219696228   260.5  67.0 chic
## 2819  -6.391342363 -0.358386640   261.5  70.0 chic
## 2820   0.493413930  0.412007444   262.5  71.5 chic
## 2821  10.872585383  2.306629062   263.5  71.0 chic
## 2822   0.293966136 -2.427708657   264.5  66.5 chic
## 2823  -9.489207871 -2.394186720   265.5  65.0 chic
## 2824 -10.707648590 -3.837444792   266.5  63.5 chic
## 2825  -4.940292118 -3.987410910   267.5  60.5 chic
## 2826 -12.146789990 -2.437794026   268.5  54.5 chic
## 2827 -15.047079470 -3.669152305   269.5  54.0 chic
## 2828 -10.644975900 -1.583642925   270.5  57.0 chic
## 2829 -16.210913110 -0.494643341   271.5  57.5 chic
## 2830  -2.608811899  1.981713218   272.5  67.5 chic
## 2831  -0.500908059 -3.726952081   273.5  58.0 chic
## 2832   1.858618197 -2.543058471   274.5  57.0 chic
## 2833   1.837734006 -2.554359841   275.5  57.5 chic
## 2834 -10.281632390 -2.686845622   276.5  53.5 chic
## 2835 -15.397546760  0.362440602   277.5  51.0 chic
## 2836   0.320419904 -0.323328507   278.5  62.0 chic
## 2837   9.097821761 -0.877460547   279.5  68.0 chic
## 2838 -18.418986820 -3.806841709   280.5  56.0 chic
## 2839 -10.581827410 -1.771910202   281.5  47.5 chic
## 2840  -9.223734636 -3.954643914   282.5  46.0 chic
## 2841  -8.991459730 -0.441917614   283.5  48.5 chic
## 2842 -13.691404520  3.394280020   284.5  51.5 chic
## 2843 -15.639146880  9.195048846   285.5  55.5 chic
## 2844  -7.298826588  0.127468938   286.5  58.5 chic
## 2845  -8.505435825  1.506614978   287.5  58.0 chic
## 2846  -0.205275901  2.595567670   288.5  66.0 chic
## 2847  -0.176409645 -1.215105324   289.5  69.5 chic
## 2848  -9.201945093 -2.314783395   290.5  65.0 chic
## 2849  -8.546570157 -1.079032265   291.5  64.5 chic
## 2850 -13.455225110 -0.613631684   292.5  57.0 chic
## 2851 -12.902264430  1.208215472   293.5  56.5 chic
## 2852  -6.459429126  0.622672612   294.5  58.5 chic
## 2853  -8.234412690 -0.722216668   295.5  52.0 chic
## 2854  -8.794656078 -0.061714385   296.5  47.0 chic
## 2855 -11.686157210 -2.848955289   297.5  43.0 chic
## 2856 -13.915438230 -2.304206431   298.5  42.0 chic
## 2857 -14.236912390  0.553478190   299.5  46.0 chic
## 2858  -5.798853357  2.107006754   300.5  54.0 chic
## 2859  -3.136850342  1.769448092   301.5  57.0 chic
## 2860 -11.665017970 -1.660627815   302.5  48.5 chic
## 2861   5.472280351 -3.888027299   303.5  45.5 chic
## 2862  -7.072373899 -3.186490006   304.5  44.5 chic
## 2863  -8.456052710 -0.276486558   305.5  51.0 chic
## 2864  -9.532879194 -1.083068959   306.5  60.5 chic
## 2865 -15.350248430 -2.620369751   307.5  58.5 chic
## 2866 -10.897249900 -2.248337787   308.5  54.0 chic
## 2867  -5.090341993 -2.794456509   309.5  48.0 chic
## 2868  -8.357422573 -0.461123176   310.5  46.5 chic
## 2869 -11.226392200  3.334365210   311.5  55.0 chic
## 2870   6.653950266 -3.376469161   312.5  47.0 chic
## 2871   6.999318779 -2.522911197   313.5  43.0 chic
## 2872  -4.524226529 -0.810331759   314.5  46.0 chic
## 2873 -13.799779020  0.832248149   315.5  46.5 chic
## 2874  -3.768589934 -2.050724251   316.5  58.0 chic
## 2875 -10.740028260 -1.387830262   317.5  50.5 chic
## 2876  -6.435674191 -3.560080096   318.5  41.5 chic
## 2877  -5.001580802  0.283365820   319.5  43.5 chic
## 2878 -14.304575530 -1.785891739   320.5  44.0 chic
## 2879  -6.961743841 -0.566788106   321.5  44.5 chic
## 2880 -15.312270110  6.689220844   322.5  38.0 chic
## 2881  -9.798685635  4.935363666   323.5  52.0 chic
## 2882  -1.972678464 -1.849873275   324.5  43.0 chic
## 2883  -5.706598845 -1.039179713   325.5  31.0 chic
## 2884 -11.619587270  0.833926605   326.5  36.0 chic
## 2885  -7.032355070  0.463629499   327.5  39.0 chic
## 2886  -7.849228919  0.240347506   328.5  39.0 chic
## 2887   2.520706624 -0.179005927   329.5  35.0 chic
## 2888  -7.451393551 -1.541268535   330.5  47.5 chic
## 2889  -3.970405554 -2.616496845   331.5  36.0 chic
## 2890  -8.879754222 -2.466946930   332.5  29.5 chic
## 2891 -12.319476780 -0.587262988   333.5  29.0 chic
## 2892 -10.183943470  1.491242984   334.5  42.0 chic
## 2893  -8.232892907  2.501495015   335.5  46.0 chic
## 2894 -11.411990510  0.709621979   336.5  48.0 chic
## 2895 -14.620784250  0.957887709   337.5  48.5 chic
## 2896 -15.648065230 -0.893395690   338.5  43.5 chic
## 2897 -10.228280930 -2.662707329   339.5  35.0 chic
## 2898   1.061732272 -2.679317267   340.5  31.0 chic
## 2899  -1.572184697 -1.420675920   341.5  30.0 chic
## 2900 -15.207445810 -1.709128907   342.5  30.5 chic
## 2901 -13.626281430  1.372132299   343.5  27.0 chic
## 2902  -5.221783496 -2.045731274   344.5  19.0 chic
## 2903 -14.223779890  1.636431485   345.5  21.5 chic
## 2904 -15.166940550  9.429803448   346.5  23.5 chic
## 2905 -12.297034350  6.940024113   347.5  27.5 chic
## 2906 -15.475139870  5.511077088   348.5  32.5 chic
## 2907 -15.449311010  0.945543788   349.5  36.5 chic
## 2908 -12.738362720 -0.113495652   350.5  37.0 chic
## 2909  -7.183841819 -2.812910824   351.5  29.5 chic
## 2910 -13.071038180 -0.982902044   352.5  28.0 chic
## 2911 -14.604820240  4.044081073   353.5  38.0 chic
## 2912 -14.743702510  7.860310907   354.5  40.5 chic
## 2913 -15.452549990  7.213237852   355.5  39.5 chic
## 2914  -8.735743428 -2.526048888   356.5  38.5 chic
## 2915  -7.540969014 -3.370545689   357.5  33.5 chic
## 2916 -10.282313470 -1.067235187   358.5  39.5 chic
## 2917 -14.329090640  3.550265245   359.5  36.0 chic
## 2918 -12.928284850  2.755918476   360.5  40.5 chic
## 2919  -8.239511470 -1.994379604   361.5  38.0 chic
## 2920   2.709471374 -2.637036834   362.5  35.0 chic
## 2921 -11.045234580  4.039376600   363.5  32.0 chic
## 2922 -15.483833270  0.375389391   364.5  32.0 chic
## 2923  -2.370248795 -4.215478189   365.5  19.5 chic
## 2924  -3.672319602 -1.734015622   366.5  14.5 chic
## 2925  -7.401027227 -1.085224888   367.5  11.0 chic
## 2926  -6.217070345 -2.022313162   368.5   3.0 chic
## 2927 -11.230941640  3.034065899   369.5   9.0 chic
## 2928 -14.493908710  1.776020436   370.5  22.5 chic
## 2929  -9.952373776 -0.053823286   371.5  19.5 chic
## 2930 -11.211890650  2.258350866   372.5  12.0 chic
## 2931  -8.638297203  3.612550116   373.5  17.5 chic
## 2932 -12.935853320  0.773051690   374.5  23.5 chic
## 2933 -16.502951790  0.333450196   375.5  33.0 chic
## 2934 -14.758118510 -2.238437575   376.5  42.0 chic
## 2935 -15.501556890 -3.831105766   377.5  35.5 chic
## 2936 -15.464481590 -4.462096763   378.5  37.0 chic
## 2937  -6.626679665 -4.287900728   379.5  33.0 chic
## 2938  -4.831163941 -3.367711210   380.5  34.0 chic
## 2939 -15.887084210 -0.985966188   381.5  36.5 chic
## 2940 -12.242144580 -1.830826797   382.5  35.5 chic
## 2941 -10.909388450 -3.937144461   383.5  34.5 chic
## 2942  -6.523531995 -4.247583757   384.5  30.0 chic
## 2943  -7.042450544 -4.583817806   385.5  23.5 chic
## 2944 -10.863844160 -2.578256003   386.5  20.0 chic
## 2945 -13.119124230 -3.539816269   387.5  20.0 chic
## 2946 -13.883078850  2.371798774   388.5  15.0 chic
## 2947 -16.071712960  5.050081279   389.5  18.0 chic
## 2948 -13.904311730  2.451274658   390.5  18.0 chic
## 2949 -14.822849550  8.464247286   391.5  23.5 chic
## 2950   4.665154239 -1.220072374   392.5  28.5 chic
## 2951   4.914967449 -4.234503235   393.5  25.0 chic
## 2952  -9.479359076  1.465803659   394.5  24.0 chic
## 2953  -7.188955367  0.831171990   395.5  30.5 chic
## 2954  -8.029814376  0.014783782   396.5  37.0 chic
## 2955   0.101969405 -2.456852022   397.5  33.5 chic
## 2956  -9.261634267 -0.509987908   398.5  30.5 chic
## 2957   1.389531386 -2.890479956   399.5  20.0 chic
## 2958   1.039535972 -4.430658948   400.5   8.5 chic
## 2959 -10.246829630 -0.577771310   401.5  11.0 chic
## 2960  -8.695700968 -0.597697139   402.5  15.0 chic
## 2961  -5.069091025 -0.277970744   403.5  12.0 chic
## 2962 -12.173891840  2.693217915   404.5  24.0 chic
## 2963  -3.646554295 -0.470796025   405.5  26.5 chic
## 2964   2.806154613 -2.631704768   406.5   6.5 chic
## 2965  -6.225768730 -2.469428566   407.5   7.5 chic
## 2966  -7.006270721 -0.925935416   408.5  17.0 chic
## 2967 -10.604789590  1.915517988   409.5  18.5 chic
## 2968 -13.893814410 -1.876315299   410.5  30.5 chic
## 2969  -7.720233931 -2.553909001   411.5  25.0 chic
## 2970 -12.951286850  2.803398078   412.5  33.0 chic
## 2971  -8.200027688  3.763783425   413.5  42.5 chic
## 2972  -3.307616592 -1.526570457   414.5  38.0 chic
## 2973  -6.791053026 -0.466097647   415.5  40.5 chic
## 2974   0.993590611 -2.986299703   416.5  28.0 chic
## 2975 -13.122502410  1.016827090   417.5  38.0 chic
## 2976  -5.000647221 -1.212809667   418.5  41.0 chic
## 2977  -2.708305359 -1.716189774   419.5  32.0 chic
## 2978  -5.652697301 -0.622455002   420.5  39.0 chic
## 2979  -2.334130451 -1.715037872   421.5  30.5 chic
## 2980  -2.277627376 -2.478261460   422.5  31.5 chic
## 2981  -1.350249743 -2.600245263   423.5  27.5 chic
## 2982  -6.117355625 -2.775995997   424.5  16.5 chic
## 2983  -8.046438367  0.107854458   425.5  17.5 chic
## 2984 -10.152100680  2.544365750   426.5  23.0 chic
## 2985  -8.568234384  2.671465040   427.5  31.5 chic
## 2986 -14.072686130 -2.199286289   428.5  35.0 chic
## 2987  -5.586464621 -1.832962619   429.5  33.0 chic
## 2988  -8.971684906 -2.658857714   430.5  30.5 chic
## 2989   0.374459680 -2.513434845   431.5  23.5 chic
## 2990  -6.547516754  0.917293993   432.5  24.5 chic
## 2991  -5.580518481  1.379862395   433.5  40.5 chic
## 2992   6.782639006  0.964844211   434.5  51.0 chic
## 2993  10.864247952 -1.150458948   435.5  58.5 chic
## 2994   0.792463949  1.872085672   436.5  61.5 chic
## 2995  -3.395301586  4.286722351   437.5  61.0 chic
## 2996  -0.385486491  5.005061437   438.5  59.0 chic
## 2997  -5.586810968 -0.417535085   439.5  54.0 chic
## 2998   3.099324995 -2.724608112   440.5  42.5 chic
## 2999   2.134467964 -0.409639641   441.5  49.0 chic
## 3000  -6.813802727 -1.894766004   442.5  50.0 chic
## 3001  -6.809741570 -2.998619142   443.5  50.5 chic
## 3002  -4.030448770 -2.867296916   444.5  43.5 chic
## 3003   1.462795846 -2.476445737   445.5  39.0 chic
## 3004   2.548963579 -3.131742744   446.5  36.0 chic
## 3005   2.631899216 -2.227902644   447.5  37.0 chic
## 3006   5.820890942 -0.398441793   448.5  42.0 chic
## 3007   3.328591604  0.897860559   449.5  43.5 chic
## 3008  -4.734401150 -2.577928401   450.5  41.5 chic
## 3009 -15.799914720 -2.450657297   451.5  41.0 chic
## 3010 -11.778111440 -2.835411525   452.5  40.0 chic
## 3011 -10.349988860 -2.284625309   453.5  40.5 chic
## 3012 -11.603101510 -2.906251455   454.5  36.5 chic
## 3013  -6.388561560 -2.580691014   455.5  37.0 chic
## 3014   1.351743011  0.568254493   456.5  44.0 chic
## 3015   0.041797825  0.895373608   457.5  55.0 chic
## 3016   2.070857572 -3.606738468   458.5  33.0 chic
## 3017  -5.286094349 -0.830594119   459.5  34.5 chic
## 3018   2.002475367  0.242380218   460.5  52.0 chic
## 3019   3.360835776 -2.873010888   461.5  42.5 chic
## 3020  -7.742932036 -2.884747726   462.5  43.0 chic
## 3021  10.711438023 -4.110283067   463.5  37.5 chic
## 3022  -2.855641307 -3.585784738   464.5  36.5 chic
## 3023 -10.225187700 -0.117171373   465.5  49.5 chic
## 3024  -4.118992943 -2.647998480   466.5  39.5 chic
## 3025  11.297784576 -3.303605731   467.5  49.0 chic
## 3026  10.712899802 -3.768248092   468.5  42.5 chic
## 3027   7.069025114 -1.338998393   469.5  46.5 chic
## 3028  -1.351404717  2.408568154   470.5  51.5 chic
## 3029   6.528476863 -0.682719154   471.5  50.5 chic
## 3030   4.011430646 -1.217978056   472.5  61.0 chic
## 3031   1.217349282 -2.863639593   473.5  47.5 chic
## 3032  -5.749467520 -3.215066632   474.5  44.0 chic
## 3033 -11.386294260 -3.205903538   475.5  46.5 chic
## 3034   0.101248314 -0.613070908   476.5  51.0 chic
## 3035   0.018955544 -2.198377268   477.5  45.0 chic
## 3036  -4.554477376  1.716713560   478.5  47.5 chic
## 3037  -2.894931767 -2.012505273   479.5  47.0 chic
## 3038 -11.087571130 -1.774641323   480.5  50.0 chic
## 3039  -2.518928801 -2.459059301   481.5  47.5 chic
## 3040  -4.763421006 -0.154806194   482.5  53.0 chic
## 3041  -4.696835956 -0.310417140   483.5  49.5 chic
## 3042   3.451926666 -2.517814794   484.5  44.5 chic
## 3043   0.006653388 -2.192230030   485.5  45.5 chic
## 3044  -0.276907442 -2.526741932   486.5  50.5 chic
## 3045  -4.063151000 -0.254418940   487.5  49.5 chic
## 3046  -7.760491048 -0.965747560   488.5  52.5 chic
## 3047  -3.351400139 -2.107810174   489.5  55.0 chic
## 3048  -0.856265761  0.165070352   490.5  56.0 chic
## 3049  14.589891807  1.656108295   491.5  62.5 chic
## 3050   3.100997546 -1.784805973   492.5  57.5 chic
## 3051   0.032700363 -0.865277556   493.5  61.5 chic
## 3052 -12.288800040 -3.365637153   494.5  51.0 chic
## 3053  -4.205951989 -1.496885960   495.5  59.5 chic
## 3054   2.029243413  0.418380736   496.5  60.0 chic
## 3055   1.331099701 -1.340068558   497.5  62.0 chic
## 3056   6.702873354 -1.201218515   498.5  62.5 chic
## 3057  -1.317269997  1.614384518   499.5  61.5 chic
## 3058   0.523085825 -0.655814383   500.5  68.5 chic
## 3059  -0.393066806 -2.402795048   501.5  58.5 chic
## 3060  -3.694717205 -2.426211990   502.5  52.5 chic
## 3061  -0.726144377 -0.443330089   503.5  60.5 chic
## 3062   4.169533289 -1.142871785   504.5  66.0 chic
## 3063   5.252866622 -2.062709950   505.5  60.5 chic
## 3064   3.520577303  0.808571538   506.5  62.0 chic
## 3065  -0.247233472 -2.137257874   507.5  59.5 chic
## 3066   0.637730296 -3.159114060   508.5  51.5 chic
## 3067  -3.610240776 -3.212822083   509.5  55.0 chic
## 3068   1.313684286 -1.850201739   510.5  55.0 chic
## 3069  -0.835417348 -2.943664977   511.5  60.5 chic
## 3070   4.795856689 -2.680254293   512.5  65.5 chic
## 3071   2.601929687 -2.256260179   513.5  61.0 chic
## 3072  -0.405573538  1.683068533   514.5  67.5 chic
## 3073   6.504919235  6.083306454   515.5  69.5 chic
## 3074   8.122752867  4.104828903   516.5  71.5 chic
## 3075  -7.949050436 -0.107754821   517.5  67.5 chic
## 3076  14.335585825 -1.220233712   518.5  67.0 chic
## 3077  12.629851123  0.572127452   519.5  67.0 chic
## 3078  14.346544660  3.045598410   520.5  67.5 chic
## 3079   6.600516276  0.776042687   521.5  74.0 chic
## 3080  10.523029451  0.110890543   522.5  71.5 chic
## 3081  -1.767232041 -3.586950107   523.5  53.5 chic
## 3082  -4.275288327 -2.289265467   524.5  59.0 chic
## 3083   1.753601292 -2.331516492   525.5  67.5 chic
## 3084  -1.034202932 -3.226243465   526.5  59.0 chic
## 3085  -1.431917458 -2.856633327   527.5  61.0 chic
## 3086  -5.376470374  2.913726831   528.5  67.5 chic
## 3087  12.530911992  1.549391340   529.5  74.0 chic
## 3088  20.037211673  4.711452259   530.5  72.0 chic
## 3089  20.283322431  1.699132974   531.5  76.5 chic
## 3090  23.046006735  2.995198121   532.5  77.5 chic
## 3091  26.919980751  1.007704545   533.5  82.0 chic
## 3092  24.151099578 -0.532128142   534.5  82.5 chic
## 3093  24.318319306 -1.477504364   535.5  85.0 chic
## 3094  14.798634654 -0.754646597   536.5  78.0 chic
## 3095  25.751482939  3.027434401   537.5  80.0 chic
## 3096  43.687780018  1.408259512   538.5  79.5 chic
## 3097  40.761579655  4.619878809   539.5  77.5 chic
## 3098  26.331649415  0.361194614   540.5  75.5 chic
## 3099  15.916550589 -1.618029027   541.5  75.5 chic
## 3100   4.284591491 -1.660262387   542.5  72.5 chic
## 3101   0.715969884 -1.797825927   543.5  74.0 chic
## 3102   5.363887501  1.923673962   544.5  77.0 chic
## 3103   6.802589575 -2.639975454   545.5  71.5 chic
## 3104   5.147316273 -2.983784576   546.5  65.5 chic
## 3105   2.476958251 -0.180158841   547.5  63.5 chic
## 3106  22.899338311 -1.109357031   548.5  71.0 chic
## 3107   6.701059548 -2.112154449   549.5  75.5 chic
## 3108   8.000929878 -2.378628215   550.5  75.0 chic
## 3109   9.571410788 -1.637991080   551.5  72.0 chic
## 3110   4.636723314 -1.889127309   552.5  70.5 chic
## 3111   4.206649415  2.544034792   553.5  70.5 chic
## 3112  18.518624918 -1.027733905   554.5  73.0 chic
## 3113   2.523175458  2.723429769   555.5  73.5 chic
## 3114  18.141378340 -0.673346586   556.5  79.0 chic
## 3115  24.160424862 -1.022607058   557.5  84.5 chic
## 3116  36.745545006  1.271856412   558.5  92.0 chic
## 3117  29.703545358  2.268585616   559.5  91.5 chic
## 3118  28.115090860  0.697659932   560.5  86.0 chic
## 3119  21.115008612 -0.933012590   561.5  83.0 chic
## 3120   5.649731528 -2.315888155   562.5  78.5 chic
## 3121   2.409601274 -1.995919567   563.5  74.0 chic
## 3122   0.447869847  0.555658184   564.5  75.5 chic
## 3123  -5.221114928 -0.934191925   565.5  73.5 chic
## 3124   3.703213604 -0.680328307   566.5  74.0 chic
## 3125   0.636022132 -1.495123991   567.5  76.0 chic
## 3126   8.371172718 -1.328271587   568.5  76.0 chic
## 3127   4.989646659 -2.368999802   569.5  79.0 chic
## 3128   0.915436731  0.056729220   570.5  81.5 chic
## 3129  12.386753466 -2.028633046   571.5  77.0 chic
## 3130  18.865248005 -1.617262521   572.5  80.5 chic
## 3131   9.433018914 -2.199315208   573.5  80.5 chic
## 3132   6.413280854 -0.077818687   574.5  82.5 chic
## 3133  13.895446812 -0.501397258   575.5  83.5 chic
## 3134  16.365166062 -0.156420089   576.5  84.5 chic
## 3135   5.343901965 -1.811919658   577.5  75.0 chic
## 3136  -0.143469740 -3.046153284   578.5  74.0 chic
## 3137 -15.212000610 -3.079187034   579.5  75.0 chic
## 3138 -13.945498310 -2.217239142   580.5  77.0 chic
## 3139   1.526099578 -2.910194824   581.5  76.5 chic
## 3140  10.218563990 -3.522561278   582.5  78.0 chic
## 3141  -1.131588145 -2.895441622   583.5  79.5 chic
## 3142   5.549481073  1.008375957   584.5  79.5 chic
## 3143 -15.755459980 -1.532829701   585.5  75.0 chic
## 3144  -5.528694181 -1.435892345   586.5  80.0 chic
## 3145   4.646788267 -0.262845044   587.5  82.0 chic
## 3146  20.691592094 -0.090052935   588.5  86.5 chic
## 3147  18.202773260 -0.854672860   589.5  87.0 chic
## 3148   9.602415919  0.006330611   590.5  87.0 chic
## 3149   5.186211864 -2.102913537   591.5  78.0 chic
## 3150  -7.772121469 -2.290010964   592.5  84.0 chic
## 3151   2.811169786 -2.337001489   593.5  81.5 chic
## 3152  12.275099126 -2.525677315   594.5  81.0 chic
## 3153   3.035592558 -2.437755534   595.5  81.0 chic
## 3154   2.085570786 -3.220531071   596.5  74.0 chic
## 3155  -1.303769951 -1.906957250   597.5  75.5 chic
## 3156  -4.214906680 -1.837536327   598.5  74.0 chic
## 3157  -3.385463487  1.331576070   599.5  74.0 chic
## 3158   1.489475445 -2.790717928   600.5  76.0 chic
## 3159   8.851926536 -1.933756436   601.5  81.0 chic
## 3160  20.394545198  1.277348245   602.5  80.0 chic
## 3161  18.714366813 -0.570079975   603.5  77.5 chic
## 3162  -7.150483292 -2.250829840   604.5  76.0 chic
## 3163  -3.444693034 -0.963351663   605.5  80.0 chic
## 3164  17.172007323  0.315155652   606.5  83.0 chic
## 3165  11.073945341  0.831442922   607.5  79.0 chic
## 3166  -4.059633378 -1.190353484   608.5  69.5 chic
## 3167  -2.230362348  2.653589723   609.5  68.5 chic
## 3168  12.024434626  2.813793281   610.5  72.0 chic
## 3169  25.043613170  3.882269843   611.5  75.0 chic
## 3170   4.936211864  3.668715613   612.5  76.5 chic
## 3171  13.237732520  1.048663655   613.5  74.5 chic
## 3172  -5.973707803 -0.823499148   614.5  62.0 chic
## 3173   0.372791584 -2.993674217   615.5  61.0 chic
## 3174  -1.519903455 -2.318679048   616.5  58.5 chic
## 3175  -1.465431177 -2.092542006   617.5  54.5 chic
## 3176  -6.989691222  0.777711534   618.5  59.0 chic
## 3177  -6.851118436  3.529300804   619.5  65.5 chic
## 3178   3.176347507 -1.726238633   620.5  73.0 chic
## 3179  -4.413519696 -2.471223556   621.5  66.5 chic
## 3180  -7.920379281 -0.017044595   622.5  65.5 chic
## 3181   4.705404137 -0.707896768   623.5  73.5 chic
## 3182  -7.050478329 -3.679538665   624.5  56.5 chic
## 3183 -14.878569850  1.117786448   625.5  56.0 chic
## 3184 -12.047026550  0.435793913   626.5  61.5 chic
## 3185  -9.566997198 -2.948299573   627.5  55.0 chic
## 3186 -13.472794300 -3.133212338   628.5  45.5 chic
## 3187 -14.781622530 -1.927831617   629.5  44.0 chic
## 3188 -11.597794300 -0.826974655   630.5  44.0 chic
## 3189  -7.047026551  0.043515415   631.5  49.5 chic
## 3190  -9.853336449  0.339610924   632.5  56.0 chic
## 3191  -8.270003116  0.620654711   633.5  60.0 chic
## 3192  -3.478336449  0.612679524   634.5  67.0 chic
## 3193   0.438664864  8.157701261   635.5  67.0 chic
## 3194   6.985539034  3.417404983   636.5  68.0 chic
## 3195  25.932272170  1.147440992   637.5  75.0 chic
## 3196   3.281782401 -1.716983525   638.5  66.5 chic
## 3197  -9.389460966  3.059855454   639.5  64.0 chic
## 3198  -7.415668147  0.326224727   640.5  62.0 chic
## 3199  -9.088736328 -2.092756448   641.5  58.0 chic
## 3200  -4.461647896 -3.069145696   642.5  58.0 chic
## 3201 -16.796837020 -2.463470461   643.5  57.5 chic
## 3202 -15.443122670 -3.708923669   644.5  49.5 chic
## 3203 -11.468583320 -0.775687282   645.5  52.5 chic
## 3204  -9.601448502 -0.696325020   646.5  61.5 chic
## 3205 -16.213537700  0.225623245   647.5  57.0 chic
## 3206 -10.241119860  1.562506201   648.5  62.5 chic
## 3207   1.784668909  0.636543229   649.5  68.5 chic
## 3208   5.937438936 -0.019799633   650.5  70.0 chic
## 3209 -10.740955820 -2.792749606   651.5  48.0 chic
## 3210  -7.680853051 -3.601937174   652.5  46.5 chic
## 3211 -14.388729760 -0.915790946   653.5  48.0 chic
## 3212  -7.672949192  2.526613063   654.5  59.5 chic
## 3213  -9.307602559 -1.718621134   655.5  58.0 chic
## 3214   3.938351636 -2.056907617   656.5  64.0 chic
## 3215 -18.771233160 -3.783875933   657.5  44.5 chic
## 3216 -18.287391170 -3.635873948   658.5  38.5 chic
## 3217 -11.553569660 -0.264017047   659.5  48.0 chic
## 3218   3.365463985 -0.491125090   660.5  60.0 chic
## 3219 -12.158143940 -2.638543512   661.5  42.5 chic
## 3220 -13.899073930 -1.021023756   662.5  46.5 chic
## 3221 -14.488757210  0.434907731   663.5  51.5 chic
## 3222  -8.522887000 -3.032477917   664.5  53.0 chic
## 3223 -15.363608980 -2.738839477   665.5  45.5 chic
## 3224 -12.156977910 -2.705768521   666.5  44.0 chic
## 3225 -11.365635830 -3.223920685   667.5  43.0 chic
## 3226 -16.793779480 -3.237087441   668.5  44.5 chic
## 3227 -15.390361770 -1.660518978   669.5  55.5 chic
## 3228 -14.356039020 -2.098015439   670.5  44.5 chic
## 3229  -7.459996471 -1.267783995   671.5  30.0 chic
## 3230  -8.508098152 -2.105858495   672.5  27.5 chic
## 3231 -14.111366330 -0.544247794   673.5  28.5 chic
## 3232 -13.693900580 -0.143465690   674.5  40.5 chic
## 3233 -10.755228550 -2.379018967   675.5  38.0 chic
## 3234  -7.501211100 -2.692005673   676.5  26.0 chic
## 3235 -14.410506800  2.414836502   677.5  32.5 chic
## 3236 -12.012244830 -2.001823513   678.5  46.5 chic
## 3237  -4.362936802 -3.490900439   679.5  26.5 chic
## 3238 -14.214984560 -1.460344152   680.5  22.5 chic
## 3239 -16.913814040 -0.571576290   681.5  26.0 chic
## 3240 -13.779401740 -0.279762852   682.5  27.0 chic
## 3241 -13.175407250  0.272281947   683.5  27.0 chic
## 3242 -15.922148370 -0.448141617   684.5  32.5 chic
## 3243 -16.568912460  0.205165948   685.5  34.5 chic
## 3244 -17.292052950  0.229453112   686.5  36.5 chic
## 3245 -13.970794890  1.838585532   687.5  39.0 chic
## 3246  -7.837818649  3.531280959   688.5  44.5 chic
## 3247  -6.024376186 -1.017807162   689.5  28.5 chic
## 3248 -16.100918200  2.058243979   690.5  27.5 chic
## 3249  -9.161133282 -1.289239862   691.5  27.0 chic
## 3250 -12.763464910  3.344253262   692.5  24.0 chic
## 3251 -13.300665070  3.366747441   693.5  40.0 chic
## 3252 -15.372468720  2.295632095   694.5  43.0 chic
## 3253 -11.299059780 -2.560975569   695.5  34.5 chic
## 3254 -11.633081820 -2.369424483   696.5  21.0 chic
## 3255 -15.877457390  5.100270572   697.5  22.0 chic
## 3256 -15.467679160  2.560524392   698.5  36.5 chic
## 3257 -13.629014070  1.568743659   699.5  39.0 chic
## 3258  -9.862775323  0.491818225   700.5  40.5 chic
## 3259 -10.600008380 -0.956311490   701.5  47.0 chic
## 3260  -6.886176837 -2.584580283   702.5  36.5 chic
## 3261  -9.217341220  0.140358330   703.5  35.0 chic
## 3262  -9.607996550  0.882649884   704.5  28.0 chic
## 3263  -8.786617720 -1.893276359   705.5  25.0 chic
## 3264 -12.821615040  0.759422208   706.5  21.5 chic
## 3265   2.030710252 -0.667284185   707.5   0.5 chic
## 3266  -1.883343730 -2.462197382   708.5   7.5 chic
## 3267 -14.302447720 -2.636476192   709.5   4.5 chic
## 3268 -11.576109460  0.534828959   710.5  15.5 chic
## 3269 -15.745631870 -0.045550498   711.5  28.5 chic
## 3270 -17.007627670 -2.497062637   712.5  38.0 chic
## 3271 -17.109326480  0.174360981   713.5  34.0 chic
## 3272 -11.438792470 -0.614662871   714.5  27.0 chic
## 3273  -9.134135987  0.366478775   715.5  30.0 chic
## 3274  -5.788554244 -0.469062123   716.5  35.0 chic
## 3275   5.182599700 -3.380313253   717.5  30.0 chic
## 3276  -7.850212390 -2.028327068   718.5  26.0 chic
## 3277  -7.950799687 -1.489982229   719.5  24.0 chic
## 3278  -9.217956550 -1.969308314   720.5  22.0 chic
## 3279 -12.714108130 -2.352291256   721.5  27.0 chic
## 3280  -6.365738062 -2.860939866   722.5  25.0 chic
## 3281  -0.145737975 -3.484943309   723.5  22.5 chic
## 3282 -16.202215480 -1.108904458   724.5  21.0 chic
## 3283 -11.168884280 -0.106273717   725.5  23.0 chic
## 3284 -14.923244950  3.455295676   726.5  23.0 chic
## 3285 -13.345478000  3.858887505   727.5  25.0 chic
## 3286 -15.837179410  2.977209423   728.5  29.5 chic
## 3287 -16.070284430 -2.674890808   729.5  34.0 chic
## 3288 -12.478393540 -1.747721858   730.5  35.0 chic
## 3289   4.724245496 -3.067917102   731.5  27.5 chic
## 3290  -0.846117222 -2.412288752   732.5  21.0 chic
## 3291 -14.521364610 -0.929692560   733.5  21.0 chic
## 3292 -11.547026540  1.695200993   734.5  16.0 chic
## 3293  -5.654911289  3.854534604   735.5  19.0 chic
## 3294   0.946443614 -2.035003474   736.5  18.5 chic
## 3295 -11.035953480  4.645778422   737.5  13.0 chic
## 3296 -11.695071190  3.122598544   738.5  27.0 chic
## 3297 -12.154754860  0.928634643   739.5  21.5 chic
## 3298 -11.019795710 -0.304497541   740.5  26.5 chic
## 3299 -13.549194530  1.509147890   741.5  28.5 chic
## 3300 -15.912212070  3.818278510   742.5  32.0 chic
## 3301  -7.693269913  4.763278054   743.5  37.5 chic
## 3302   2.636168850 -2.470087097   744.5  24.0 chic
## 3303 -15.734730060 -2.012916360   745.5  32.5 chic
## 3304 -13.842378460 -2.287025584   746.5  47.0 chic
## 3305  -4.203809964 -2.404216474   747.5  36.5 chic
## 3306  -0.484896317 -2.133819734   748.5   9.0 chic
## 3307 -13.201848380 -0.997496163   749.5  15.5 chic
## 3308 -12.557433150  3.062338573   750.5  25.5 chic
## 3309 -10.909040170 -0.071681698   751.5  37.5 chic
## 3310 -11.660325890 -2.463783149   752.5  33.0 chic
## 3311  -4.188064976 -1.697768633   753.5  20.5 chic
## 3312 -13.471463230  0.956384890   754.5  20.0 chic
## 3313 -11.023532920  0.893627743   755.5  32.0 chic
## 3314   3.161398587 -2.228999724   756.5  16.5 chic
## 3315  -8.419935083  4.244816354   757.5  19.0 chic
## 3316  -3.061889084 -0.950885364   758.5  18.5 chic
## 3317  -6.871873967  0.411975062   759.5   2.0 chic
## 3318  -9.379057113  1.857580085   760.5  -3.0 chic
## 3319  -6.072583284  4.236704163   761.5   1.5 chic
## 3320  -6.866378290  0.342256773   762.5 -10.5 chic
## 3321  -7.697825560 -0.614361069   763.5 -12.0 chic
## 3322  -7.456067166  2.192346087   764.5  -6.0 chic
## 3323  -9.372733833  4.642712074   765.5  11.5 chic
## 3324 -10.486180420  5.365804991   766.5  18.5 chic
## 3325 -12.414400500  0.178113551   767.5  38.0 chic
## 3326 -14.298867870 -0.855428673   768.5  39.5 chic
## 3327 -11.801522790  3.801126115   769.5  39.0 chic
## 3328  -0.893091064  3.885192482   770.5  44.5 chic
## 3329   7.269910055 -2.727191727   771.5  32.0 chic
## 3330  -1.948566702 -2.277408315   772.5  23.0 chic
## 3331 -11.539400500  0.064160459   773.5  27.5 chic
## 3332  -7.144017278 -1.621966253   774.5  27.5 chic
## 3333  -5.474729866 -1.334100833   775.5  24.0 chic
## 3334  -1.129630897 -1.790564240   776.5  21.0 chic
## 3335  -3.706067166  0.197070158   777.5  23.5 chic
## 3336  -5.372733833  1.256623712   778.5  18.5 chic
## 3337 -12.206067170  4.393508492   779.5  33.0 chic
## 3338 -13.206067170 -0.259472179   780.5  43.5 chic
## 3339  -1.658696391 -3.834380713   781.5  31.5 chic
## 3340  -8.323820789 -2.718510297   782.5  35.5 chic
## 3341 -12.622733830 -1.623911464   783.5  44.5 chic
## 3342  -1.999652547  3.133237839   784.5  46.0 chic
## 3343  -9.849081771  5.475029195   785.5  46.5 chic
## 3344 -11.206067170  0.093619875   786.5  40.5 chic
## 3345 -12.829854610 -2.545536122   787.5  38.5 chic
## 3346   0.428588469 -3.095341643   788.5  20.0 chic
## 3347  -0.896386714 -1.201766156   789.5  17.0 chic
## 3348  -4.224697217  3.142821880   790.5  26.0 chic
## 3349   6.689475672 -1.893282888   791.5  15.0 chic
## 3350  -0.372733833 -1.414471008   792.5  12.5 chic
## 3351 -10.539400500  3.293325168   793.5  25.0 chic
## 3352  -3.977831297 -2.660954943   794.5  31.5 chic
## 3353   9.377266167 -3.253651072   795.5  20.5 chic
## 3354  -1.144954585 -0.939720328   796.5  11.5 chic
## 3355  -3.112642873 -1.851990752   797.5   8.0 chic
## 3356  -4.647365758  0.990959363   798.5  17.5 chic
## 3357  -5.277094159  0.567092380   799.5  26.0 chic
## 3358  -8.365906944  5.690037585   800.5  37.5 chic
## 3359  -6.168440787  7.753964526   801.5  43.0 chic
## 3360  -9.122733833  5.929376478   802.5  45.0 chic
## 3361  -0.181363244  2.615216309   803.5  47.5 chic
## 3362  -0.331067166 -1.299564187   804.5  40.5 chic
## 3363   4.627266167 -2.965313407   805.5  34.5 chic
## 3364  -7.997733833 -2.037977151   806.5  31.0 chic
## 3365  -8.363027165  1.525899706   807.5  34.5 chic
## 3366  13.511544148 -3.365582696   808.5  35.5 chic
## 3367  14.599570532 -3.724261214   809.5  31.5 chic
## 3368   0.271300215 -2.895697585   810.5  29.5 chic
## 3369  -1.272519699 -1.067518093   811.5  35.0 chic
## 3370  -1.753389526  1.345081187   812.5  39.0 chic
## 3371  10.530101052  1.680097731   813.5  50.5 chic
## 3372   4.788737840 -3.149709874   814.5  35.5 chic
## 3373   9.794743856 -2.102554731   815.5  18.5 chic
## 3374   5.395045937  1.046425611   816.5  24.0 chic
## 3375  -0.384422027 -0.498119056   817.5  37.0 chic
## 3376   8.157079111 -1.143011794   818.5  38.5 chic
## 3377  -3.273588155  1.625874797   819.5  42.0 chic
## 3378  10.914695716 -3.113044445   820.5  36.5 chic
## 3379   9.819286292 -2.782032188   821.5  35.5 chic
## 3380  -0.960691686  1.703538708   822.5  47.5 chic
## 3381  -2.315456516  1.513939402   823.5  52.0 chic
## 3382   3.639617901 -3.710198182   824.5  33.5 chic
## 3383   6.119864790 -3.177862172   825.5  32.0 chic
## 3384   9.787190120 -3.493320595   826.5  33.0 chic
## 3385   6.203840191 -3.441222992   827.5  32.0 chic
## 3386   1.348465145 -2.164184817   828.5  30.0 chic
## 3387  -6.183236071 -2.023641030   829.5  36.0 chic
## 3388  -3.324784947  1.485980218   830.5  45.5 chic
## 3389  11.300716539  4.791269535   831.5  64.0 chic
## 3390  -2.298388560 -2.201178632   832.5  53.0 chic
## 3391  -0.557755033 -4.581628158   833.5  40.5 chic
## 3392  14.740981157 -4.215764689   834.5  37.5 chic
## 3393  -1.643800904 -3.318703784   835.5  40.0 chic
## 3394   1.042064970 -2.394721943   836.5  42.5 chic
## 3395  -0.978683778  0.090033402   837.5  47.0 chic
## 3396   9.157363514 -0.161958270   838.5  63.5 chic
## 3397   3.980702175 -0.810388384   839.5  62.5 chic
## 3398  -4.378915299 -2.522657561   840.5  52.0 chic
## 3399   0.095734696  1.541179116   841.5  52.0 chic
## 3400  -1.202454815 -1.983168192   842.5  51.5 chic
## 3401   2.862938143 -1.837382525   843.5  45.0 chic
## 3402  -0.416844630  1.765345695   844.5  52.5 chic
## 3403   5.051669914 -0.766122493   845.5  61.0 chic
## 3404  -2.508613871 -2.136379561   846.5  45.5 chic
## 3405  -2.582954772  1.537270351   847.5  45.0 chic
## 3406  -2.493725639 -1.685370665   848.5  44.0 chic
## 3407   0.927199151 -4.049562099   849.5  40.5 chic
## 3408   6.339917156 -1.696369141   850.5  43.5 chic
## 3409   4.488461213 -0.910869805   851.5  50.0 chic
## 3410  -0.387446558  0.294820568   852.5  52.5 chic
## 3411   5.700922559 -0.944590780   853.5  52.5 chic
## 3412   8.183987113 -2.924925177   854.5  47.5 chic
## 3413   4.591012239 -2.009665359   855.5  49.0 chic
## 3414   1.896064639 -1.802449670   856.5  45.5 chic
## 3415  -2.709721427 -0.125475370   857.5  50.5 chic
## 3416 -14.050590380 -1.762756466   858.5  57.0 chic
## 3417 -15.202951760 -2.023374251   859.5  58.0 chic
## 3418  -2.892415637 -2.733499848   860.5  51.0 chic
## 3419   4.996290571 -2.915919939   861.5  45.0 chic
## 3420   8.444118686 -2.562751608   862.5  42.5 chic
## 3421  -4.131124108 -0.602610976   863.5  45.0 chic
## 3422   4.451365816 -0.790055859   864.5  47.5 chic
## 3423  -1.487414574 -1.957897804   865.5  54.5 chic
## 3424 -13.921099870 -2.630201122   866.5  53.5 chic
## 3425   7.257637709 -1.954453419   867.5  69.5 chic
## 3426  17.331583343  0.348290765   868.5  81.0 chic
## 3427  13.971860499  1.727866227   869.5  81.0 chic
## 3428   4.565810122 -0.066295053   870.5  64.0 chic
## 3429   3.880444290 -0.977535058   871.5  65.0 chic
## 3430   3.714683122  1.235860451   872.5  67.0 chic
## 3431  -3.519878946 -1.138809917   873.5  56.0 chic
## 3432   3.165421637 -3.257666568   874.5  52.0 chic
## 3433  10.030546033 -1.644254787   875.5  51.5 chic
## 3434  17.354887168 -1.649563006   876.5  54.0 chic
## 3435  12.736252953 -3.226630048   877.5  51.0 chic
## 3436   8.029088134 -3.055364743   878.5  51.5 chic
## 3437  11.594676640 -1.471300725   879.5  51.0 chic
## 3438   2.748321752  0.216426828   880.5  51.5 chic
## 3439  12.842928775  0.900059518   881.5  57.5 chic
## 3440  10.293876264  1.203112716   882.5  62.5 chic
## 3441  21.764212574 -1.328897924   883.5  67.5 chic
## 3442   1.817249885 -1.688167173   884.5  60.5 chic
## 3443 -12.496689880 -1.198409830   885.5  57.5 chic
## 3444   8.487423053  1.371621875   886.5  61.5 chic
## 3445   0.065406014 -0.927168724   887.5  63.5 chic
## 3446  -2.017477185 -2.156558738   888.5  54.5 chic
## 3447   1.701307748 -2.183042756   889.5  53.0 chic
## 3448   5.257940098 -3.017336278   890.5  56.5 chic
## 3449   2.650369676 -1.707921156   891.5  61.5 chic
## 3450  12.408657693 -2.228077003   892.5  60.0 chic
## 3451   4.466697423  0.253392171   893.5  65.5 chic
## 3452  13.288992456  3.388549958   894.5  75.5 chic
## 3453  10.400415037 -0.782909199   895.5  71.5 chic
## 3454  13.543226003  0.516708182   896.5  71.0 chic
## 3455  18.326696964  0.695846850   897.5  72.5 chic
## 3456  12.396179243 -2.917737625   898.5  74.0 chic
## 3457   4.060422240 -2.126582175   899.5  73.5 chic
## 3458   1.244266775 -1.470885222   900.5  69.5 chic
## 3459   7.237539740 -0.630143593   901.5  73.5 chic
## 3460   6.254380153 -1.511184011   902.5  76.5 chic
## 3461  12.923392861 -0.374157617   903.5  71.0 chic
## 3462   8.617164449 -0.713467116   904.5  61.0 chic
## 3463   3.060668572 -1.630977423   905.5  68.5 chic
## 3464   4.344948605 -2.205607388   906.5  65.5 chic
## 3465   8.959103896  0.143580046   907.5  68.0 chic
## 3466  27.003779126  7.566218715   908.5  74.0 chic
## 3467  19.580635335  8.457976606   909.5  77.5 chic
## 3468  37.353165136  6.562600702   910.5  83.0 chic
## 3469  17.908724051 -0.841533474   911.5  83.0 chic
## 3470  11.716219341 -0.339951424   912.5  75.5 chic
## 3471  10.002906014  1.725482275   913.5  73.0 chic
## 3472   2.894427696 -2.259071232   914.5  65.0 chic
## 3473   3.838934034 -0.299169090   915.5  64.0 chic
## 3474  11.368500305  3.719180982   916.5  70.5 chic
## 3475  34.483510020  5.200987340   917.5  73.0 chic
## 3476  30.976128943  1.599706093   918.5  78.0 chic
## 3477   6.338716883  0.177379235   919.5  71.5 chic
## 3478   0.013807748 -2.698978945   920.5  63.0 chic
## 3479  -0.450498348 -2.270099401   921.5  63.5 chic
## 3480  15.068684772 -0.921507887   922.5  66.0 chic
## 3481  11.138807748  2.507426569   923.5  73.0 chic
## 3482  11.293630629 -1.443230278   924.5  74.5 chic
## 3483  -2.304563533  0.422407999   925.5  69.0 chic
## 3484   1.641223556 -1.249798266   926.5  71.5 chic
## 3485   5.287280882  0.602500679   927.5  76.5 chic
## 3486  -1.005181715 -0.844639666   928.5  73.5 chic
## 3487   4.331531178 -2.540153726   929.5  79.0 chic
## 3488   1.242198272 -2.156975318   930.5  72.0 chic
## 3489   3.020775136 -2.697101449   931.5  65.0 chic
## 3490   1.583275136 -2.832556923   932.5  61.5 chic
## 3491   1.351149181  1.376604297   933.5  65.5 chic
## 3492  -1.837141175  0.659865495   934.5  70.5 chic
## 3493  -1.925384445  1.972453299   935.5  73.5 chic
## 3494  -4.264121640 -1.001292030   936.5  68.5 chic
## 3495  -4.017523212 -0.542992794   937.5  69.5 chic
## 3496  -0.043553717  0.600399783   938.5  68.5 chic
## 3497   5.307468275 -2.552407657   939.5  67.5 chic
## 3498   1.462417477 -1.771573602   940.5  70.0 chic
## 3499  -4.192748098 -1.869828948   941.5  68.5 chic
## 3500  -6.623953596 -1.158390864   942.5  65.5 chic
## 3501  -5.532875666 -1.393695311   943.5  66.5 chic
## 3502  -2.308297708 -1.021143882   944.5  67.5 chic
## 3503   3.229608596  2.532122969   945.5  68.5 chic
## 3504  20.404037069  0.236310169   946.5  74.0 chic
## 3505  16.797048238 -1.313616549   947.5  82.0 chic
## 3506  10.489955123 -1.333243318   948.5  79.5 chic
## 3507   5.452657643  0.055182178   949.5  83.0 chic
## 3508   5.098534301 -0.701073872   950.5  77.0 chic
## 3509  -2.206131718 -0.109039265   951.5  71.0 chic
## 3510   2.355702823 -2.177063874   952.5  68.5 chic
## 3511  -2.554296264  0.012798032   953.5  69.5 chic
## 3512   2.370907504 -0.284552558   954.5  71.0 chic
## 3513   4.517274533 -0.918313139   955.5  74.0 chic
## 3514   1.132233602  0.782966954   956.5  76.0 chic
## 3515  -4.731545109 -1.434304974   957.5  69.5 chic
## 3516  -5.616208999 -2.069771664   958.5  68.5 chic
## 3517   0.406621790 -1.087421264   959.5  69.0 chic
## 3518   3.549708483 -1.736889205   960.5  70.5 chic
## 3519   4.763534434 -2.210586066   961.5  76.0 chic
## 3520  -0.529038718 -0.330841808   962.5  77.0 chic
## 3521  -0.001421573 -0.294808777   963.5  78.0 chic
## 3522   8.847059975 -0.431962224   964.5  79.0 chic
## 3523  -4.231460557 -2.902198729   965.5  70.0 chic
## 3524   1.366302563 -1.671386818   966.5  71.0 chic
## 3525   6.602259393  0.485964394   967.5  69.5 chic
## 3526  11.565454598  0.100573328   968.5  72.0 chic
## 3527   4.840566935 -3.014182414   969.5  70.0 chic
## 3528   0.296293229 -0.004173680   970.5  68.5 chic
## 3529  14.617650003 -2.245132855   971.5  69.5 chic
## 3530  10.956418118 -2.484098766   972.5  69.0 chic
## 3531  13.000264762  0.229539589   973.5  70.5 chic
## 3532  17.050457667 -0.155512598   974.5  73.5 chic
## 3533  23.448288457 -0.220266186   975.5  71.0 chic
## 3534  12.960508668  1.911183586   976.5  73.0 chic
## 3535  17.216912488  2.046182812   977.5  73.0 chic
## 3536  22.311159551 -2.152472344   978.5  74.0 chic
## 3537  20.618361857 -1.189166983   979.5  74.0 chic
## 3538   1.173125667 -2.396024419   980.5  73.5 chic
## 3539   0.414886294 -1.614568801   981.5  73.0 chic
## 3540  -0.326099732 -2.657059978   982.5  68.5 chic
## 3541  -4.701099732  0.617495678   983.5  69.5 chic
## 3542  -9.614662548 -1.582661938   984.5  67.0 chic
## 3543 -11.369618430 -1.993346874   985.5  58.0 chic
## 3544  -8.993247435 -1.684934047   986.5  56.0 chic
## 3545 -13.786285100 -2.557678927   987.5  53.0 chic
## 3546  -9.284433065 -1.928472245   988.5  59.0 chic
## 3547  -6.575291517 -2.117239385   989.5  58.5 chic
## 3548  -2.329387362 -3.496913845   990.5  60.5 chic
## 3549  -7.258349471 -3.215040132   991.5  62.0 chic
## 3550  -3.474750810 -0.597907921   992.5  61.0 chic
## 3551 -10.083396460  0.093116050   993.5  58.5 chic
## 3552 -10.500026410 -0.698303689   994.5  64.0 chic
## 3553  -3.245581372 -1.721496175   995.5  62.5 chic
## 3554 -15.398603940 -0.465728488   996.5  59.0 chic
## 3555  -7.167031325 -0.940598210   997.5  57.0 chic
## 3556  -8.639511168 -1.269237815   998.5  53.0 chic
## 3557  -7.165069360 -3.237912261   999.5  60.5 chic
## 3558 -12.569997710 -2.707808045  1000.5  58.5 chic
## 3559 -11.754665780 -1.171021003  1001.5  53.5 chic
## 3560  -7.479974326  2.161635727  1002.5  57.5 chic
## 3561  -5.873764831  3.710620757  1003.5  63.0 chic
## 3562  -0.039132083  1.050855580  1004.5  62.0 chic
## 3563  -4.698682444 -1.310338721  1005.5  57.0 chic
## 3564   0.616197188 -1.497533194  1006.5  46.5 chic
## 3565  -8.647598137 -0.729446434  1007.5  47.0 chic
## 3566 -12.103727140  3.094971239  1008.5  53.5 chic
## 3567   4.820627902 -0.115350465  1009.5  63.5 chic
## 3568  -5.390066362 -1.473521912  1010.5  53.5 chic
## 3569 -11.225853360 -3.112088053  1011.5  45.5 chic
## 3570 -14.367808020 -1.024857612  1012.5  47.5 chic
## 3571 -10.990683740 -2.796875151  1013.5  42.5 chic
## 3572 -11.925092930 -0.432420852  1014.5  42.0 chic
## 3573  -5.265599997  1.124552947  1015.5  61.0 chic
## 3574   5.284709081  1.505730049  1016.5  66.0 chic
## 3575  -1.669897619 -1.732972772  1017.5  57.0 chic
## 3576  -7.897916014  1.437424032  1018.5  64.0 chic
## 3577 -13.606621520 -1.254930076  1019.5  63.0 chic
## 3578   0.920800979 -2.038325450  1020.5  59.0 chic
## 3579 -10.319986630 -0.571718613  1021.5  46.5 chic
## 3580 -11.601158480  0.415052056  1022.5  45.0 chic
## 3581 -13.319298020  0.227082756  1023.5  47.5 chic
## 3582 -12.989476270  4.054015843  1024.5  53.0 chic
## 3583 -14.508028100 -1.436555670  1025.5  55.5 chic
## 3584 -14.971232640 -1.257235406  1026.5  43.0 chic
## 3585 -13.540935950  0.194545764  1027.5  48.0 chic
## 3586  -9.016684521  1.029172077  1028.5  52.5 chic
## 3587 -10.591554410 -1.058902627  1029.5  62.0 chic
## 3588  -6.561262129 -0.927922529  1030.5  59.0 chic
## 3589 -14.061978860  0.574864445  1031.5  46.5 chic
## 3590 -13.173496140 -1.792907098  1032.5  50.5 chic
## 3591  -5.437492351 -2.760619900  1033.5  44.5 chic
## 3592  -6.869030348 -1.642839887  1034.5  33.0 chic
## 3593  -8.336295277 -2.502450064  1035.5  31.0 chic
## 3594  -9.506437461 -1.282300380  1036.5  29.0 chic
## 3595 -11.009328550  2.314181859  1037.5  35.5 chic
## 3596 -15.616638710  5.461391095  1038.5  42.0 chic
## 3597 -16.365215380 -1.743923163  1039.5  46.5 chic
## 3598 -14.718422220 -3.302553165  1040.5  54.0 chic
## 3599 -12.874230030 -2.739667033  1041.5  43.0 chic
## 3600 -13.855501930 -0.428671482  1042.5  37.0 chic
## 3601  -6.914828947 -3.080055801  1043.5  34.5 chic
## 3602  -3.404969792 -3.419595816  1044.5  28.0 chic
## 3603  -7.508391412 -2.275189423  1045.5  26.5 chic
## 3604  -9.610884265 -2.400405409  1046.5  23.0 chic
## 3605 -13.658021440 -1.335629556  1047.5  22.5 chic
## 3606  -4.076190325 -1.929473726  1048.5  25.0 chic
## 3607  -9.297975670  4.999596383  1049.5  40.0 chic
## 3608  -9.258059061  3.176654336  1050.5  48.0 chic
## 3609  -6.248209984 -2.571025018  1051.5  41.5 chic
## 3610 -13.088754860 -0.643280402  1052.5  31.0 chic
## 3611 -14.070330120  1.460474985  1053.5  29.5 chic
## 3612  -4.485418593 -1.028798637  1054.5  29.0 chic
## 3613  -5.097822341 -3.390091563  1055.5  34.0 chic
## 3614 -13.972454270 -0.180889715  1056.5  34.5 chic
## 3615 -14.508253380 -0.259215268  1057.5  35.5 chic
## 3616 -11.066433200 -2.022400200  1058.5  35.0 chic
## 3617  -3.016411310 -1.515130740  1059.5  26.5 chic
## 3618  -9.701919541 -0.459754911  1060.5  20.0 chic
## 3619 -14.038816570  3.518782767  1061.5  22.5 chic
## 3620 -12.007825150  5.635891257  1062.5  29.5 chic
## 3621 -13.796028110  5.244144335  1063.5  33.5 chic
## 3622 -13.675596280 -2.049988546  1064.5  40.5 chic
## 3623  -8.145541144 -3.118562134  1065.5  30.5 chic
## 3624 -10.385527780  0.113592448  1066.5  27.5 chic
## 3625 -14.513966650 -1.463777540  1067.5  30.0 chic
## 3626 -14.746333690  0.301573757  1068.5  26.5 chic
## 3627 -14.414149800 -0.798796716  1069.5  32.5 chic
## 3628 -14.839551160  3.149299212  1070.5  33.5 chic
## 3629  -9.616794750  2.708179163  1071.5  30.5 chic
## 3630  -5.864763999 -2.342100257  1072.5  28.5 chic
## 3631 -12.432985240  0.241628733  1073.5  28.5 chic
## 3632 -14.831309400 10.106769519  1074.5  35.5 chic
## 3633 -12.986499230 -2.291081710  1075.5  37.0 chic
## 3634 -13.671381470 -1.831627083  1076.5  36.5 chic
## 3635 -14.442936230  2.297728794  1077.5  34.5 chic
## 3636 -14.668750570  2.632965375  1078.5  36.5 chic
## 3637 -10.365062790  0.094199901  1079.5  38.0 chic
## 3638  -8.973808208  2.549592509  1080.5  29.0 chic
## 3639 -11.600754180 -0.609105431  1081.5  24.0 chic
## 3640  -6.148293373 -1.914674416  1082.5  13.0 chic
## 3641  -8.265738428 -1.706281330  1083.5   8.0 chic
## 3642  -9.621165186  1.105408936  1084.5   9.5 chic
## 3643  -7.385015918  0.262166964  1085.5  26.5 chic
## 3644 -13.759424090 -0.642619370  1086.5  34.5 chic
## 3645 -13.283233610 -3.291159008  1087.5  42.5 chic
## 3646  -1.096973564 -2.852655640  1088.5  20.0 chic
## 3647  -2.138739523 -2.235285337  1089.5   4.5 chic
## 3648 -12.512823770  3.891716693  1090.5  14.0 chic
## 3649 -14.730314610 -1.057145434  1091.5  26.0 chic
## 3650 -13.803459760 -2.729165861  1092.5  37.0 chic
## 3651 -12.639879240 -3.705787317  1093.5  30.0 chic
## 3652 -14.153844440 -0.427593329  1094.5  27.5 chic
## 3653 -14.024842550 -1.370994040  1095.5  27.5 chic
## 3654 -14.804729330 -1.365202469  1096.5  36.0 chic
## 3655 -14.677359870 -1.830637935  1097.5  45.0 chic
## 3656 -14.102806250 -1.350109057  1098.5  40.0 chic
## 3657 -12.754248560 -2.820538949  1099.5  51.5 chic
## 3658   0.540040893 -3.114230409  1100.5  27.0 chic
## 3659  -4.042250774 -3.776645267  1101.5  17.0 chic
## 3660  -8.697021305 -1.163525238  1102.5  16.0 chic
## 3661 -12.039778250 -0.962327383  1103.5  19.0 chic
## 3662  -6.599257719 -2.886637100  1104.5  26.0 chic
## 3663  -9.172189316 -3.025620181  1105.5  16.0 chic
## 3664  -3.634734136 -0.598304248  1106.5   1.5 chic
## 3665  -4.081309479 -1.713481239  1107.5   1.0 chic
## 3666 -10.479722360  2.300227107  1108.5   3.0 chic
## 3667 -12.372574530  9.764160457  1109.5  10.0 chic
## 3668 -13.638027830 10.173231509  1110.5  19.0 chic
## 3669  -0.168647714 -2.717268898  1111.5   9.5 chic
## 3670  -3.911907147  0.374844524  1112.5  -3.0 chic
## 3671  -6.696299687  2.781956872  1113.5   0.0 chic
## 3672 -10.458093180  5.169913239  1114.5  14.0 chic
## 3673  -2.801844386  4.897563825  1115.5  31.0 chic
## 3674  -7.621554715  0.413098448  1116.5  35.0 chic
## 3675   1.607913733 -2.427933055  1117.5  36.5 chic
## 3676  -0.270752438 -2.213182366  1118.5  26.0 chic
## 3677 -14.065624230 -1.114289981  1119.5  32.0 chic
## 3678  -2.882702987 -3.131237086  1120.5  14.5 chic
## 3679 -10.244539070  0.347596105  1121.5  11.0 chic
## 3680  -9.754821042  4.744645889  1122.5  17.0 chic
## 3681  -5.848259045 -0.561882015  1123.5   2.0 chic
## 3682 -13.073816220  4.243638255  1124.5   8.0 chic
## 3683 -13.004905970  8.529655459  1125.5  16.5 chic
## 3684  -7.881672894  7.815627511  1126.5  31.5 chic
## 3685  -3.448014940  1.110844027  1127.5  35.0 chic
## 3686  -8.258293264  0.432991802  1128.5  36.5 chic
## 3687   2.885688217 -1.499209935  1129.5  30.0 chic
## 3688  -7.866627577 -2.203455984  1130.5  34.5 chic
## 3689  -5.264755185 -1.565964686  1131.5  30.0 chic
## 3690  -3.778112229 -2.264231834  1132.5  26.0 chic
## 3691  -6.647966044 -0.914306818  1133.5  25.5 chic
## 3692  -0.458143728 -3.011500149  1134.5  25.5 chic
## 3693  -7.810153078  1.860000576  1135.5  26.0 chic
## 3694  -6.014226373  0.401499960  1136.5  27.0 chic
## 3695   0.284599593 -0.707016732  1137.5  23.5 chic
## 3696   2.884204925 -1.421095198  1138.5  21.0 chic
## 3697  -0.036783051  2.311020955  1139.5  20.5 chic
## 3698  -4.754548758  1.575589482  1140.5  25.5 chic
## 3699  -0.681126962  0.605277054  1141.5  20.0 chic
## 3700  -0.188554732 -0.881505347  1142.5  18.5 chic
## 3701  -2.910226414  2.872301720  1143.5  30.0 chic
## 3702   5.085753375  4.105291544  1144.5  48.5 chic
## 3703  -2.172317953  0.008213100  1145.5  37.5 chic
## 3704 -13.206848950 -1.817161447  1146.5  35.5 chic
## 3705  -4.100456348 -4.037327974  1147.5  36.0 chic
## 3706   1.773310838 -2.408473072  1148.5  26.0 chic
## 3707   6.651705729 -2.220296604  1149.5  28.0 chic
## 3708   0.177006709 -1.343050723  1150.5  21.5 chic
## 3709  -7.807581551  1.057723992  1151.5  25.5 chic
## 3710 -13.279001430 -0.611635924  1152.5  36.5 chic
## 3711  -5.469681147 -2.901548890  1153.5  34.5 chic
## 3712  -4.697420014 -1.701599791  1154.5  37.5 chic
## 3713  -7.670504857 -1.382854023  1155.5  45.5 chic
## 3714  11.248941488 -2.710159347  1156.5  35.0 chic
## 3715   6.633295509 -2.248882697  1157.5  33.5 chic
## 3716  -4.931951744 -1.314464495  1158.5  38.0 chic
## 3717  -2.716210512 -1.844732821  1159.5  33.0 chic
## 3718   4.468708118 -2.196947846  1160.5  26.5 chic
## 3719  -1.860153307  1.859085656  1161.5  35.5 chic
## 3720   9.321104884 -2.169371411  1162.5  39.0 chic
## 3721  -6.228115274 -2.482868384  1163.5  37.0 chic
## 3722  -0.012079342  2.392144172  1164.5  44.0 chic
## 3723   9.419565695 -2.005279670  1165.5  37.0 chic
## 3724   9.827143531 -1.085759342  1166.5  33.5 chic
## 3725   8.887259759 -1.958668976  1167.5  37.5 chic
## 3726   3.515700376 -3.257439609  1168.5  26.5 chic
## 3727  11.179554879 -3.001513830  1169.5  19.0 chic
## 3728   6.071808400  0.014524983  1170.5  24.5 chic
## 3729   2.965015444  5.013391500  1171.5  45.0 chic
## 3730  20.937667817 -3.140843920  1172.5  33.5 chic
## 3731   8.286754575  0.381880375  1173.5  35.5 chic
## 3732   6.067008668  1.144079249  1174.5  46.0 chic
## 3733  -3.376394109  1.331145465  1175.5  53.5 chic
## 3734   8.097795705 -1.577149456  1176.5  37.5 chic
## 3735   4.732198275 -2.979548066  1177.5  32.5 chic
## 3736  -3.170211220 -1.628673466  1178.5  33.0 chic
## 3737  -6.231156491 -1.781876640  1179.5  40.5 chic
## 3738   0.196573800  0.441386025  1180.5  44.0 chic
## 3739   3.960673687  3.309574503  1181.5  60.5 chic
## 3740  -7.901978690 -0.489458984  1182.5  55.5 chic
## 3741  -3.905588193 -3.051734915  1183.5  43.5 chic
## 3742   5.415428556 -3.790231670  1184.5  37.5 chic
## 3743   4.191163063 -1.422329031  1185.5  38.5 chic
## 3744  -5.264391266  1.970399982  1186.5  44.5 chic
## 3745  -9.346159085  4.235159046  1187.5  53.0 chic
## 3746  -8.813233502  4.934689207  1188.5  59.5 chic
## 3747   7.743411916  1.308859145  1189.5  62.5 chic
## 3748   3.992231863 -3.659178212  1190.5  60.5 chic
## 3749   6.327123143 -2.481845899  1191.5  45.0 chic
## 3750   5.623523906 -2.491338704  1192.5  34.0 chic
## 3751  -0.677093441 -2.878347340  1193.5  28.5 chic
## 3752   1.709373181 -2.050833262  1194.5  30.0 chic
## 3753   0.729644456 -1.357849029  1195.5  30.5 chic
## 3754   4.462222095 -3.311367349  1196.5  33.5 chic
## 3755   0.247147237 -3.078645136  1197.5  33.5 chic
## 3756   6.920948714 -1.679949748  1198.5  38.5 chic
## 3757  -3.418004704  3.912236473  1199.5  41.5 chic
## 3758   6.082737426  3.049610342  1200.5  49.0 chic
## 3759  -1.076196435 -1.723356590  1201.5  43.0 chic
## 3760  -4.010735482 -1.449537384  1202.5  40.5 chic
## 3761  -9.257615227  4.834234154  1203.5  40.0 chic
## 3762   0.773467551  1.885942046  1204.5  45.5 chic
## 3763   2.292892322 11.112982469  1205.5  49.0 chic
## 3764  14.374404092 -1.407423546  1206.5  45.0 chic
## 3765   7.556948968 -2.752410115  1207.5  43.0 chic
## 3766   4.999404092 -2.841483967  1208.5  48.5 chic
## 3767   0.998944337 -2.326215041  1209.5  47.5 chic
## 3768   3.545915364 -0.358967672  1210.5  48.0 chic
## 3769   0.596792804  4.186180730  1211.5  49.5 chic
## 3770   6.369418651  2.944630802  1212.5  47.5 chic
## 3771  -1.528489421  6.246216537  1213.5  50.5 chic
## 3772  12.581864005  0.744709220  1214.5  60.0 chic
## 3773   1.713174755  0.172391105  1215.5  55.0 chic
## 3774  -2.089268493 -2.006401654  1216.5  48.5 chic
## 3775 -10.327844360 -0.899485238  1217.5  48.5 chic
## 3776   0.602544961 -3.358456448  1218.5  49.0 chic
## 3777   8.845259964 -2.087737270  1219.5  51.5 chic
## 3778   8.455391829 -1.093971462  1220.5  62.0 chic
## 3779   0.882986569 -0.562280649  1221.5  55.5 chic
## 3780  -1.365484389 -0.793303032  1222.5  52.5 chic
## 3781   6.998830876 -2.104459983  1223.5  61.0 chic
## 3782  -0.118072556 -2.749704041  1224.5  50.5 chic
## 3783  -0.184732321  1.304923836  1225.5  52.5 chic
## 3784   6.758050012  0.788265762  1226.5  59.5 chic
## 3785   5.328854635 -3.385518799  1227.5  52.5 chic
## 3786  -3.699689118 -0.366165381  1228.5  45.5 chic
## 3787  -7.761357810 -1.650931806  1229.5  49.5 chic
## 3788  -7.297777937 -2.958402658  1230.5  45.0 chic
## 3789   0.304172670 -0.145056107  1231.5  51.0 chic
## 3790   7.003412253 -2.040824103  1232.5  50.5 chic
## 3791   3.134475524 -0.316870123  1233.5  59.5 chic
## 3792  -2.007009752 -2.733206609  1234.5  54.0 chic
## 3793  -4.593119445 -0.778743532  1235.5  51.5 chic
## 3794  -1.343621830 -2.539464843  1236.5  48.0 chic
## 3795  -3.178769711 -0.785525083  1237.5  50.5 chic
## 3796   2.710977502  2.492420824  1238.5  58.0 chic
## 3797  18.308148671  2.046901657  1239.5  68.5 chic
## 3798  15.152838886 -3.227592697  1240.5  50.0 chic
## 3799  10.238823735 -2.778992340  1241.5  50.0 chic
## 3800   7.549018983 -1.867683752  1242.5  56.0 chic
## 3801  -1.773675703 -1.487218705  1243.5  58.5 chic
## 3802  -3.786432184 -1.514730084  1244.5  58.0 chic
## 3803   7.592798413 -0.094127837  1245.5  61.0 chic
## 3804  13.633958652 -2.296872269  1246.5  59.0 chic
## 3805  22.516482004 -2.846098256  1247.5  60.5 chic
## 3806   7.005661399 -3.371681183  1248.5  58.0 chic
## 3807   9.069914761 -2.658228492  1249.5  61.5 chic
## 3808   6.685294243 -0.921086609  1250.5  59.0 chic
## 3809   7.970101282  0.871390535  1251.5  62.0 chic
## 3810   1.353064414 -2.871522010  1252.5  63.5 chic
## 3811  -0.082766521 -3.445355863  1253.5  58.5 chic
## 3812  18.475196254 -1.567634247  1254.5  61.0 chic
## 3813  19.001215686 -1.055492930  1255.5  62.5 chic
## 3814  16.444926491  3.022568506  1256.5  66.5 chic
## 3815  -1.657703909 -0.652269859  1257.5  67.5 chic
## 3816  -2.122468527 -0.285364253  1258.5  70.0 chic
## 3817   7.099341640  0.810860051  1259.5  71.0 chic
## 3818  -1.383511095 -2.425986691  1260.5  56.5 chic
## 3819  12.483999274 -1.502808374  1261.5  63.0 chic
## 3820  10.307126205 -1.691364810  1262.5  67.0 chic
## 3821   0.391862921 -2.067941918  1263.5  60.5 chic
## 3822   9.675848003  2.402014186  1264.5  67.0 chic
## 3823  10.502269671  3.790420241  1265.5  70.0 chic
## 3824  19.181597487 -1.034725499  1266.5  81.5 chic
## 3825   8.406586518 -2.217702218  1267.5  75.5 chic
## 3826  14.000675941  3.413698091  1268.5  77.0 chic
## 3827   6.144211628  0.453617452  1269.5  82.5 chic
## 3828  10.246010498  1.804589352  1270.5  84.0 chic
## 3829   7.835925087  0.814557308  1271.5  78.0 chic
## 3830   7.163062046  1.324751450  1272.5  73.5 chic
## 3831   8.791387631  4.110009038  1273.5  72.5 chic
## 3832  24.724309838  1.470146081  1274.5  74.0 chic
## 3833  33.334503682  2.891954569  1275.5  75.0 chic
## 3834  -2.209955039 -2.609792993  1276.5  76.0 chic
## 3835  15.151413119 -0.559765807  1277.5  77.5 chic
## 3836   7.985301452 -1.369423732  1278.5  79.5 chic
## 3837  -3.733098360 -0.369406086  1279.5  68.5 chic
## 3838 -10.760859800 -2.097257760  1280.5  61.5 chic
## 3839   0.056874448  0.801749531  1281.5  65.5 chic
## 3840   9.698116594  2.380277402  1282.5  70.0 chic
## 3841   4.952205952 -2.808112436  1283.5  65.5 chic
## 3842   7.792455437 -0.465162634  1284.5  71.0 chic
## 3843   6.257005553 -3.658149370  1285.5  62.0 chic
## 3844   2.384753087 -1.357399916  1286.5  64.5 chic
## 3845  12.119695501  0.278677011  1287.5  68.5 chic
## 3846  26.723987123  1.543552871  1288.5  74.5 chic
## 3847  16.635694594 -0.937101637  1289.5  82.5 chic
## 3848   6.853327729 -0.461204734  1290.5  83.0 chic
## 3849   3.263981353 -1.477417839  1291.5  79.0 chic
## 3850  10.815564288  1.358472821  1292.5  82.5 chic
## 3851  22.361164008  4.452230759  1293.5  84.0 chic
## 3852  -0.893065655  0.654837761  1294.5  79.0 chic
## 3853   6.898103004 -2.371128010  1295.5  74.5 chic
## 3854  13.506609380 -1.441202988  1296.5  76.5 chic
## 3855   8.405308857 -3.077905357  1297.5  72.0 chic
## 3856   7.149286387 -3.531597896  1298.5  69.0 chic
## 3857   9.240585211 -3.348348649  1299.5  69.0 chic
## 3858  17.865244815 -2.598356255  1300.5  72.5 chic
## 3859  14.296787371 -3.053956791  1301.5  76.0 chic
## 3860  22.165105872 -3.695833313  1302.5  85.5 chic
## 3861   6.075816503 -2.117375702  1303.5  81.5 chic
## 3862  -1.340405256 -2.344793528  1304.5  74.5 chic
## 3863  -1.111772500 -2.687506478  1305.5  66.5 chic
## 3864  -1.522455039 -3.145839811  1306.5  68.5 chic
## 3865   4.564061097  0.470057419  1307.5  69.0 chic
## 3866  18.809950993  2.142183266  1308.5  74.5 chic
## 3867  22.030187593  0.086243809  1309.5  81.5 chic
## 3868  14.113520926  0.242636503  1310.5  78.0 chic
## 3869   1.030187593 -3.874547762  1311.5  71.0 chic
## 3870  -2.178024476 -4.049552557  1312.5  62.5 chic
## 3871  -8.029279409  1.124522366  1313.5  64.5 chic
## 3872   3.443622628 -0.664167111  1314.5  67.0 chic
## 3873  11.300339732  0.612987657  1315.5  69.5 chic
## 3874   3.382005553 -1.409967410  1316.5  70.5 chic
## 3875   2.910625543 -2.538844375  1317.5  75.5 chic
## 3876  -6.006742358 -3.805065696  1318.5  67.5 chic
## 3877 -16.076327780 -4.300663602  1319.5  69.5 chic
## 3878  -3.561651735 -2.544453469  1320.5  68.5 chic
## 3879  -1.637750924 -2.236693629  1321.5  65.5 chic
## 3880  -1.029344428 -2.469621562  1322.5  77.0 chic
## 3881   9.358420559 -3.970139922  1323.5  80.0 chic
## 3882   0.399214959 -4.336818556  1324.5  68.5 chic
## 3883  -5.439381358 -4.554536501  1325.5  63.5 chic
## 3884  -5.534536410 -3.100046249  1326.5  64.0 chic
## 3885  -7.508511095 -2.680693072  1327.5  67.0 chic
## 3886  -3.865045951 -3.218594028  1328.5  63.5 chic
## 3887 -10.339539260 -2.302098594  1329.5  64.0 chic
## 3888  -4.198359802 -0.895165101  1330.5  65.5 chic
## 3889   6.699324231 -3.068460111  1331.5  70.5 chic
## 3890   4.676464274 -3.762712551  1332.5  65.5 chic
## 3891  -4.470229924 -1.804837591  1333.5  69.0 chic
## 3892  -6.308070333 -2.205156892  1334.5  77.0 chic
## 3893  -3.064381358 -3.453589322  1335.5  68.5 chic
## 3894  -4.785774407 -1.632992711  1336.5  67.0 chic
## 3895  -3.695025763 -1.319836747  1337.5  65.0 chic
## 3896   5.636926631 -2.988549963  1338.5  72.0 chic
## 3897   3.037696254  1.208626495  1339.5  70.5 chic
## 3898  -9.017546035 -2.574028295  1340.5  68.0 chic
## 3899  -5.036178947 -3.877271863  1341.5  58.5 chic
## 3900  -9.034439217 -1.374108609  1342.5  59.0 chic
## 3901  -6.261673096  1.391788043  1343.5  60.5 chic
## 3902  10.407657564  1.681538395  1344.5  74.5 chic
## 3903  -0.230788372 -3.849186571  1345.5  66.0 chic
## 3904  -5.026874457 -2.054368632  1346.5  67.5 chic
## 3905  -2.354366967 -3.214629405  1347.5  68.5 chic
## 3906  -9.193219041 -2.885970619  1348.5  64.5 chic
## 3907 -13.772392610 -1.088188592  1349.5  61.5 chic
## 3908 -11.275184660 -0.392108057  1350.5  61.5 chic
## 3909  -1.392165573  1.099071174  1351.5  63.5 chic
## 3910  10.363618695  0.147442059  1352.5  69.0 chic
## 3911  10.230512940  0.207884551  1353.5  69.5 chic
## 3912  16.124799700  0.233549668  1354.5  74.5 chic
## 3913  -5.499367170 -2.356922412  1355.5  70.0 chic
## 3914  -1.233724877  1.436738821  1356.5  69.0 chic
## 3915   1.210925087 -1.814027159  1357.5  73.0 chic
## 3916  -7.263827780 -3.140611069  1358.5  58.5 chic
## 3917 -13.623333020  0.649355513  1359.5  57.0 chic
## 3918 -16.468564530  1.907988775  1360.5  57.5 chic
## 3919 -10.550675770 -2.820031590  1361.5  59.0 chic
## 3920 -13.688281790  2.144812831  1362.5  56.5 chic
## 3921  -5.475323171 -0.383006080  1363.5  66.0 chic
## 3922  -6.918288372 -3.023944667  1364.5  60.0 chic
## 3923  -3.607507590 -0.500431044  1365.5  62.5 chic
## 3924  -6.544515399  2.869789918  1366.5  64.0 chic
## 3925  -3.954436989  1.069913053  1367.5  65.0 chic
## 3926 -10.167476330 -1.853936087  1368.5  59.5 chic
## 3927  -6.751073928 -2.642075234  1369.5  52.0 chic
## 3928  -6.085361861  4.443133085  1370.5  61.0 chic
## 3929   7.715285819  1.233456163  1371.5  73.0 chic
## 3930  -0.899429409 -1.208884142  1372.5  68.0 chic
## 3931   6.301854008 -0.765041341  1373.5  70.0 chic
## 3932  -0.531282663  5.271914221  1374.5  70.5 chic
## 3933  -3.440061562  4.012634246  1375.5  73.5 chic
## 3934   8.242198359  2.490419416  1376.5  72.5 chic
## 3935  -5.952719962 -1.714833518  1377.5  64.0 chic
## 3936 -12.287901830 -0.174632611  1378.5  59.0 chic
## 3937  -4.786594402 -0.261800848  1379.5  61.5 chic
## 3938   8.981812510  3.333511850  1380.5  70.0 chic
## 3939  -7.751847953 -1.532504564  1381.5  59.5 chic
## 3940 -12.211646200 -1.304289736  1382.5  42.5 chic
## 3941 -17.388370700  3.427575899  1383.5  42.5 chic
## 3942 -11.201920410  0.871848110  1384.5  47.5 chic
## 3943 -14.590943670  2.012737077  1385.5  45.5 chic
## 3944 -13.115651130 -0.832643267  1386.5  44.5 chic
## 3945  -7.092091937  2.431107543  1387.5  50.0 chic
## 3946 -14.292247700 -0.007880361  1388.5  45.5 chic
## 3947 -10.046424190 -2.445548286  1389.5  42.5 chic
## 3948  -7.512976700 -2.094682195  1390.5  36.0 chic
## 3949 -17.232064090  1.225656229  1391.5  44.5 chic
## 3950 -19.540115420  0.720591612  1392.5  46.0 chic
## 3951  -5.785023596 -2.272671111  1393.5  47.0 chic
## 3952   1.627999828 -3.661742521  1394.5  40.0 chic
## 3953  -9.005536650 -3.587934003  1395.5  32.5 chic
## 3954 -16.153403660  1.218350924  1396.5  39.0 chic
## 3955 -17.392110820  2.336063255  1397.5  44.0 chic
## 3956 -10.197212070 -0.003938697  1398.5  51.0 chic
## 3957  -9.191441083  0.975042811  1399.5  59.5 chic
## 3958 -11.501661470 -2.666032514  1400.5  50.0 chic
## 3959 -10.742252530 -2.848161320  1401.5  38.5 chic
## 3960 -18.975866090 -2.685669004  1402.5  36.5 chic
## 3961 -19.455874100 -3.200599396  1403.5  39.0 chic
## 3962 -16.430000270  1.251011909  1404.5  40.0 chic
## 3963 -13.526739850  1.701088987  1405.5  45.0 chic
## 3964  -2.364100572 -0.801983278  1406.5  43.0 chic
## 3965  -6.110519287  0.078378529  1407.5  44.0 chic
## 3966 -11.501618030  0.668086466  1408.5  42.5 chic
## 3967 -10.727569120 -0.180063942  1409.5  36.5 chic
## 3968 -13.825516100 -0.844950534  1410.5  28.5 chic
## 3969 -12.860819850 -0.522560538  1411.5  26.5 chic
## 3970 -11.905901250  1.773582496  1412.5  31.5 chic
## 3971  -4.087015495 -3.241699766  1413.5  36.0 chic
## 3972 -11.535635980 -3.286926620  1414.5  29.0 chic
## 3973  -5.298759182 -2.039126876  1415.5  22.0 chic
## 3974 -13.327769420  4.916425722  1416.5  25.5 chic
## 3975 -13.766622980  4.691569657  1417.5  32.0 chic
## 3976 -15.849953770  0.886332227  1418.5  27.5 chic
## 3977 -16.963647480  2.073460058  1419.5  33.5 chic
## 3978  -0.938309721 -2.274331363  1420.5  35.5 chic
## 3979  -9.740770417  1.185332584  1421.5  34.5 chic
## 3980  -7.984629241 -1.757436402  1422.5  26.0 chic
## 3981 -13.207905090  2.913174521  1423.5  26.5 chic
## 3982 -10.483599220  4.241496014  1424.5  47.0 chic
## 3983 -11.273415300  2.109596937  1425.5  43.0 chic
## 3984 -13.055127390  0.893546677  1426.5  37.5 chic
## 3985 -15.336696330 -2.485130185  1427.5  49.0 chic
## 3986 -14.885429950 -2.579957503  1428.5  45.5 chic
## 3987  -4.900281269 -3.503473130  1429.5  42.0 chic
## 3988   1.519056599 -3.579829463  1430.5  37.0 chic
## 3989 -11.338717910 -2.114067936  1431.5  36.5 chic
## 3990 -17.360871470 -1.963240758  1432.5  36.5 chic
## 3991 -13.656068030 -2.788575161  1433.5  32.0 chic
## 3992 -14.596190650 -3.275895750  1434.5  26.0 chic
## 3993 -11.288802340 -3.599985304  1435.5  28.5 chic
## 3994 -10.691273620 -2.614670515  1436.5  30.0 chic
## 3995 -14.085481540 -2.455306770  1437.5  28.5 chic
## 3996 -16.878159180  3.545247345  1438.5  33.0 chic
## 3997  -6.343664882 -2.383495142  1439.5  34.5 chic
## 3998  -2.300998409 -1.667473617  1440.5  33.5 chic
## 3999  -9.720486085 -2.431360471  1441.5  27.5 chic
## 4000 -13.298040920 -1.587300779  1442.5  26.5 chic
## 4001 -14.894697490  2.251693014  1443.5  31.0 chic
## 4002 -15.695372100 14.899766811  1444.5  37.5 chic
## 4003 -15.145749190  7.602800627  1445.5  39.0 chic
## 4004 -12.237174220  1.971229396  1446.5  37.0 chic
## 4005 -16.088880840  5.984400888  1447.5  38.5 chic
## 4006 -16.683325170  4.672105317  1448.5  40.5 chic
## 4007 -11.136465270 -2.990793405  1449.5  33.0 chic
## 4008  -3.739284296 -2.477059113  1450.5  32.5 chic
## 4009 -12.845051550 -2.319627760  1451.5  34.5 chic
## 4010 -16.328015430 -0.605456189  1452.5  34.0 chic
## 4011 -13.467701510 -1.184217924  1453.5  34.0 chic
## 4012 -12.599935360 -3.853316561  1454.5  30.5 chic
## 4013 -14.462317220 -1.043331605  1455.5  28.5 chic
## 4014 -13.824222420  1.322820964  1456.5  23.0 chic
## 4015 -14.108054280  0.944253885  1457.5  25.5 chic
## 4016 -16.383906010 -1.929716204  1458.5  29.5 chic
## 4017 -13.238195420 -2.090700666  1459.5  21.5 chic
## 4018  -8.783640769  0.406419490  1460.5  18.0 chic
## 4019  -4.284300269  5.731384302  1461.5  28.0 chic
## 4020  -9.462496616  3.524306676  1462.5  44.0 chic
## 4021  -7.584756890  3.538336634  1463.5  47.0 chic
## 4022 -11.548523440 -4.032755853  1464.5  45.0 chic
## 4023 -15.501240370 -2.946878360  1465.5  48.0 chic
## 4024 -11.943575400 -0.975528010  1466.5  43.0 chic
## 4025  -3.833914227 -4.490786427  1467.5  38.0 chic
## 4026  -2.725344072 -4.163295961  1468.5  34.0 chic
## 4027  -7.891191095 -2.997194217  1469.5  25.0 chic
## 4028  -7.161396574 -1.634640645  1470.5  12.0 chic
## 4029 -13.433677410  2.997538288  1471.5  18.0 chic
## 4030 -17.034455940  0.900626017  1472.5  23.0 chic
## 4031 -12.455003880 -0.253857171  1473.5   7.0 chic
## 4032  -9.683677406  0.363553522  1474.5  17.0 chic
## 4033 -14.431518580  0.051993044  1475.5  28.0 chic
## 4034 -10.106920610  1.720561675  1476.5  28.0 chic
## 4035 -15.574435990  0.832350799  1477.5  23.0 chic
## 4036 -15.327974800  1.769304871  1478.5  16.0 chic
## 4037 -16.795783020  3.075914532  1479.5  24.0 chic
## 4038 -15.094184850  3.023975310  1480.5  26.0 chic
## 4039 -15.017010740  3.465469613  1481.5  31.0 chic
## 4040 -18.017010740  8.592127349  1482.5  32.0 chic
## 4041 -16.929225550  4.245354571  1483.5  28.0 chic
## 4042 -16.467125090  0.883336325  1484.5  28.0 chic
## 4043  -8.386634604  1.404765966  1485.5  28.0 chic
## 4044 -16.940184450  1.377286097  1486.5  31.0 chic
## 4045 -17.528198150  4.736232885  1487.5  33.0 chic
## 4046 -17.531831270  2.083845775  1488.5  36.0 chic
## 4047 -14.933677410 -0.178114227  1489.5  37.0 chic
## 4048 -12.966455050 -0.784014933  1490.5  31.0 chic
## 4049 -12.772505280  8.173973096  1491.5  35.0 chic
## 4050 -11.950929940  5.123437794  1492.5  39.0 chic
## 4051  -7.838976122 -1.296333754  1493.5  33.0 chic
## 4052  -4.763000614 -1.143609277  1494.5  34.0 chic
## 4053   7.795789341 -4.800754417  1495.5  32.0 chic
## 4054  10.351548992 -5.289095176  1496.5  32.0 chic
## 4055   4.482989261 -2.511468896  1497.5  36.0 chic
## 4056   0.983614435 -1.458262268  1498.5  34.0 chic
## 4057   5.441322594 -3.174415945  1499.5  33.0 chic
## 4058 -14.308677410  5.197616018  1500.5  35.0 chic
## 4059 -12.901517510  6.304925145  1501.5  39.0 chic
## 4060 -15.100344070  0.275532166  1502.5  41.0 chic
## 4061  -5.366134032 -1.271011501  1503.5  35.0 chic
## 4062 -10.957921540  2.936848910  1504.5  36.0 chic
## 4063 -12.369786370  0.984895861  1505.5  38.0 chic
## 4064 -12.041357590  5.859557290  1506.5  43.0 chic
## 4065  -9.713517866  0.857844962  1507.5  42.0 chic
## 4066  -7.100344072 -4.092914511  1508.5  41.0 chic
## 4067  -1.334854862 -4.161887938  1509.5  39.0 chic
## 4068 -12.706124850 -1.481686236  1510.5  40.0 chic
## 4069  -8.608719573  0.158724723  1511.5  39.0 chic
## 4070  -5.121612612 -0.388820714  1512.5  38.0 chic
## 4071  -5.879535147  0.864176039  1513.5  44.0 chic
## 4072   0.609092263  2.601476090  1514.5  41.0 chic
## 4073 -10.215165220 -0.291370841  1515.5  45.0 chic
## 4074  -2.175417579  0.100215548  1516.5  42.0 chic
## 4075  -1.977184453  4.154441538  1517.5  54.0 chic
## 4076  -4.592345281 -2.004919192  1518.5  42.0 chic
## 4077  -6.808677406 -2.331175781  1519.5  41.0 chic
## 4078  -7.111431379 -1.375959883  1520.5  35.0 chic
## 4079 -12.850344070 -3.898754809  1521.5  33.0 chic
## 4080  -0.938692313 -3.710321478  1522.5  34.0 chic
## 4081   2.033713899 -2.168654812  1523.5  34.0 chic
## 4082   0.792392658 -1.047062333  1524.5  36.0 chic
## 4083  -8.403314482 -2.564909024  1525.5  34.0 chic
## 4084   3.566465738 -2.903494852  1526.5  36.0 chic
## 4085   3.314171811 -4.625010829  1527.5  38.0 chic
## 4086   9.192435857 -5.341278887  1528.5  26.0 chic
## 4087   0.434826543 -3.970712636  1529.5  18.0 chic
## 4088  -3.597139802 -1.185210352  1530.5  18.0 chic
## 4089  -6.773201340  0.961807817  1531.5  18.0 chic
## 4090  -4.259616327  4.206357785  1532.5  29.0 chic
## 4091   7.107989261 -1.780733481  1533.5  28.0 chic
## 4092   8.357989261 -2.000621311  1534.5  27.0 chic
## 4093  10.232989261  0.291824901  1535.5  30.0 chic
## 4094  -4.898509665 -0.729934395  1536.5  36.0 chic
## 4095 -15.671280700 -2.011915720  1537.5  45.0 chic
## 4096  -0.458887415 -3.267208566  1538.5  37.0 chic
## 4097  17.671225732 -5.026788249  1539.5  35.0 chic
## 4098  16.482989261 -4.062538127  1540.5  37.0 chic
## 4099   7.523164088 -1.135418074  1541.5  36.0 chic
## 4100  -4.681840503  0.021385246  1542.5  38.0 chic
## 4101  -5.223484191  0.273490597  1543.5  40.0 chic
## 4102  -2.913924186  1.559017685  1544.5  51.0 chic
## 4103  11.397657312 -1.349260639  1545.5  68.0 chic
## 4104  11.691317959 -0.193292933  1546.5  67.0 chic
## 4105   7.539456540 -1.926827064  1547.5  57.0 chic
## 4106   1.390102214 -0.712745848  1548.5  62.0 chic
## 4107   2.472242155 -1.724275529  1549.5  73.0 chic
## 4108   0.221446317 -2.207614239  1550.5  59.0 chic
## 4109  -1.187648719 -3.045562706  1551.5  44.0 chic
## 4110  -7.234485924 -3.494011136  1552.5  44.0 chic
## 4111  -3.567342815 -4.427183711  1553.5  41.0 chic
## 4112   7.771516213 -4.101318683  1554.5  41.0 chic
## 4113  -1.742342815 -1.812511221  1555.5  41.0 chic
## 4114  -5.482422180  2.110186181  1556.5  48.0 chic
## 4115  -0.975141297 -0.807144543  1557.5  52.0 chic
## 4116 -14.185781590 -3.596576761  1558.5  48.0 chic
## 4117   4.471387839 -3.372267944  1559.5  43.0 chic
## 4118   2.585739858 -0.731964888  1560.5  46.0 chic
## 4119   0.752630650  0.990580898  1561.5  50.0 chic
## 4120  21.901477623 -0.047832468  1562.5  64.0 chic
## 4121  10.788776072 -1.857878130  1563.5  62.0 chic
## 4122  -0.920441770 -2.051343500  1564.5  58.0 chic
## 4123  -8.027857736 -0.226194961  1565.5  53.0 chic
## 4124 -11.172101450 -3.503659285  1566.5  42.0 chic
## 4125  -2.699935416 -0.961281517  1567.5  45.0 chic
## 4126  -2.770443463  1.855125706  1568.5  50.0 chic
## 4127   7.269490808 -0.164270824  1569.5  52.0 chic
## 4128   1.407424266 -0.388022438  1570.5  52.0 chic
## 4129   8.198816409 -3.491387176  1571.5  50.0 chic
## 4130   7.658831267 -3.012426818  1572.5  53.0 chic
## 4131  -2.044706773  2.545330850  1573.5  56.0 chic
## 4132   3.375867847  1.774422456  1574.5  62.0 chic
## 4133   9.780303749 -1.496460000  1575.5  56.0 chic
## 4134   5.740420561 -2.298514794  1576.5  48.0 chic
## 4135   8.662121863 -1.562213424  1577.5  45.0 chic
## 4136   1.284245287 -0.491293777  1578.5  46.0 chic
## 4137  -6.298075737 -1.466916635  1579.5  54.0 chic
## 4138 -11.260147240 -2.303651781  1580.5  55.0 chic
## 4139 -10.077125430 -0.984268040  1581.5  59.0 chic
## 4140  -0.678454888 -0.931604900  1582.5  57.0 chic
## 4141   1.989175715 -2.203217812  1583.5  53.0 chic
## 4142   0.498278430 -0.383161230  1584.5  60.0 chic
## 4143  -2.112968615 -0.083213000  1585.5  62.0 chic
## 4144   1.189976496 -1.464948160  1586.5  68.0 chic
## 4145   0.357429567 -3.390814595  1587.5  57.0 chic
## 4146  16.046078564 -2.797174609  1588.5  62.0 chic
## 4147   5.253128733 -1.772139941  1589.5  58.0 chic
## 4148   7.694097312 -2.342151778  1590.5  58.0 chic
## 4149   2.492162543 -2.207714536  1591.5  61.0 chic
## 4150  12.439664191 -0.299882686  1592.5  63.0 chic
## 4151  10.630094840  0.122060132  1593.5  68.0 chic
## 4152  10.994077452  3.316285409  1594.5  68.0 chic
## 4153  15.630679125 -0.807687270  1595.5  75.0 chic
## 4154  13.818868103 -2.233510824  1596.5  72.0 chic
## 4155   4.783896462  1.346355938  1597.5  68.0 chic
## 4156  13.840883388  4.500988529  1598.5  76.0 chic
## 4157  12.856330857  4.200899190  1599.5  79.0 chic
## 4158  -0.192690441  1.312952528  1600.5  65.0 chic
## 4159   6.269490808  2.713109290  1601.5  64.0 chic
## 4160  -0.163537933  2.102775383  1602.5  57.0 chic
## 4161  10.894490808  4.163620132  1603.5  61.0 chic
## 4162  11.503855140 -1.801567206  1604.5  61.0 chic
## 4163  14.794795400 -1.679306933  1605.5  62.0 chic
## 4164   1.961462067  4.775358543  1606.5  63.0 chic
## 4165  15.008554446  4.488346173  1607.5  67.0 chic
## 4166  11.527430645 -0.573542001  1608.5  75.0 chic
## 4167   6.221222499 -1.218020452  1609.5  74.0 chic
## 4168  10.774655928  2.177904868  1610.5  73.0 chic
## 4169  16.772997524 -2.567754643  1611.5  70.0 chic
## 4170   3.816322594  2.171337041  1612.5  63.0 chic
## 4171   3.462188473 -0.485645031  1613.5  63.0 chic
## 4172  -8.369821212  0.564108970  1614.5  55.0 chic
## 4173   2.117497629 -0.832922993  1615.5  56.0 chic
## 4174  -2.079478193 -3.159369743  1616.5  50.0 chic
## 4175 -12.992433280 -1.308912747  1617.5  54.0 chic
## 4176   0.284164296 -1.337473722  1618.5  58.0 chic
## 4177  -3.893356837 -0.752909497  1619.5  56.0 chic
## 4178  -5.560912881 -4.120422047  1620.5  61.0 chic
## 4179   0.878128733 -0.396116315  1621.5  68.0 chic
## 4180  -2.090835704 -3.193549552  1622.5  68.0 chic
## 4181  10.452686231 -2.264324131  1623.5  74.0 chic
## 4182  11.626553255 -1.169439948  1624.5  69.0 chic
## 4183   7.147997524 -0.296608898  1625.5  67.0 chic
## 4184   6.103573077 -2.134051820  1626.5  70.0 chic
## 4185  -7.917251430 -0.128506627  1627.5  70.0 chic
## 4186  -0.496144860 -2.154855734  1628.5  73.0 chic
## 4187  13.951568953 -2.548712696  1629.5  77.0 chic
## 4188   7.939664191 -0.594124506  1630.5  73.0 chic
## 4189  10.144490808 -0.829087781  1631.5  78.0 chic
## 4190  12.147997524 -0.028754941  1632.5  79.0 chic
## 4191   6.534164296  1.436322410  1633.5  74.0 chic
## 4192  18.784164296  1.092185296  1634.5  74.0 chic
## 4193  10.769490808  0.091128828  1635.5  83.0 chic
## 4194  21.450975964 -1.483744098  1636.5  83.0 chic
## 4195  16.564664191 -0.555606542  1637.5  79.0 chic
## 4196  27.106330857 -1.116963276  1638.5  82.0 chic
## 4197  16.253128733 -1.611940445  1639.5  78.0 chic
## 4198  -1.060023504 -1.528264645  1640.5  77.0 chic
## 4199   3.522997524 -2.805644723  1641.5  74.0 chic
## 4200   2.628128733 -1.605890216  1642.5  70.0 chic
## 4201   5.853573077  2.023105218  1643.5  73.0 chic
## 4202  10.314664191  1.146825461  1644.5  79.0 chic
## 4203   4.378855140 -4.512410887  1645.5  69.0 chic
## 4204   3.436157475  3.600262344  1646.5  70.0 chic
## 4205   7.412787484 -1.109118239  1647.5  78.0 chic
## 4206  11.390867620 -3.613666594  1648.5  72.0 chic
## 4207  11.064664191 -2.157527016  1649.5  74.0 chic
## 4208   8.460376879 -0.379469781  1650.5  75.0 chic
## 4209  10.069097312 -3.226730055  1651.5  73.0 chic
## 4210  16.352824142  0.019493024  1652.5  71.0 chic
## 4211  16.200830962  0.473396246  1653.5  71.0 chic
## 4212  13.216780123  2.343389320  1654.5  73.0 chic
## 4213  15.474684760 -0.434754483  1655.5  78.0 chic
## 4214  13.896089356 -0.363341892  1656.5  79.0 chic
## 4215   0.963244288 -2.335527228  1657.5  80.0 chic
## 4216   5.477824142  0.273447232  1658.5  75.0 chic
## 4217  10.016066492  1.084021875  1659.5  77.0 chic
## 4218  11.611151024 -3.301366253  1660.5  81.0 chic
## 4219   5.727824142 -1.310983849  1661.5  79.0 chic
## 4220  14.481330857 -2.261326315  1662.5  85.0 chic
## 4221  -3.264236021 -1.298798738  1663.5  76.0 chic
## 4222  -2.215168030 -1.638925394  1664.5  73.0 chic
## 4223  -0.496871267  0.234072179  1665.5  72.0 chic
## 4224   1.960861077 -0.944062380  1666.5  70.0 chic
## 4225   5.644490808 -1.148922906  1667.5  70.0 chic
## 4226   5.022997524 -0.597551532  1668.5  74.0 chic
## 4227   3.754881582 -1.071468846  1669.5  79.0 chic
## 4228  11.711462067 -1.883797613  1670.5  77.0 chic
## 4229  -1.305902688 -1.068186008  1671.5  72.0 chic
## 4230   2.750020486 -2.981998793  1672.5  71.0 chic
## 4231   9.269876457 -2.208654399  1673.5  72.0 chic
## 4232  17.586462067 -0.602718326  1674.5  72.0 chic
## 4233  11.064976496  1.224363268  1675.5  78.0 chic
## 4234  -1.882502371 -3.850333571  1676.5  70.0 chic
## 4235   0.593136530 -4.222337108  1677.5  76.0 chic
## 4236  -4.365710077 -2.351983762  1678.5  78.0 chic
## 4237 -10.629694910 -3.761834825  1679.5  75.0 chic
## 4238  -2.526023774 -3.523034480  1680.5  77.0 chic
## 4239   0.644594566 -1.008985499  1681.5  77.0 chic
## 4240   6.667925060 -2.034862977  1682.5  76.0 chic
## 4241   1.753855140 -2.673234031  1683.5  71.0 chic
## 4242   7.894490808 -1.639183666  1684.5  70.0 chic
## 4243  11.111446572 -0.736728469  1685.5  71.0 chic
## 4244   3.999415236 -0.509337891  1686.5  71.0 chic
## 4245  -0.284270489 -1.606496060  1687.5  71.0 chic
## 4246   4.711462067 -0.856472042  1688.5  72.0 chic
## 4247   2.319097312 -0.626232902  1689.5  75.0 chic
## 4248  -0.529247527 -3.468705798  1690.5  68.0 chic
## 4249  -0.097569355  1.244463567  1691.5  69.0 chic
## 4250   3.314976496  0.296276590  1692.5  73.0 chic
## 4251  11.677768184  0.350577689  1693.5  80.0 chic
## 4252  12.478573077 -1.293336303  1694.5  78.0 chic
## 4253  24.398309830 -0.634531867  1695.5  81.0 chic
## 4254  -4.722569355 -0.129379355  1696.5  76.0 chic
## 4255   3.985763979 -0.863451377  1697.5  76.0 chic
## 4256   3.663377371 -1.895872943  1698.5  73.0 chic
## 4257   5.651405862 -3.050588760  1699.5  72.0 chic
## 4258  -9.090835704 -2.379350920  1700.5  69.0 chic
## 4259  -0.430132938 -0.864928156  1701.5  76.0 chic
## 4260   0.870250721 -2.968298498  1702.5  70.0 chic
## 4261  -1.647175858  0.498616199  1703.5  69.0 chic
## 4262  -4.365041095 -1.377924076  1704.5  69.0 chic
## 4263  -1.275582613 -1.827210792  1705.5  65.0 chic
## 4264   2.772997524 -0.561458829  1706.5  68.0 chic
## 4265  13.835081392 -1.693601976  1707.5  69.0 chic
## 4266  10.182644457 -1.751787401  1708.5  70.0 chic
## 4267  26.259964137  2.152677331  1709.5  81.0 chic
## 4268   6.817532773 -3.474823280  1710.5  66.0 chic
## 4269  -4.264236021 -2.533406816  1711.5  63.0 chic
## 4270  -5.136836050 -2.406028957  1712.5  63.0 chic
## 4271  -6.355002961 -1.353913766  1713.5  63.0 chic
## 4272   6.647544314  3.407095502  1714.5  71.0 chic
## 4273  13.761315105  0.469683713  1715.5  74.0 chic
## 4274  13.669795400  2.517909672  1716.5  74.0 chic
## 4275   0.294795400 -1.218264269  1717.5  72.0 chic
## 4276  -6.560023504 -3.688376101  1718.5  70.0 chic
## 4277   3.235763979 -3.755526774  1719.5  72.0 chic
## 4278   5.907863304 -3.290848744  1720.5  68.0 chic
## 4279   1.697295339 -1.404951025  1721.5  68.0 chic
## 4280  14.485763979  1.527988985  1722.5  72.0 chic
## 4281  -1.534160489  0.100335611  1723.5  76.0 chic
## 4282  -7.858936547 -2.030316336  1724.5  63.0 chic
## 4283  -2.262724336 -2.128311665  1725.5  58.0 chic
## 4284 -10.180902690  2.017855209  1726.5  56.0 chic
## 4285 -14.092061920  0.062940615  1727.5  59.0 chic
## 4286 -10.556406650 -2.676186867  1728.5  66.0 chic
## 4287  17.110763979 -1.542909203  1729.5  79.0 chic
## 4288  10.022292435 -1.428263512  1730.5  73.0 chic
## 4289  -3.000277921  0.478334223  1731.5  63.0 chic
## 4290 -14.765142530  3.000810379  1732.5  63.0 chic
## 4291  -8.507036115 -1.321370904  1733.5  64.0 chic
## 4292 -11.843789140 -2.707184733  1734.5  53.0 chic
## 4293  -9.784812108 -3.528325870  1735.5  49.0 chic
## 4294  -2.981040660 -1.952846834  1736.5  55.0 chic
## 4295  -2.625487388 -1.204559163  1737.5  60.0 chic
## 4296 -10.814368790 -3.859575870  1738.5  65.0 chic
## 4297 -10.492817740 -3.972665096  1739.5  65.0 chic
## 4298 -17.309682200 -1.122163909  1740.5  56.0 chic
## 4299  -9.915131267 -0.818378984  1741.5  54.0 chic
## 4300 -17.551091850  1.694995172  1742.5  53.0 chic
## 4301 -13.418611460  1.108543853  1743.5  53.0 chic
## 4302  -1.975704167 -1.617870421  1744.5  56.0 chic
## 4303 -10.082493180  1.382531367  1745.5  60.0 chic
## 4304 -14.187828090 -0.867401954  1746.5  49.0 chic
## 4305 -17.702552830 -0.383896539  1747.5  48.0 chic
## 4306 -14.519187470  1.693559048  1748.5  58.0 chic
## 4307   1.679342716  2.146458955  1749.5  68.0 chic
## 4308   2.075910173 -2.228310205  1750.5  67.0 chic
## 4309  -6.040363179 -4.085564743  1751.5  57.0 chic
## 4310 -13.278678030 -1.166070161  1752.5  52.0 chic
## 4311 -12.190476640 -1.880541912  1753.5  50.0 chic
## 4312 -11.489128860 -1.829628465  1754.5  48.0 chic
## 4313 -13.611497670  0.950139351  1755.5  45.0 chic
## 4314 -14.009164030  2.115172743  1756.5  52.0 chic
## 4315 -10.496732380  3.000394161  1757.5  54.0 chic
## 4316 -13.379144430  4.828819024  1758.5  55.0 chic
## 4317 -11.907796490  3.311599178  1759.5  62.0 chic
## 4318 -16.462140590  2.897593385  1760.5  63.0 chic
## 4319  -9.353061163 -3.336496343  1761.5  54.0 chic
## 4320 -18.734646550 -1.546426638  1762.5  52.0 chic
## 4321 -20.368319240 -2.264733058  1763.5  61.0 chic
## 4322 -18.472004940 -3.730010836  1764.5  57.0 chic
## 4323  -1.720754598 -4.281515465  1765.5  54.0 chic
## 4324   0.916574169 -3.881399724  1766.5  49.0 chic
## 4325   7.017162122 -3.983000987  1767.5  41.0 chic
## 4326  -2.101974600 -3.125308153  1768.5  37.0 chic
## 4327 -16.749168770 -2.279247078  1769.5  39.0 chic
## 4328 -13.604964400 -1.074208111  1770.5  38.0 chic
## 4329 -14.601426580  0.183556487  1771.5  35.0 chic
## 4330 -14.868572710 -2.089249022  1772.5  37.0 chic
## 4331 -13.458346780  0.105586410  1773.5  43.0 chic
## 4332  -6.280432661 -1.846370366  1774.5  52.0 chic
## 4333  -5.298782194 -1.792864608  1775.5  41.0 chic
## 4334 -17.315082120  3.293340772  1776.5  40.0 chic
## 4335 -10.589317930  1.463309702  1777.5  43.0 chic
## 4336 -12.108992700  0.418710527  1778.5  50.0 chic
## 4337  -5.220915373 -1.667516325  1779.5  43.0 chic
## 4338 -15.932083650 -0.152713427  1780.5  48.0 chic
## 4339 -14.545240790  0.817258778  1781.5  42.0 chic
## 4340 -15.500117310  1.801874137  1782.5  48.0 chic
## 4341 -13.490996970 -1.991476031  1783.5  45.0 chic
## 4342 -14.603244170 -3.520179735  1784.5  31.0 chic
## 4343 -13.233837780  1.903767252  1785.5  34.0 chic
## 4344  -8.090668131  1.885966594  1786.5  49.0 chic
## 4345  -8.548009599  2.018568252  1787.5  50.0 chic
## 4346 -16.159736760  2.959132549  1788.5  46.0 chic
## 4347 -17.687540010  2.508292487  1789.5  50.0 chic
## 4348 -12.351026440 -1.378822089  1790.5  50.0 chic
## 4349 -17.162113540  2.518011603  1791.5  48.0 chic
## 4350 -11.639168340  4.466383391  1792.5  54.0 chic
## 4351  -9.611328633 -2.222339557  1793.5  63.0 chic
## 4352  -9.576530099 -2.673287781  1794.5  52.0 chic
## 4353 -16.755949160  4.378568483  1795.5  45.0 chic
## 4354 -14.010743680  7.007762188  1796.5  55.0 chic
## 4355 -13.363180040  5.258446003  1797.5  61.0 chic
## 4356 -18.867554740 -1.749831359  1798.5  61.0 chic
## 4357 -15.050018110 -2.005549355  1799.5  59.0 chic
## 4358 -13.321423790 -2.041560436  1800.5  50.0 chic
## 4359  -3.335274524 -3.200700595  1801.5  39.0 chic
## 4360 -16.512815730 -2.517424625  1802.5  38.0 chic
## 4361 -17.008070480  2.044497816  1803.5  39.0 chic
## 4362 -16.952610090 -1.279629310  1804.5  35.0 chic
## 4363 -16.359203490  1.863137341  1805.5  39.0 chic
## 4364 -16.134240960 10.299935368  1806.5  38.0 chic
## 4365  -9.258035783 -0.397417890  1807.5  38.0 chic
## 4366 -14.232693970  1.336294958  1808.5  36.0 chic
## 4367 -16.461434600  3.126692003  1809.5  44.0 chic
## 4368 -16.117185100 -1.834875803  1810.5  37.0 chic
## 4369 -12.816847760 -3.044503328  1811.5  32.0 chic
## 4370 -14.944888730  0.417174359  1812.5  43.0 chic
## 4371 -13.135642160 -3.127026476  1813.5  35.0 chic
## 4372 -17.151751070 -2.198682360  1814.5  31.0 chic
## 4373 -15.456305680 -3.898994192  1815.5  23.0 chic
## 4374  -7.008001697 -3.299786238  1816.5  10.0 chic
## 4375 -12.547057680  2.698474812  1817.5  17.0 chic
## 4376 -11.521849220 -0.868580790  1818.5  19.0 chic
## 4377  -7.795543884  2.243391641  1819.5  25.0 chic
## 4378  -9.947142058 -0.243992288  1820.5  26.0 chic
## 4379 -11.958215120  1.091764868  1821.5  36.0 chic
## 4380 -16.367046580 -1.369734859  1822.5  29.0 chic
## 4381  -9.666223089 -2.458327468  1823.5  22.0 chic
## 4382 -12.939151190  0.707992939  1824.5   7.0 chic
## 4383 -12.496228820 -3.385704080  1825.5  13.0 chic
## 4384  -2.244121522 -3.866673094  1826.5  14.0 chic
## 4385   7.126712570 -4.213597599  1827.5  22.0 chic
## 4386  -0.772970924 -4.171005006  1828.5  16.0 chic
## 4387 -10.329249460 -4.015310722  1829.5  -2.0 chic
## 4388 -12.032902430  4.320634749  1830.5   1.0 chic
## 4389 -13.169203800 -0.645504530  1831.5  12.0 chic
## 4390 -14.826167270  0.562920299  1832.5   1.0 chic
## 4391  -4.741993255  0.207433098  1833.5  16.0 chic
## 4392  -9.531692431  0.238889662  1834.5   4.0 chic
## 4393  -1.957632610  3.011834868  1835.5   6.0 chic
## 4394 -11.212094590  1.160848249  1836.5   5.0 chic
## 4395 -11.812147360  1.808771827  1837.5  20.0 chic
## 4396   8.134710460 -3.688829080  1838.5  20.0 chic
## 4397  -4.399030587 -1.359694282  1839.5  18.0 chic
## 4398 -15.197244480  4.379247727  1840.5  23.0 chic
## 4399 -12.915737630  7.643100021  1841.5  30.0 chic
## 4400 -13.916959790  0.507200695  1842.5  28.0 chic
## 4401  -1.502495623 -1.943414548  1843.5  29.0 chic
## 4402 -12.336144770  0.689895701  1844.5  27.0 chic
## 4403 -14.616026120  4.420162505  1845.5  30.0 chic
## 4404 -14.508109080  3.552384097  1846.5  37.0 chic
## 4405 -17.006461280 -2.479832804  1847.5  42.0 chic
## 4406 -13.427459140 -2.883759753  1848.5  39.0 chic
## 4407  -5.340752522 -1.968670654  1849.5  33.0 chic
## 4408 -11.859360110 -2.919129697  1850.5  28.0 chic
## 4409 -13.713183370  0.577727087  1851.5  29.0 chic
## 4410 -11.002396360  0.236782246  1852.5  42.0 chic
## 4411 -11.028652070 -2.530499434  1853.5  33.0 chic
## 4412  -9.035513660  1.514005460  1854.5  33.0 chic
## 4413  -5.318512933 -1.860014251  1855.5  34.0 chic
## 4414   0.532172830 -1.078330917  1856.5  36.0 chic
## 4415 -14.704699320 -3.626626678  1857.5  40.0 chic
## 4416 -13.862804340 -2.032047171  1858.5  38.0 chic
## 4417  -6.736690202  0.142086676  1859.5  41.0 chic
## 4418  -5.232999441 -3.940345916  1860.5  31.0 chic
## 4419 -15.344208590 -0.205348775  1861.5  31.0 chic
## 4420 -10.800689360  0.166112412  1862.5  40.0 chic
## 4421  -6.686287028 -3.052357683  1863.5  33.0 chic
## 4422 -13.596997480 -2.241342422  1864.5  37.0 chic
## 4423 -10.866617260 -1.119202378  1865.5  44.0 chic
## 4424 -10.197482720 -0.019372483  1866.5  44.0 chic
## 4425  -7.441012965 -2.547347886  1867.5  49.0 chic
## 4426   1.033987035 -4.331979341  1868.5  23.0 chic
## 4427  -5.003366923 -3.883237157  1869.5  23.0 chic
## 4428  -5.895236709 -3.122732766  1870.5  34.0 chic
## 4429  -6.727086024 -0.519727327  1871.5  45.0 chic
## 4430 -10.911219490 -2.480138223  1872.5  39.0 chic
## 4431  -7.821483296 -3.723924303  1873.5  27.0 chic
## 4432  -8.705882252  0.362858923  1874.5  29.0 chic
## 4433   5.217234486 -2.910101547  1875.5  28.0 chic
## 4434   2.255880171 -3.910132146  1876.5  25.0 chic
## 4435  13.940632918 -4.745158936  1877.5  28.0 chic
## 4436   5.858975400 -3.316544693  1878.5  26.0 chic
## 4437  -6.001368058 -0.644840305  1879.5  29.0 chic
## 4438 -15.485842940  2.131344134  1880.5  27.0 chic
## 4439 -12.161071250  1.278579543  1881.5  33.0 chic
## 4440 -13.736666850  2.058622276  1882.5  36.0 chic
## 4441 -16.068421860 -1.247199642  1883.5  43.0 chic
## 4442  -6.572188983 -4.158958814  1884.5  37.0 chic
## 4443   1.712403991 -0.567440265  1885.5  38.0 chic
## 4444 -16.985741610 -1.759719380  1886.5  37.0 chic
## 4445  -0.943536015 -3.115170457  1887.5  31.0 chic
## 4446  -5.094811576  2.275534243  1888.5  30.0 chic
## 4447  -7.250934754 -0.275986618  1889.5  35.0 chic
## 4448  14.274660303 -5.391938145  1890.5  25.0 chic
## 4449  14.595708575 -3.885317141  1891.5  21.0 chic
## 4450   4.550388940 -2.850782695  1892.5  24.0 chic
## 4451   5.318425470 -3.525113639  1893.5  29.0 chic
## 4452  15.390686940 -3.207143442  1894.5  29.0 chic
## 4453   0.629556940 -3.563272622  1895.5  28.0 chic
## 4454   8.615379112 -2.724737342  1896.5  27.0 chic
## 4455  12.031145065  1.157014502  1897.5  28.0 chic
## 4456  18.142964665 -4.070582798  1898.5  32.0 chic
## 4457  -7.223874353  6.755590058  1899.5  31.0 chic
## 4458  -8.690266327  5.789747294  1900.5  43.0 chic
## 4459   7.824396490 -0.147264745  1901.5  50.0 chic
## 4460   5.249664104 -3.479815092  1902.5  38.0 chic
## 4461  -0.544093797 -3.361180833  1903.5  36.0 chic
## 4462  -3.611483148 -0.452320147  1904.5  39.0 chic
## 4463   9.481320598 -3.641273425  1905.5  38.0 chic
## 4464   1.262288312  1.947933414  1906.5  36.0 chic
## 4465  -8.285444013  4.516400998  1907.5  38.0 chic
## 4466   9.722633435 -2.221755885  1908.5  35.0 chic
## 4467   7.226248318 -4.109323340  1909.5  32.0 chic
## 4468   7.610142891 -3.302287879  1910.5  35.0 chic
## 4469   0.315047792  0.831743797  1911.5  41.0 chic
## 4470  -3.992241825  0.319655607  1912.5  43.0 chic
## 4471  -5.317945175 -2.087697487  1913.5  48.0 chic
## 4472   1.916311448  2.666708116  1914.5  55.0 chic
## 4473   8.201590441 -0.426470785  1915.5  60.0 chic
## 4474  -8.013949366 -2.795939784  1916.5  58.0 chic
## 4475  -9.849654601  0.583440868  1917.5  64.0 chic
## 4476  -8.319291577 -3.160796773  1918.5  63.0 chic
## 4477  -1.181179380 -1.812851568  1919.5  56.0 chic
## 4478  -3.786625693 -1.495075919  1920.5  50.0 chic
## 4479   0.563058169 -3.951071551  1921.5  53.0 chic
## 4480   6.001454595 -0.174449588  1922.5  55.0 chic
## 4481   7.886577936 -0.419862455  1923.5  55.0 chic
## 4482   7.732660637 -3.509552620  1924.5  45.0 chic
## 4483   9.328586021 -0.588693151  1925.5  47.0 chic
## 4484   0.125819739 -3.273168037  1926.5  46.0 chic
## 4485   1.252469500  0.797694452  1927.5  45.0 chic
## 4486  -3.679416145  1.386598885  1928.5  50.0 chic
## 4487   4.360224920  1.172672741  1929.5  49.0 chic
## 4488  11.085443690 -0.232297666  1930.5  49.0 chic
## 4489   1.242984371 -3.274420954  1931.5  44.0 chic
## 4490  -5.244564135 -2.643028260  1932.5  44.0 chic
## 4491 -13.137361610 -0.473051091  1933.5  42.0 chic
## 4492 -12.619336780  3.401085300  1934.5  42.0 chic
## 4493  -1.844272394 -1.556938618  1935.5  45.0 chic
## 4494   2.116664666 -1.876764480  1936.5  54.0 chic
## 4495 -10.684735070 -2.620314222  1937.5  48.0 chic
## 4496   4.650642258 -3.275541383  1938.5  43.0 chic
## 4497   8.662568508 -1.633825084  1939.5  44.0 chic
## 4498   8.204752714  1.806425780  1940.5  48.0 chic
## 4499  14.659255054  1.589368820  1941.5  51.0 chic
## 4500   2.976666079 -2.242483834  1942.5  50.0 chic
## 4501   5.980228764 -2.732509087  1943.5  51.0 chic
## 4502  10.392772252 -2.346558951  1944.5  52.0 chic
## 4503   7.720082405  0.105952464  1945.5  51.0 chic
## 4504   7.423030072 -1.440052102  1946.5  56.0 chic
## 4505  19.473871162  1.717143691  1947.5  58.0 chic
## 4506  16.989773813  2.981611283  1948.5  64.0 chic
## 4507  18.383982192  2.436071307  1949.5  67.0 chic
## 4508  11.959686162 -1.960127593  1950.5  70.0 chic
## 4509  -4.089270388 -3.407237348  1951.5  58.0 chic
## 4510  -4.077614616 -1.421317752  1952.5  56.0 chic
## 4511 -11.140965940 -4.830644293  1953.5  53.0 chic
## 4512  -5.607295410 -3.962317298  1954.5  51.0 chic
## 4513   8.401885560 -0.298046009  1955.5  58.0 chic
## 4514   9.792340880  1.162396061  1956.5  67.0 chic
## 4515   3.883607371 -1.089995810  1957.5  62.0 chic
## 4516   6.033988512 -2.902047609  1958.5  51.0 chic
## 4517   3.250472475 -2.752924802  1959.5  53.0 chic
## 4518  -2.689688290 -0.253198641  1960.5  60.0 chic
## 4519  14.508607371 -1.941752134  1961.5  69.0 chic
## 4520   5.773496136 -1.673270938  1962.5  69.0 chic
## 4521  -1.708702646 -3.762517504  1963.5  61.0 chic
## 4522  -2.982295410  4.533917794  1964.5  61.0 chic
## 4523  13.400965808 -0.674758005  1965.5  64.0 chic
## 4524  22.146280365 -0.898081477  1966.5  66.0 chic
## 4525  11.089146517 -3.860234255  1967.5  59.0 chic
## 4526  -6.442203440 -2.425627774  1968.5  60.0 chic
## 4527  -5.505823948 -3.912072241  1969.5  51.0 chic
## 4528   4.075655179 -2.269720643  1970.5  56.0 chic
## 4529   0.171657688 -1.832194098  1971.5  60.0 chic
## 4530   1.140374411  3.201429212  1972.5  64.0 chic
## 4531   5.179684147  1.773679612  1973.5  75.0 chic
## 4532  23.831607480  3.220479400  1974.5  76.0 chic
## 4533  26.295977945 -0.399005573  1975.5  70.0 chic
## 4534   4.542139142 -2.014898386  1976.5  73.0 chic
## 4535  -6.289072906 -2.890840590  1977.5  65.0 chic
## 4536  -9.410853483 -3.422751966  1978.5  64.0 chic
## 4537  -1.400536773 -3.602162568  1979.5  58.0 chic
## 4538   8.639267266 -1.991586729  1980.5  64.0 chic
## 4539   6.431478423 -3.615892285  1981.5  75.0 chic
## 4540   8.750472475 -2.466291457  1982.5  80.0 chic
## 4541  13.274818823 -0.843022759  1983.5  81.0 chic
## 4542  14.892704590  3.552706033  1984.5  80.0 chic
## 4543   2.295669956  0.342203750  1985.5  79.0 chic
## 4544   3.741132696 -0.562592922  1986.5  79.0 chic
## 4545   7.562947031 -1.772481207  1987.5  78.0 chic
## 4546  17.016129893 -2.033310406  1988.5  75.0 chic
## 4547   3.854613698 -3.770166392  1989.5  65.0 chic
## 4548   2.239358436 -3.150721948  1990.5  66.0 chic
## 4549   3.036445963 -3.306971948  1991.5  58.0 chic
## 4550   2.000472475 -3.318083059  1992.5  61.0 chic
## 4551   3.057796560 -3.298490115  1993.5  57.0 chic
## 4552   3.334792895 -0.592325732  1994.5  59.0 chic
## 4553  13.880813184 -0.485744065  1995.5  68.0 chic
## 4554  30.083805808 -0.879741308  1996.5  67.0 chic
## 4555  25.373765196  1.052225730  1997.5  72.0 chic
## 4556   8.187947031 -1.781026267  1998.5  74.0 chic
## 4557  21.165219759 -1.941236168  1999.5  76.0 chic
## 4558   3.486864733 -2.670263917  2000.5  76.0 chic
## 4559   7.684371257  1.522619048  2001.5  75.0 chic
## 4560  10.825655179 -1.016612934  2002.5  76.0 chic
## 4561  15.887465482  1.099825422  2003.5  78.0 chic
## 4562   4.392704590 -2.577466133  2004.5  77.0 chic
## 4563  -0.168689867  0.508766556  2005.5  66.0 chic
## 4564   5.157608308 -1.749800051  2006.5  68.0 chic
## 4565   2.547479851 -2.445987099  2007.5  74.0 chic
## 4566   4.577786007 -1.722202975  2008.5  77.0 chic
## 4567   5.514872025 -2.445952252  2009.5  84.0 chic
## 4568   5.722600600  0.664960932  2010.5  86.0 chic
## 4569  10.187947031  1.812166610  2011.5  87.0 chic
## 4570   3.437947031  4.019621444  2012.5  79.0 chic
## 4571   3.658954595  0.276297672  2013.5  75.0 chic
## 4572  15.053726321  2.927332029  2014.5  76.0 chic
## 4573  10.000472475  3.365973532  2015.5  76.0 chic
## 4574  -0.541194192 -3.584228701  2016.5  64.0 chic
## 4575  -2.145386302  0.175391389  2017.5  67.0 chic
## 4576   0.841940705  2.946752041  2018.5  68.0 chic
## 4577   8.042139142  1.745402031  2019.5  70.0 chic
## 4578  22.104613698  3.236283524  2020.5  74.0 chic
## 4579  26.833805808  0.111487544  2021.5  78.0 chic
## 4580  21.576463864 -1.392616933  2022.5  82.0 chic
## 4581   1.726037923 -2.681658579  2023.5  75.0 chic
## 4582   7.792139142  0.760727628  2024.5  77.0 chic
## 4583   2.125472475  0.816663702  2025.5  79.0 chic
## 4584   4.057796560 -2.055025796  2026.5  76.0 chic
## 4585   5.464146517 -1.774212549  2027.5  83.0 chic
## 4586   4.564326935  1.661883703  2028.5  82.0 chic
## 4587   0.341940705 -1.450309553  2029.5  83.0 chic
## 4588   8.214146517 -0.002250193  2030.5  82.0 chic
## 4589  13.920583592  6.997442480  2031.5  85.0 chic
## 4590  -1.707860858  2.492533804  2032.5  80.0 chic
## 4591   1.687947031  0.995341118  2033.5  79.0 chic
## 4592   8.494783655  1.974810467  2034.5  82.0 chic
## 4593   4.613877074 -0.947470562  2035.5  83.0 chic
## 4594   9.151877247 -1.387769226  2036.5  90.0 chic
## 4595  11.819750025 -0.562629896  2037.5  83.0 chic
## 4596   1.492713441 -1.616044101  2038.5  74.0 chic
## 4597  -5.278296757 -1.507826508  2039.5  71.0 chic
## 4598  -1.528882021  2.783141940  2040.5  71.0 chic
## 4599  -1.235732734  0.538333004  2041.5  76.0 chic
## 4600  -2.398170284 -0.945645212  2042.5  73.0 chic
## 4601   4.101037923  0.552429038  2043.5  76.0 chic
## 4602  -3.541194192 -4.045908634  2044.5  72.0 chic
## 4603   0.805933933 -5.803164569  2045.5  66.0 chic
## 4604  -1.983870107 -2.077253583  2046.5  66.0 chic
## 4605   6.589146517 -0.938447031  2047.5  78.0 chic
## 4606  -6.666194192 -2.462419634  2048.5  75.0 chic
## 4607 -10.103719640 -4.144378141  2049.5  70.0 chic
## 4608  -2.341011488 -3.637108385  2050.5  72.0 chic
## 4609  -2.164681513 -5.107085554  2051.5  64.0 chic
## 4610  -5.869186816  0.072970136  2052.5  66.0 chic
## 4611   4.175292860  3.067993284  2053.5  73.0 chic
## 4612   3.363527848 -1.062056045  2054.5  75.0 chic
## 4613 -17.645386300 -0.596363450  2055.5  67.0 chic
## 4614  -0.530573381 -2.711973046  2056.5  68.0 chic
## 4615   0.050274038 -2.410236935  2057.5  67.0 chic
## 4616  -5.562052969 -0.877370817  2058.5  65.0 chic
## 4617  -1.228719635 -3.369071679  2059.5  67.0 chic
## 4618  -2.182987451 -2.887896108  2060.5  72.0 chic
## 4619   5.125472475 -3.771601687  2061.5  71.0 chic
## 4620   9.479640686 -3.369709410  2062.5  70.0 chic
## 4621   1.125472475 -2.772652630  2063.5  72.0 chic
## 4622  -0.276447029 -0.248268529  2064.5  74.0 chic
## 4623   7.492713441 -1.404948970  2065.5  80.0 chic
## 4624   7.112801613 -3.621550333  2066.5  69.0 chic
## 4625   0.220555727 -1.540346401  2067.5  62.0 chic
## 4626  -2.108870107  1.679196638  2068.5  65.0 chic
## 4627  14.258607371  3.497492020  2069.5  70.0 chic
## 4628  17.097600600  4.813147626  2070.5  74.0 chic
## 4629  25.365716542  5.588371258  2071.5  76.0 chic
## 4630  32.391641584  5.572181055  2072.5  79.0 chic
## 4631  31.599463227  8.845147271  2073.5  77.0 chic
## 4632   2.187947031 -2.068511800  2074.5  66.0 chic
## 4633 -10.784594740  0.712365959  2075.5  65.0 chic
## 4634  -4.416194192 -0.550930646  2076.5  71.0 chic
## 4635 -11.025539940 -1.714230032  2077.5  62.0 chic
## 4636  -9.482295410  0.364001308  2078.5  62.0 chic
## 4637  -3.562052969  3.449694727  2079.5  64.0 chic
## 4638   0.755813184  1.648708021  2080.5  68.0 chic
## 4639 -16.175899090 -3.100305273  2081.5  59.0 chic
## 4640 -13.818224380 -0.258112500  2082.5  59.0 chic
## 4641 -13.192203440 -0.048935410  2083.5  55.0 chic
## 4642  -9.349812136 -2.639238506  2084.5  59.0 chic
## 4643  -9.760240390 -2.675854667  2085.5  59.0 chic
## 4644  -4.108870107 -0.185296340  2086.5  62.0 chic
## 4645   5.722600600  1.853321638  2087.5  64.0 chic
## 4646 -10.900536770 -3.013758447  2088.5  58.0 chic
## 4647  -5.641903202 -4.269237899  2089.5  51.0 chic
## 4648  -9.284504915 -1.063720997  2090.5  57.0 chic
## 4649  -1.365469554  1.092567468  2091.5  66.0 chic
## 4650  -2.944870534 -4.140376366  2092.5  57.0 chic
## 4651   4.661031913  0.696993981  2093.5  62.0 chic
## 4652  20.767773816 -3.174158758  2094.5  72.0 chic
## 4653  16.150472475 -4.606140708  2095.5  66.0 chic
## 4654  -5.999527525 -5.539730346  2096.5  58.0 chic
## 4655 -10.706812330 -2.013233581  2097.5  56.0 chic
## 4656  -9.333623369  0.931205827  2098.5  56.0 chic
## 4657 -15.292144550  0.965087299  2099.5  51.0 chic
## 4658 -13.572184710 -3.587228580  2100.5  46.0 chic
## 4659  -9.218489125 -5.705449937  2101.5  44.0 chic
## 4660  -5.588230974 -4.279393637  2102.5  47.0 chic
## 4661 -13.615460830  1.223316310  2103.5  51.0 chic
## 4662  -5.163428400 -2.121236688  2104.5  52.0 chic
## 4663  -9.706907566  4.514631606  2105.5  59.0 chic
## 4664  -8.798478496 -0.912590805  2106.5  61.0 chic
## 4665 -18.530186660 -2.138458169  2107.5  59.0 chic
## 4666  -0.005306670 -1.502642288  2108.5  66.0 chic
## 4667  -8.967054971 -1.173911891  2109.5  55.0 chic
## 4668  -3.434007928  1.364895504  2110.5  62.0 chic
## 4669  -8.663936419 -0.992184135  2111.5  56.0 chic
## 4670 -12.175773950 -2.005220866  2112.5  48.0 chic
## 4671  -0.021041198  1.906594633  2113.5  64.0 chic
## 4672  -1.273929264 -2.405965819  2114.5  58.0 chic
## 4673  -8.391609112 -2.677217213  2115.5  45.0 chic
## 4674 -18.560660450 -1.178245053  2116.5  44.0 chic
## 4675 -13.959948120  2.137263440  2117.5  50.0 chic
## 4676 -15.571331100 -0.028000660  2118.5  44.0 chic
## 4677 -12.777503690  4.247000008  2119.5  52.0 chic
## 4678  -4.731222211 -2.958878987  2120.5  51.0 chic
## 4679  -3.212451137 -3.289372088  2121.5  43.0 chic
## 4680  -8.850335460  2.000354633  2122.5  40.0 chic
## 4681 -10.863531910  3.887738008  2123.5  50.0 chic
## 4682 -11.933788240  0.481468258  2124.5  53.0 chic
## 4683 -13.265346070  0.021478509  2125.5  48.0 chic
## 4684  -9.583441077  6.126122814  2126.5  64.0 chic
## 4685   0.665887236  0.846864742  2127.5  62.0 chic
## 4686  14.103904537 -0.002831109  2128.5  66.0 chic
## 4687  -7.645992630  3.906750492  2129.5  58.0 chic
## 4688  -7.319418350  4.750196000  2130.5  59.0 chic
## 4689  -3.795366865 -4.847562406  2131.5  42.0 chic
## 4690  -6.941127519 -2.214610325  2132.5  37.0 chic
## 4691  -8.974854044  4.613972238  2133.5  46.0 chic
## 4692  -7.053898199  2.400762300  2134.5  55.0 chic
## 4693  -7.462413751 -2.029824658  2135.5  45.0 chic
## 4694  -2.124616132  0.176901696  2136.5  44.0 chic
## 4695 -12.223321160  7.323399783  2137.5  55.0 chic
## 4696   0.138609936  2.906797639  2138.5  67.0 chic
## 4697   0.349699303 -0.295974159  2139.5  58.0 chic
## 4698  -0.582886423 -3.135799950  2140.5  44.0 chic
## 4699 -15.857384100  5.701804901  2141.5  47.0 chic
## 4700  -7.155630381  5.049960921  2142.5  54.0 chic
## 4701  -0.106801806 -2.944998752  2143.5  45.0 chic
## 4702 -12.427981910 -0.240090076  2144.5  41.0 chic
## 4703  -5.069534420 -4.292796359  2145.5  38.0 chic
## 4704 -10.189452030 -0.348337111  2146.5  40.0 chic
## 4705  -4.941847308  1.885796180  2147.5  58.0 chic
## 4706  -6.128741289 -2.524364508  2148.5  49.0 chic
## 4707 -17.342032810 -2.234268159  2149.5  39.0 chic
## 4708 -16.916250750 -3.079625576  2150.5  42.0 chic
## 4709 -12.874733250  1.258842647  2151.5  54.0 chic
## 4710 -11.028278370 -2.322238487  2152.5  52.0 chic
## 4711 -12.060641850 -3.654359880  2153.5  40.0 chic
## 4712 -12.451089310  0.171785798  2154.5  36.0 chic
## 4713 -10.733108410  1.385370273  2155.5  37.0 chic
## 4714 -12.217737070 -3.672375654  2156.5  41.0 chic
## 4715 -11.527260880 -1.910720270  2157.5  34.0 chic
## 4716 -10.924696770 -1.447417100  2158.5  32.0 chic
## 4717 -13.429116270  2.241415474  2159.5  30.0 chic
## 4718 -13.063204100  5.798130845  2160.5  36.0 chic
## 4719 -11.896537430  2.628897715  2161.5  50.0 chic
## 4720  -8.814674815 -3.193223587  2162.5  53.0 chic
## 4721 -15.392468590 -3.444196388  2163.5  49.0 chic
## 4722  -7.810791391 -5.535132165  2164.5  39.0 chic
## 4723 -11.635846660 -3.597101863  2165.5  29.0 chic
## 4724 -13.727248390 -2.925522989  2166.5  33.0 chic
## 4725 -14.946374930  1.925991430  2167.5  38.0 chic
## 4726 -13.641167800  2.202342352  2168.5  43.0 chic
## 4727 -11.373081720 -3.279502537  2169.5  31.0 chic
## 4728 -13.824412550 -1.724793670  2170.5  31.0 chic
## 4729 -16.815278190  1.150648964  2171.5  35.0 chic
## 4730 -10.178579210 -0.911793958  2172.5  35.0 chic
## 4731  -7.398085704 -3.951282651  2173.5  38.0 chic
## 4732 -15.949671540 -4.101217226  2174.5  36.0 chic
## 4733 -14.309945510 -5.254141496  2175.5  27.0 chic
## 4734  -8.754967346 -2.074836975  2176.5  23.0 chic
## 4735 -11.044996720  1.423222385  2177.5  19.0 chic
## 4736 -14.941372940  2.350619646  2178.5  32.0 chic
## 4737  -6.090618262 -2.233191526  2179.5  21.0 chic
## 4738 -11.054915910 -4.378272472  2180.5   8.0 chic
## 4739 -14.273066590 -0.866106580  2181.5  13.0 chic
## 4740 -12.059710430  0.056108152  2182.5  10.0 chic
## 4741  -9.890621385 -0.478762805  2183.5  14.0 chic
## 4742 -11.542735440  4.346789429  2184.5  19.0 chic
## 4743   3.226967970 -0.714475791  2185.5  28.0 chic
## 4744  -6.306412773 -4.574775621  2186.5  19.0 chic
## 4745  -9.526839389  0.985735180  2187.5  21.0 chic
## 4746 -14.236885050  0.374136133  2188.5  36.0 chic
## 4747  -8.383720694 -2.426327998  2189.5  34.0 chic
## 4748 -12.611954540 -0.048923228  2190.5  34.0 chic
## 4749 -13.500580520  7.389041547  2191.5  42.0 chic
## 4750  -2.294333025 -1.050088952  2192.5  48.0 chic
## 4751  -7.504970023 -4.434609539  2193.5  35.0 chic
## 4752  -6.021543578 -4.838050117  2194.5  28.0 chic
## 4753 -14.061388890 -2.013356809  2195.5  20.0 chic
## 4754 -14.250955930  4.546386094  2196.5  34.0 chic
## 4755  -9.816863213  0.852685323  2197.5  25.0 chic
## 4756 -11.916073150  1.624801405  2198.5  37.0 chic
## 4757 -16.220402030 -3.582784537  2199.5  39.0 chic
## 4758 -10.565306550 -1.502595856  2200.5  42.0 chic
## 4759  -6.654233491 -2.371525731  2201.5  33.0 chic
## 4760  -3.875352212 -2.655679208  2202.5  31.0 chic
## 4761  -9.256052088 -4.007069826  2203.5  25.0 chic
## 4762 -14.531985320  1.392344121  2204.5  20.0 chic
## 4763 -14.497920210  1.759368530  2205.5  34.0 chic
## 4764   2.027811206 -1.239052349  2206.5  31.0 chic
## 4765   2.117300611 -3.942800420  2207.5  22.0 chic
## 4766 -14.209023050 -3.463289118  2208.5  25.0 chic
## 4767 -14.442331010  0.787207135  2209.5  21.0 chic
## 4768  -5.594012161 -3.575687510  2210.5  10.0 chic
## 4769 -10.613787790  1.494982760  2211.5   5.0 chic
## 4770 -13.171302180  3.059625468  2212.5  17.0 chic
## 4771  -4.038539626 -0.664127985  2213.5  16.0 chic
## 4772 -11.825966250  4.406937107  2214.5   6.0 chic
## 4773  -8.749694438 -0.252980541  2215.5  17.0 chic
## 4774  -9.422509546 -1.749099263  2216.5  15.0 chic
## 4775  -6.455682181  1.807694428  2217.5  20.0 chic
## 4776 -12.171707010  5.439143713  2218.5  21.0 chic
## 4777   3.936012649  3.242936830  2219.5  24.0 chic
## 4778  -9.565788472  2.477998728  2220.5  28.0 chic
## 4779  -7.568607382  2.106038690  2221.5  20.0 chic
## 4780  -9.135935804 -0.103841953  2222.5  25.0 chic
## 4781 -10.241811760  3.779003326  2223.5  21.0 chic
## 4782  -8.981205799  2.278331852  2224.5  33.0 chic
## 4783  -6.717823961  0.049908598  2225.5  25.0 chic
## 4784  -7.372171853 -1.701084565  2226.5  21.0 chic
## 4785 -10.453773720  0.465214813  2227.5  23.0 chic
## 4786  -3.992923226 -1.361497516  2228.5  26.0 chic
## 4787  -6.465596497  2.008622395  2229.5  22.0 chic
## 4788  -6.811183716  5.094774338  2230.5  41.0 chic
## 4789  -9.431673745  0.893354381  2231.5  35.0 chic
## 4790   0.965663022 -0.888819811  2232.5  24.0 chic
## 4791   5.311922133 -0.368778103  2233.5  24.0 chic
## 4792  -4.933448340  0.136522654  2234.5  30.0 chic
## 4793  -3.890229882 -0.056646466  2235.5  26.0 chic
## 4794 -11.920564130  4.614811224  2236.5  31.0 chic
## 4795  -1.806314251 -0.294241425  2237.5  28.0 chic
## 4796  -2.211719136  3.137286045  2238.5  27.0 chic
## 4797   5.577065480 -2.696047289  2239.5  30.0 chic
## 4798   2.456334691 -2.719382285  2240.5  29.0 chic
## 4799   3.007429200 -0.946882517  2241.5  27.0 chic
## 4800  -7.366742952  2.305982225  2242.5  35.0 chic
## 4801 -10.177779520  0.872659801  2243.5  49.0 chic
## 4802   5.626398791  0.136366209  2244.5  59.0 chic
## 4803  -3.382903882 -1.596606551  2245.5  49.0 chic
## 4804  -3.988200924 -0.389764251  2246.5  55.0 chic
## 4805   3.235972630 -3.229837903  2247.5  56.0 chic
## 4806   2.336608949 -3.137572676  2248.5  45.0 chic
## 4807  -8.855345041  2.453594115  2249.5  43.0 chic
## 4808  -1.682923310  6.589721730  2250.5  54.0 chic
## 4809   4.844213664 -2.381383477  2251.5  46.0 chic
## 4810   6.225262202 -3.493659968  2252.5  33.0 chic
## 4811   3.845239371 -0.075787678  2253.5  35.0 chic
## 4812  -5.561270993  3.001097828  2254.5  46.0 chic
## 4813  -6.780323484 -0.146223600  2255.5  52.0 chic
## 4814  -4.462653054  1.300808304  2256.5  51.0 chic
## 4815   1.413393449  6.161177388  2257.5  66.0 chic
## 4816   8.034540058  2.367549408  2258.5  67.0 chic
## 4817  -0.750910484 -2.493765551  2259.5  46.0 chic
## 4818   1.128650763 -3.066386790  2260.5  30.0 chic
## 4819   7.103998192 -3.118745697  2261.5  30.0 chic
## 4820  -3.962672385  1.142641972  2262.5  34.0 chic
## 4821 -12.847860110 -0.649802178  2263.5  39.0 chic
## 4822  -4.831731678 -0.046479107  2264.5  45.0 chic
## 4823  -2.515314801  3.416218891  2265.5  50.0 chic
## 4824  16.667904377 -3.607191201  2266.5  33.0 chic
## 4825  14.594028598 -3.547209842  2267.5  32.0 chic
## 4826   2.062441088  2.355235543  2268.5  34.0 chic
## 4827  -4.918743832 -3.042530399  2269.5  39.0 chic
## 4828  -6.214007117 -3.974865520  2270.5  49.0 chic
## 4829 -16.879856200 -0.732624295  2271.5  47.0 chic
## 4830 -11.436182650  4.758662402  2272.5  51.0 chic
## 4831  -7.016478649  5.157731050  2273.5  50.0 chic
## 4832  -2.511050062  6.225140788  2274.5  57.0 chic
## 4833  11.205947259 -1.865391661  2275.5  53.0 chic
## 4834   9.295959550 -0.507803351  2276.5  52.0 chic
## 4835   7.737843974 -1.997504707  2277.5  47.0 chic
## 4836  -6.642821828 -4.408439238  2278.5  39.0 chic
## 4837  -2.176366768 -1.232056246  2279.5  40.0 chic
## 4838   2.609010592 -0.800214130  2280.5  40.0 chic
## 4839  -8.542813013 10.703578907  2281.5  45.0 chic
## 4840  -6.544157125  4.523032415  2282.5  52.0 chic
## 4841  -2.066653213  2.476255891  2283.5  51.0 chic
## 4842  -4.174482179  1.900267308  2284.5  52.0 chic
## 4843   1.096055582 -2.400309399  2285.5  38.0 chic
## 4844  -0.965548425  2.583823021  2286.5  44.0 chic
## 4845   3.044856122 -1.911471487  2287.5  53.0 chic
## 4846  -7.862158218 -2.680244380  2288.5  41.0 chic
## 4847   5.134768979 -4.077781108  2289.5  34.0 chic
## 4848   1.003418877 -0.120192201  2290.5  45.0 chic
## 4849   6.650902791 -4.032233163  2291.5  37.0 chic
## 4850  -0.817336551 -4.328768799  2292.5  34.0 chic
## 4851  -3.207684033 -0.783950455  2293.5  39.0 chic
## 4852  -2.610389154  1.059562549  2294.5  49.0 chic
## 4853   1.782189418  2.614337216  2295.5  58.0 chic
## 4854   1.201579544  1.048230961  2296.5  56.0 chic
## 4855  -4.133925985 -1.878212848  2297.5  44.0 chic
## 4856  -7.878419065 -4.855525797  2298.5  41.0 chic
## 4857 -13.521415940  0.177705484  2299.5  48.0 chic
## 4858 -12.883507900 -3.854167530  2300.5  52.0 chic
## 4859  -9.666769508 -2.104052746  2301.5  53.0 chic
## 4860   5.690124608 -4.346706557  2302.5  44.0 chic
## 4861  -1.822278091  2.885098582  2303.5  48.0 chic
## 4862   8.532229760 -1.542509843  2304.5  47.0 chic
## 4863  13.233591988 -1.962774258  2305.5  52.0 chic
## 4864  12.928768728 -1.409594237  2306.5  49.0 chic
## 4865   3.505789707  1.780581659  2307.5  52.0 chic
## 4866   1.486833275  7.305366657  2308.5  53.0 chic
## 4867   6.999949742  4.428744241  2309.5  54.0 chic
## 4868   1.205358838 -3.237877065  2310.5  48.0 chic
## 4869  -0.243938083  7.083850094  2311.5  54.0 chic
## 4870  -1.393207175  1.753818499  2312.5  57.0 chic
## 4871   5.580643510  3.537714226  2313.5  57.0 chic
## 4872   8.840747244 -0.449299591  2314.5  63.0 chic
## 4873   8.564068577  1.155842742  2315.5  72.0 chic
## 4874  11.911381484  3.590874881  2316.5  74.0 chic
## 4875   9.882472905 -1.619099830  2317.5  74.0 chic
## 4876   3.575188322 -1.874311781  2318.5  71.0 chic
## 4877   1.389634562 -1.615031752  2319.5  76.0 chic
## 4878   0.414817991 -2.674566675  2320.5  59.0 chic
## 4879  -1.810495937  0.558994480  2321.5  55.0 chic
## 4880  -1.775834072 -2.073609978  2322.5  67.0 chic
## 4881   1.102381238 -0.087417977  2323.5  73.0 chic
## 4882   2.858251256 -3.575769043  2324.5  53.0 chic
## 4883  10.265889154 -3.840421058  2325.5  52.0 chic
## 4884  -3.789452157 -0.230940005  2326.5  57.0 chic
## 4885  -3.038923110 -0.982331231  2327.5  61.0 chic
## 4886  -7.596363182  0.748885469  2328.5  61.0 chic
## 4887   7.246598073 -2.943359013  2329.5  58.0 chic
## 4888   4.743044107 -3.581624937  2330.5  46.0 chic
## 4889   1.060714572 -0.797141027  2331.5  55.0 chic
## 4890   5.161381484 -0.589578151  2332.5  57.0 chic
## 4891  -3.228510787  0.246387966  2333.5  67.0 chic
## 4892   2.495287680  0.097777519  2334.5  69.0 chic
## 4893  11.568403863 -1.437195983  2335.5  68.0 chic
## 4894   7.727734129 -2.164283337  2336.5  64.0 chic
## 4895  -4.891201224  1.738938865  2337.5  61.0 chic
## 4896   9.576520759 -2.411184883  2338.5  60.0 chic
## 4897   3.150465443 -3.575096729  2339.5  55.0 chic
## 4898   4.033158763 -1.047089382  2340.5  56.0 chic
## 4899   3.260742083 -1.063005448  2341.5  65.0 chic
## 4900  -8.007232706 -1.614375956  2342.5  67.0 chic
## 4901  10.797746748 -1.138368089  2343.5  73.0 chic
## 4902   3.823562034 -2.470997605  2344.5  60.0 chic
## 4903   6.602381238 -2.348479241  2345.5  55.0 chic
## 4904   7.866492096 -2.401712200  2346.5  56.0 chic
## 4905  10.286381484 -3.161288156  2347.5  54.0 chic
## 4906  -0.141201224  0.326973706  2348.5  60.0 chic
## 4907   9.957956576  3.375910100  2349.5  64.0 chic
## 4908  29.692544047  8.009151859  2350.5  78.0 chic
## 4909  34.088909924  8.337539158  2351.5  77.0 chic
## 4910  10.269047905  2.426735469  2352.5  80.0 chic
## 4911  -5.623456871 -2.939337152  2353.5  70.0 chic
## 4912 -13.599534560 -1.762311879  2354.5  57.0 chic
## 4913  -9.684985150 -2.486727262  2355.5  69.0 chic
## 4914   4.892644189 -2.922052354  2356.5  72.0 chic
## 4915   4.577508653 -2.231728198  2357.5  70.0 chic
## 4916   1.038614706 -2.245645277  2358.5  67.0 chic
## 4917   4.786381484 -2.423176481  2359.5  60.0 chic
## 4918   5.810714572  0.711526715  2360.5  65.0 chic
## 4919   8.187281441 -1.036967737  2361.5  66.0 chic
## 4920  -1.314285428 -3.019816411  2362.5  74.0 chic
## 4921   3.892644189 -1.455078205  2363.5  74.0 chic
## 4922   7.535841987 -1.170640249  2364.5  72.0 chic
## 4923   2.435714572  2.663919012  2365.5  71.0 chic
## 4924   0.640957370 -2.770210989  2366.5  71.0 chic
## 4925   7.698562034 -1.022441981  2367.5  75.0 chic
## 4926  -0.672491347 -2.338021627  2368.5  72.0 chic
## 4927  -1.326476854 -0.235526923  2369.5  68.0 chic
## 4928 -11.902709010  0.896247553  2370.5  62.0 chic
## 4929  -1.968104633 -1.967746726  2371.5  66.0 chic
## 4930  -0.759285830 -0.106807611  2372.5  66.0 chic
## 4931  11.452508653  1.061159633  2373.5  71.0 chic
## 4932  12.411381484 -2.481705421  2374.5  74.0 chic
## 4933  -0.277724785 -3.444948292  2375.5  68.0 chic
## 4934   5.952508653 -2.089996751  2376.5  70.0 chic
## 4935  21.199825430 -1.398372088  2377.5  76.0 chic
## 4936  17.018453896 -2.327528561  2378.5  74.0 chic
## 4937   1.785841986  2.043683560  2379.5  70.0 chic
## 4938  11.205322857 -2.122033108  2380.5  73.0 chic
## 4939  14.602381238 -1.383226397  2381.5  81.0 chic
## 4940   5.586273646 -3.201660879  2382.5  73.0 chic
## 4941  11.329185504 -1.432465909  2383.5  69.0 chic
## 4942  13.963909924  1.044633559  2384.5  72.0 chic
## 4943   9.823562034 -1.497983107  2385.5  75.0 chic
## 4944   9.638957660 -2.034994213  2386.5  77.0 chic
## 4945   5.724130120 -3.409994213  2387.5  73.0 chic
## 4946   0.452879242 -0.028281625  2388.5  73.0 chic
## 4947  -1.452756742 -0.424893064  2389.5  77.0 chic
## 4948  -0.297120758 -3.409994213  2390.5  66.0 chic
## 4949  -1.796951849 -3.534911896  2391.5  63.0 chic
## 4950  -3.884771300  0.465005787  2392.5  67.0 chic
## 4951  -4.856271346 -1.361614958  2393.5  67.0 chic
## 4952  -5.650834072 -2.736614958  2394.5  63.0 chic
## 4953  -5.093104633 -3.826660879  2395.5  63.0 chic
## 4954   2.807463454 -0.789649773  2396.5  66.0 chic
## 4955   8.047243258 -2.092675564  2397.5  69.0 chic
## 4956  13.368124326 -0.497983107  2398.5  73.0 chic
## 4957  10.099165928 -0.201660879  2399.5  77.0 chic
## 4958   8.410841986 -3.034994213  2400.5  74.0 chic
## 4959  27.144800054 -4.819948292  2401.5  73.0 chic
## 4960  14.974165928 -4.611614958  2402.5  71.0 chic
## 4961  -3.804855387 -4.819948292  2403.5  70.0 chic
## 4962  -3.088787424 -3.690038754  2404.5  73.0 chic
## 4963   4.036381484 -2.986614958  2405.5  76.0 chic
## 4964   6.597290993 -4.451660879  2406.5  63.0 chic
## 4965   2.765796787 -2.481705421  2407.5  67.0 chic
## 4966   1.288656191 -3.493327546  2408.5  67.0 chic
## 4967   7.599165928 -3.528281625  2409.5  78.0 chic
## 4968  -3.464158014 -3.148372088  2410.5  76.0 chic
## 4969  -1.676437966 -1.986614958  2411.5  77.0 chic
## 4970   3.166017125 -2.997983107  2412.5  79.0 chic
## 4971  -0.716841237 -1.786026585  2413.5  74.0 chic
## 4972  -3.130285182 -2.731705421  2414.5  71.0 chic
## 4973   1.547243258 -0.714996751  2415.5  71.0 chic
## 4974   2.638941881  2.298339121  2416.5  74.0 chic
## 4975   7.325697059  1.543683560  2417.5  76.0 chic
## 4976  17.703048151  0.721718375  2418.5  82.0 chic
## 4977  -0.505285182 -3.081316440  2419.5  70.0 chic
## 4978  -1.458230209 -2.655591802  2420.5  68.0 chic
## 4979   0.368124326 -3.153281625  2421.5  67.0 chic
## 4980  -5.230952095 -3.694948292  2422.5  64.0 chic
## 4981   5.619545909 -1.856705421  2423.5  65.0 chic
## 4982   7.894800054  0.184961246  2424.5  67.0 chic
## 4983   5.769047905 -1.341559731  2425.5  76.0 chic
## 4984   5.328048151 -2.959939629  2426.5  76.0 chic
## 4985   0.340729310 -2.591559731  2427.5  71.0 chic
## 4986   1.911212576 -1.674893064  2428.5  70.0 chic
## 4987   8.411381484 -1.243327546  2429.5  73.0 chic
## 4988  21.869714818 -4.201660879  2430.5  72.0 chic
## 4989   9.244545909 -2.943661943  2431.5  75.0 chic
## 4990   5.726787229  0.721718375  2432.5  77.0 chic
## 4991   8.972290993 -2.307765716  2433.5  77.0 chic
## 4992  14.956360663  0.049616926  2434.5  80.0 chic
## 4993  19.108188091 -0.161053247  2435.5  80.0 chic
## 4994  15.618124326 -1.148372088  2436.5  79.0 chic
## 4995  13.432499261 -2.028281625  2437.5  77.0 chic
## 4996   2.515796787 -4.819948292  2438.5  66.0 chic
## 4997   6.892747030 -4.595835856  2439.5  63.0 chic
## 4998   0.732420944  1.621555449  2440.5  61.0 chic
## 4999   5.006531125  1.002966892  2441.5  74.0 chic
## 5000  -9.804872467  3.444423539  2442.5  75.0 chic
## 5001  -3.336726417 -3.008226397  2443.5  76.0 chic
## 5002  -7.453077531 -4.361614958  2444.5  78.0 chic
## 5003  -3.567518643 -4.243327546  2445.5  75.0 chic
## 5004  -5.047491347 -3.595835856  2446.5  63.0 chic
## 5005  -9.339158014 -1.174893064  2447.5  63.0 chic
## 5006  -3.211343809 -3.319948292  2448.5  65.0 chic
## 5007  -8.794677143 -4.689513509  2449.5  56.0 chic
## 5008  -7.340209007 -1.091559731  2450.5  56.0 chic
## 5009   6.037508384 -1.456316440  2451.5  68.0 chic
## 5010   1.667943167 -1.278281625  2452.5  69.0 chic
## 5011  18.911569838 -1.486614958  2453.5  75.0 chic
## 5012 -12.878010480 -4.778281625  2454.5  58.0 chic
## 5013  -8.267966342 -3.493327546  2455.5  54.0 chic
## 5014 -16.331541050 -2.356705421  2456.5  58.0 chic
## 5015  -9.158143790 -4.081316440  2457.5  63.0 chic
## 5016   1.244545909 -4.648372088  2458.5  53.0 chic
## 5017  -4.689285428 -3.943661943  2459.5  49.0 chic
## 5018  -9.902724785 -0.591559731  2460.5  56.0 chic
## 5019  -5.933442768  4.002966892  2461.5  59.0 chic
## 5020  -7.775834072 -0.622983107  2462.5  55.0 chic
## 5021  -9.235112762 -0.403281625  2463.5  61.0 chic
## 5022   3.359440373  1.055051708  2464.5  65.0 chic
## 5023  26.204185504 -1.539649773  2465.5  72.0 chic
## 5024   8.588740860  3.925903275  2466.5  69.0 chic
## 5025  -6.973896857  0.627016893  2467.5  59.0 chic
## 5026   8.054145608 -4.372983107  2468.5  57.0 chic
## 5027  -2.467400902 -4.689513509  2469.5  52.0 chic
## 5028  -5.648981189 -3.986614958  2470.5  41.0 chic
## 5029  -8.471444957 -4.153281625  2471.5  40.0 chic
## 5030  -7.464279429 -4.581316440  2472.5  39.0 chic
## 5031 -12.285255610 -1.955366441  2473.5  44.0 chic
## 5032 -12.598892960 -0.987140204  2474.5  49.0 chic
## 5033 -11.729020960  3.366773603  2475.5  53.0 chic
## 5034  -1.204274733  5.127016893  2476.5  58.0 chic
## 5035   7.595348577  6.293683560  2477.5  66.0 chic
## 5036   4.108645127  2.161673144  2478.5  67.0 chic
## 5037  -3.713351976 -2.739971252  2479.5  55.0 chic
## 5038  -6.279354419 -3.508226397  2480.5  51.0 chic
## 5039 -13.068850180 -1.784994213  2481.5  52.0 chic
## 5040 -10.835430690 -0.591559731  2482.5  54.0 chic
## 5041  -7.896482875  3.893294579  2483.5  62.0 chic
## 5042   1.434639222  5.351627912  2484.5  66.0 chic
## 5043  -0.703347280  0.951669915  2485.5  58.0 chic
## 5044  -9.449472005  2.785003249  2486.5  59.0 chic
## 5045 -17.941190090  1.293683560  2487.5  65.0 chic
## 5046 -10.681942750 -2.606705421  2488.5  63.0 chic
## 5047 -11.026472720 -2.732991770  2489.5  62.0 chic
## 5048  -3.597704477  1.465005787  2490.5  68.0 chic
## 5049  -5.255946095  1.451669915  2491.5  61.0 chic
## 5050   2.161297030 -3.493327546  2492.5  50.0 chic
## 5051  -4.604368885 -1.190038754  2493.5  46.0 chic
## 5052 -10.971147950  5.798339121  2494.5  50.0 chic
## 5053 -16.930846160  8.423339121  2495.5  59.0 chic
## 5054 -11.544677140  7.184961246  2496.5  63.0 chic
## 5055 -14.236042340 -0.789649773  2497.5  55.0 chic
## 5056 -10.763232030 -2.438200498  2498.5  48.0 chic
## 5057 -12.211343810 -2.789649773  2499.5  46.0 chic
## 5058  -4.777724785 -1.398372088  2500.5  46.0 chic
## 5059  -0.902709007 -1.104175965  2501.5  49.0 chic
## 5060 -13.652724790 -2.508226397  2502.5  49.0 chic
## 5061 -15.674420600 -1.145710380  2503.5  40.0 chic
## 5062 -13.305821470 -5.206316440  2504.5  39.0 chic
## 5063 -15.564879440 -4.694948292  2505.5  37.0 chic
## 5064 -14.148212770 -1.664649773  2506.5  38.0 chic
## 5065 -13.773212770 -0.243327546  2507.5  45.0 chic
## 5066 -15.402724790 -4.486614958  2508.5  36.0 chic
## 5067 -12.814879440 -4.159994213  2509.5  32.0 chic
## 5068 -13.152709010  0.184961246  2510.5  31.0 chic
## 5069 -16.652709010 -2.299893064  2511.5  36.0 chic
## 5070 -10.711343810 -3.414649773  2512.5  30.0 chic
## 5071  -8.694375674 -2.909994213  2513.5  27.0 chic
## 5072  -7.861042340 -2.440038754  2514.5  30.0 chic
## 5073  -9.605283203 -3.797757745  2515.5  24.0 chic
## 5074  -9.111058119 -3.399124647  2516.5  19.0 chic
## 5075 -15.025869880  0.381672454  2517.5  22.0 chic
## 5076 -15.694391450  2.283440269  2518.5  29.0 chic
## 5077 -15.745100310  4.243336582  2519.5  32.0 chic
## 5078 -12.064879440  4.408440269  2520.5  38.0 chic
## 5079 -17.652724790 -4.331316440  2521.5  39.0 chic
## 5080 -17.527724790 -3.646035248  2522.5  34.0 chic
## 5081 -16.069391450 -1.576660879  2523.5  34.0 chic
## 5082 -16.819391450 -3.279041832  2524.5  35.0 chic
## 5083 -12.314879440 -2.451660879  2525.5  34.0 chic
## 5084   2.455322857 -3.038699774  2526.5  34.0 chic
## 5085  10.080322857 -3.206316440  2527.5  31.0 chic
## 5086 -12.936799920 -0.258226397  2528.5  28.0 chic
## 5087 -12.694391450  0.923339121  2529.5  32.0 chic
## 5088  -6.359203213 -3.015149050  2530.5  21.0 chic
## 5089 -14.152681470 -1.997983107  2531.5  15.0 chic
## 5090 -17.500507560 -2.997983107  2532.5  25.0 chic
## 5091 -12.442536550 -2.236614958  2533.5  18.0 chic
## 5092 -14.919677140 -0.106705421  2534.5  16.0 chic
## 5093 -16.984203210  1.825106936  2535.5  30.0 chic
## 5094  -3.044677143 -3.955366441  2536.5  23.0 chic
## 5095 -10.069391450 -2.580366441  2537.5   4.0 chic
## 5096 -14.606546100  2.276776416  2538.5   8.0 chic
## 5097 -16.413551040  1.096597452  2539.5  17.0 chic
## 5098 -16.900869880  0.741773603  2540.5  17.0 chic
## 5099 -17.214297330  2.059961246  2541.5  25.0 chic
## 5100  -6.327867631 -2.409994213  2542.5   8.0 chic
## 5101 -14.765367630  6.006672454  2543.5   8.0 chic
## 5102 -11.429778290  0.344408198  2544.5  14.0 chic
## 5103 -15.057034300  5.612733060  2545.5   6.0 chic
## 5104  -7.862287921 -0.569948292  2546.5   6.0 chic
## 5105 -12.057034300 -3.214996751  2547.5  -1.0 chic
## 5106 -13.952867630 13.116773603  2548.5   8.0 chic
## 5107  -9.671302111 -2.731705421  2549.5   5.0 chic
## 5108 -13.577867630  0.493336582  2550.5   5.0 chic
## 5109 -12.036200960  7.491773603  2551.5  11.0 chic
## 5110 -13.274541330  1.673339121  2552.5  12.0 chic
## 5111 -11.179778290  2.618336582  2553.5  13.0 chic
## 5112  -6.036200964  4.931990994  2554.5  21.0 chic
## 5113  -0.598700964 -1.456316440  2555.5  27.0 chic
## 5114  -4.890367631  0.090005787  2556.5  16.0 chic
```

which indicates that we should include every variable except the variables death through pm25median The equivalent code in base R would be


```r
i <- match("death", names(chicago))
j <- match("pm25median", names(chicago))
head(chicago[, -(i:j)])
```

```
##    o3median  so2median    time tmpd city
## 1 -19.59234  1.9280426 -2556.5 31.5 chic
## 2 -19.03861 -0.9855631 -2555.5 33.0 chic
## 3 -20.21734 -1.8914161 -2554.5 33.0 chic
## 4 -19.67567  6.1393413 -2553.5 29.0 chic
## 5 -19.21734  2.2784649 -2552.5 32.0 chic
## 6 -17.63400  9.8585839 -2551.5 40.0 chic
```

The select() function also allows a special syntax that allows you to specify variable names based on patterns. So, for example, if you wanted to keep every variable that ends with a “n”, we could do


```r
subset <- select(chicago, ends_with("n"))
str(subset)
```

```
## 'data.frame':	5114 obs. of  4 variables:
##  $ pm10median: num  -7.434 NA -0.827 5.566 NA ...
##  $ pm25median: num  NA NA NA NA NA NA NA NA NA NA ...
##  $ o3median  : num  -19.6 -19 -20.2 -19.7 -19.2 ...
##  $ so2median : num  1.928 -0.986 -1.891 6.139 2.278 ...
```

Or if we wanted to keep every variable that starts with a “p”, we could do


```r
subset <- select(chicago, starts_with("p"))
str(subset)
```

```
## 'data.frame':	5114 obs. of  2 variables:
##  $ pm10median: num  -7.434 NA -0.827 5.566 NA ...
##  $ pm25median: num  NA NA NA NA NA NA NA NA NA NA ...
```

### filter()

The filter() function is used to extract subsets of rows from a data frame. This function is similar to the existing subset() function in R but is quite a bit faster in my experience.

Suppose we wanted to extract the rows of the chicago data frame where the levels of tmpd are greater than 25 (which is a reasonably high level), we could do


```r
chic.f <- filter(chicago, tmpd > 25)
str(chic.f)
```

```
## 'data.frame':	4590 obs. of  8 variables:
##  $ death     : int  130 150 101 135 126 130 129 109 125 153 ...
##  $ pm10median: num  -7.434 NA -0.827 5.566 NA ...
##  $ pm25median: num  NA NA NA NA NA NA NA NA NA NA ...
##  $ o3median  : num  -19.6 -19 -20.2 -19.7 -19.2 ...
##  $ so2median : num  1.928 -0.986 -1.891 6.139 2.278 ...
##  $ time      : num  -2556 -2556 -2554 -2554 -2552 ...
##  $ tmpd      : num  31.5 33 33 29 32 40 34.5 29 26.5 32.5 ...
##  $ city      : chr  "chic" "chic" "chic" "chic" ...
```

```r
summary(chic.f$tmpd)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   25.50   39.50   54.00   54.09   68.50   92.00
```

We can place an arbitrarily complex logical sequence inside of filter()


```r
chic.f <- filter(chicago, tmpd > 25 & death > 100)
str(chic.f)
```

```
## 'data.frame':	3856 obs. of  8 variables:
##  $ death     : int  130 150 101 135 126 130 129 109 125 153 ...
##  $ pm10median: num  -7.434 NA -0.827 5.566 NA ...
##  $ pm25median: num  NA NA NA NA NA NA NA NA NA NA ...
##  $ o3median  : num  -19.6 -19 -20.2 -19.7 -19.2 ...
##  $ so2median : num  1.928 -0.986 -1.891 6.139 2.278 ...
##  $ time      : num  -2556 -2556 -2554 -2554 -2552 ...
##  $ tmpd      : num  31.5 33 33 29 32 40 34.5 29 26.5 32.5 ...
##  $ city      : chr  "chic" "chic" "chic" "chic" ...
```

### arrange()

The arrange() function is used to reorder rows of a data frame according to one of the variables/columns. Reordering rows of a data frame (while preserving corresponding order of other columns) is normally a pain to do in R.

Here we can order the rows of the data frame by date, so that the first row is the lowest death observation and the last row is the largest observation.


```r
chicago <- arrange(chicago, death)
head(chicago)
```

```
##      death pm10median pm25median    o3median   so2median    time tmpd city
## 3871    69  -1.818182         NA  -8.0292794  1.12452237  1313.5 64.5 chic
## 4610    73 -19.320548         NA  -5.8691868  0.07297014  2052.5 66.0 chic
## 194     77  -8.801262         NA -13.1703598 -3.48994781 -2363.5 64.5 chic
## 4204    77 -19.165746  -10.14961   3.4361575  3.60026234  1646.5 70.0 chic
## 1656    78         NA         NA  -0.4076242 -2.38698223  -901.5 69.5 chic
## 606     79  45.563177         NA  -7.0826849          NA -1951.5 64.0 chic
```

```r
tail(chicago)
```

```
##      death  pm10median pm25median   o3median   so2median    time tmpd city
## 2929   176 -13.0097895         NA  -9.952374 -0.05382329   371.5 19.5 chic
## 1096   186   0.7626582         NA -14.561288  1.81565467 -1461.5 30.5 chic
## 3117   226  20.9416667         NA  29.703545  2.26858562   559.5 91.5 chic
## 3120   228  -3.2327324         NA   5.649732 -2.31588816   562.5 78.5 chic
## 3119   287  -8.3333333         NA  21.115009 -0.93301259   561.5 83.0 chic
## 3118   411  14.7981033         NA  28.115091  0.69765993   560.5 86.0 chic
```


```r
chicago <- arrange(chicago, desc(death))
head(chicago)
```

```
##      death  pm10median pm25median   o3median   so2median    time tmpd city
## 3118   411  14.7981033         NA  28.115091  0.69765993   560.5 86.0 chic
## 3119   287  -8.3333333         NA  21.115009 -0.93301259   561.5 83.0 chic
## 3120   228  -3.2327324         NA   5.649732 -2.31588816   562.5 78.5 chic
## 3117   226  20.9416667         NA  29.703545  2.26858562   559.5 91.5 chic
## 1096   186   0.7626582         NA -14.561288  1.81565467 -1461.5 30.5 chic
## 2929   176 -13.0097895         NA  -9.952374 -0.05382329   371.5 19.5 chic
```

```r
tail(chicago)
```

```
##      death pm10median pm25median    o3median   so2median    time tmpd city
## 5005    79  28.876056  -4.572464  -9.3391580 -1.17489306  2447.5 63.0 chic
## 1656    78         NA         NA  -0.4076242 -2.38698223  -901.5 69.5 chic
## 194     77  -8.801262         NA -13.1703598 -3.48994781 -2363.5 64.5 chic
## 4204    77 -19.165746 -10.149606   3.4361575  3.60026234  1646.5 70.0 chic
## 4610    73 -19.320548         NA  -5.8691868  0.07297014  2052.5 66.0 chic
## 3871    69  -1.818182         NA  -8.0292794  1.12452237  1313.5 64.5 chic
```

### rename()

Renaming a variable in a data frame in R is surprisingly hard to do! The rename() function is designed to make this process easier.


```r
head(chicago[, 1:8], 4)
```

```
##      death pm10median pm25median  o3median  so2median  time tmpd city
## 3118   411  14.798103         NA 28.115091  0.6976599 560.5 86.0 chic
## 3119   287  -8.333333         NA 21.115009 -0.9330126 561.5 83.0 chic
## 3120   228  -3.232732         NA  5.649732 -2.3158882 562.5 78.5 chic
## 3117   226  20.941667         NA 29.703545  2.2685856 559.5 91.5 chic
```


```r
chicago <- rename(chicago, pm25 = pm25median)
```

The syntax inside the rename() function is to have the new name on the left-hand side of the = sign and the old name on the right-hand side.

### mutate()

The mutate() function exists to compute transformations of variables in a data frame. Often, you want to create new variables that are derived from existing variables and mutate() provides a clean interface for doing that.

Here we create a pm25detrend variable that subtracts the mean from the pm25 variable.


```r
chicago <- mutate(chicago, pm25detrend = pm25 - mean(pm25, na.rm = TRUE))
```

There is also the related transmute() function, which does the same thing as mutate() but then drops all non-transformed variables.


```r
head(transmute(chicago,
               pm10detrend = pm10median - mean(pm10median, na.rm = TRUE),
               o3detrend = o3median - mean(o3median, na.rm = TRUE)))
```

```
##      pm10detrend  o3detrend
## 3118  14.9444928  30.294468
## 3119  -8.1869437  23.294385
## 3120  -3.0863428   7.829108
## 3117  21.0880563  31.882922
## 1096   0.9090478 -12.381911
## 2929 -12.8633999  -7.772997
```

### group_by()

The group_by() function is used to generate summary statistics from the data frame within strata defined by a variable. For example, in this air pollution dataset, you might want to know what the average annual level of PM2.5 is. So the stratum is the year, and that is something we can derive from the date variable. In conjunction with the group_by() function we often use the summarize() function (or summarise() for some parts of the world).

The general operation here is a combination of splitting a data frame into separate pieces defined by a variable or group of variables (group_by()), and then applying a summary function across those subsets (summarize()).


```r
days <- group_by(chicago, time)
summarize(days, pm25 = mean(pm25, na.rm = TRUE),
          o3median = max(o3median),
          .groups = "drop")
```

```
## # A tibble: 5,114 × 3
##      time  pm25 o3median
##     <dbl> <dbl>    <dbl>
##  1 -2556.   NaN    -19.6
##  2 -2556.   NaN    -19.0
##  3 -2554.   NaN    -20.2
##  4 -2554.   NaN    -19.7
##  5 -2552.   NaN    -19.2
##  6 -2552.   NaN    -17.6
##  7 -2550.   NaN    -15.4
##  8 -2550.   NaN    -12.2
##  9 -2548.   NaN    -20.1
## 10 -2548.   NaN    -18.6
## # ℹ 5,104 more rows
```

summarize() returns a data frame with days as the first column, and then the annual averages of pm25 and o3 max.

In a slightly more complicated example, we might want to know what are the average levels of ozone (o3) and nitrogen dioxide (no2) within quintiles of pm25. A slicker way to do this would be through a regression model, but we can actually do this quickly with group_by() and summarize().


```r
qq <- quantile(chicago$pm25,
               seq(0, 1, 0.2), 
               na.rm = TRUE)
chicago <- mutate(chicago, pm25.quint = cut(pm25, qq))
```

Now we can group the data frame by the pm25.quint variable.


```r
quint <- group_by(chicago, pm25.quint)
```


```r
summarize(quint, 
          o3 = mean(o3median, 
                    na.rm = TRUE),
          o2 = mean(so2median,
                    na.rm = TRUE),
          .groups = "drop")
```

```
## # A tibble: 6 × 3
##   pm25.quint        o3      o2
##   <fct>          <dbl>   <dbl>
## 1 (-16.4,-7.33] -0.514 -2.24  
## 2 (-7.33,-3.06] -1.65  -1.39  
## 3 (-3.06,1.33]  -1.54  -0.160 
## 4 (1.33,7.14]   -2.57   0.0458
## 5 (7.14,38.2]   -2.41   0.485 
## 6 <NA>          -2.25  -0.634
```

The pipeline operater %>% is very handy for stringing together multiple dplyr functions in a sequence of operations. Notice above that every time we wanted to apply more than one function, the sequence gets buried in a sequence of nested function calls that is difficult to read, i.e.


```r
third(second(first(x)))
```


```r
first(x) %>%
  second %>%
  third
```

Take the example that we just did in the last section where we computed the mean of o3 and no2 within quintiles of pm25. There we had to

create a new variable pm25.quint
split the data frame by that new variable
compute the mean of o3 and no2 in the sub-groups defined by pm25.quint


```r
mutate(chicago, pm25.quint = cut(pm25, qq)) %>%
  group_by(pm25.quint) %>%
  summarize(o3 = mean(o3median, na.rm = TRUE),
            so2 = mean(so2median, na.rm = TRUE),
            .groups = "drop")
```

```
## # A tibble: 6 × 3
##   pm25.quint        o3     so2
##   <fct>          <dbl>   <dbl>
## 1 (-16.4,-7.33] -0.514 -2.24  
## 2 (-7.33,-3.06] -1.65  -1.39  
## 3 (-3.06,1.33]  -1.54  -0.160 
## 4 (1.33,7.14]   -2.57   0.0458
## 5 (7.14,38.2]   -2.41   0.485 
## 6 <NA>          -2.25  -0.634
```

This way we don’t have to create a set of temporary variables along the way or create a massive nested sequence of function calls.

Notice in the above code that I pass the chicago data frame to the first call to mutate(), but then afterwards I do not have to pass the first argument to group_by() or summarize(). Once you travel down the pipeline with %>%, the first argument is taken to be the output of the previous element in the pipeline.

## Control Structures *

Control structures in R allow you to control the flow of execution of a series of R expressions. Basically, control structures allow you to put some “logic” into your R code, rather than just always executing the same R code every time. Control structures allow you to respond to inputs or to features of the data and execute different R expressions accordingly.

Commonly used control structures are

- if and else: testing a condition and acting on it
- for: execute a loop a fixed number of times
- while: execute a loop while a condition is true
- repeat: execute an infinite loop (must break out of it to stop)
- break: break the execution of a loop
- next: skip an interation of a loop

### if else

The if-else combination is probably the most commonly used control structure in R (or perhaps any language). This structure allows you to test a condition and act on it depending on whether it’s true or false.


```r
if(condition) {
  ## do something
} else {
  ## do something else
} else {
  ## do something else
}

## continue code
```


```r
## Generate a uniform random number
x <- runif(1, 0, 10)

if(x > 3) {
  y <- 10 
} else { 
  y <- 0
}
```

The value of y is set depending on whether x > 3 or not. This expression can also be written a different, but equivalent, way in R.


```r
y <- if(x > 3) {
  10
} else {
  0
}
```

Of course, the else clause is not necessary. You could have a series of if clauses that always get executed if their respective conditions are true.


```r
if(condition1) {
  
  
} 

if(condition2){ 
  
}
```

### for loops

For loops are pretty much the only looping construct that you will need in R. While you may occasionally find a need for other types of loops, in my experience doing data analysis, I’ve found very few situations where a for loop wasn’t sufficient.

In R, for loops take an interator variable and assign it successive values from a sequence or vector. For loops are most commonly used for iterating over the elements of an object (list, vector, etc.)


```r
for(i in 1:10) { 
  print(i)
}
```

```
## [1] 1
## [1] 2
## [1] 3
## [1] 4
## [1] 5
## [1] 6
## [1] 7
## [1] 8
## [1] 9
## [1] 10
```

This loop takes the i variable and in each iteration of the loop gives it values 1, 2, 3, …, 10, executes the code within the curly braces, and then the loop exits.

The following three loops all have the same behavior.


```r
x <- c("a", "b", "c", "d")

for(i in 1:4) {
  ## Print out each element of x
  print(x[i])
}
```

```
## [1] "a"
## [1] "b"
## [1] "c"
## [1] "d"
```

The seq_along() function is commonly used in conjunction with for loops in order to generate an integer sequence based on the length of an object (in this case, the object x).


```r
## Generate a sequence based on length of x
for(i in seq_along(x)) {
  print(x[i])
}
```

```
## [1] "a"
## [1] "b"
## [1] "c"
## [1] "d"
```

It is not necessary to use an index-type variable.


```r
for(letter in x) {
  print(letter)
}
```

```
## [1] "a"
## [1] "b"
## [1] "c"
## [1] "d"
```

For one line loops, the curly braces are not strictly necessary.


```r
for(i in 1:4) print(x[i])
```

```
## [1] "a"
## [1] "b"
## [1] "c"
## [1] "d"
```

### Nested for loops

for loops can be nested inside of each other.


```r
x <- matrix(1:6, 2, 3)

for(i in seq_len(nrow(x))) {
  for(j in seq_len(ncol(x))) {
    print(x[i, j])
  }
}
```

```
## [1] 1
## [1] 3
## [1] 5
## [1] 2
## [1] 4
## [1] 6
```

### while loops

While loops begin by testing a condition. If it is true, then they execute the loop body. Once the loop body is executed, the condition is tested again, and so forth, until the condition is false, after which the loop exits.


```r
count <- 0

while(count < 10) {
  print(count)
  count <- count +1
}
```

```
## [1] 0
## [1] 1
## [1] 2
## [1] 3
## [1] 4
## [1] 5
## [1] 6
## [1] 7
## [1] 8
## [1] 9
```


```r
z <- 5
set.seed(1)

while(z >= 3 && z <= 10) {
  coin <- rbinom(1, 1, 0.5)
  
  if(coin == 1) { ## Random walk
    z <- z +1
  } else {
    z <- z -1
  }
}
print(z)
```

```
## [1] 2
```

Conditions are always evaluated from left to right. For example, in the above code, if z were less than 3, the second test would not have been evaluated.

### repeat loops

repeat initiates an infinite loop right from the start. These are not commonly used in statistical or data analysis applications but they do have their uses. The only way to exit a repeat loop is to call break.

One possible paradigm might be in an iterative algorith where you may be searching for a solution and you don’t want to stop until you’re close enough to the solution. In this kind of situation, you often don’t know in advance how many iterations it’s going to take to get “close enough” to the solution.


```r
x0 <- 1
tol <- 1e-8

repeat {
  x1 <- computeEstimate()
  
  if(abs(x1 - x0) < tol) { ## close enough?
    break
  } else {
      x0 <- x1
    }
}
```

### next, break

next is used to skip an iteration of a loop.


```r
for(i in 1:100) {
  if (i <= 20) {
    ## Skip the first 20 iterations
    next
  }
  ## Do something here
  if (i > 15) {
    break
  }
}
```

break is used to exit a loop immediately, regardless of what iteration the loop may be on.


```r
for (i in 1:100){
  print(i)
  
  if(i > 20) {
    ## Stop loop after 20 iterations
    break
  }
}
```

```
## [1] 1
## [1] 2
## [1] 3
## [1] 4
## [1] 5
## [1] 6
## [1] 7
## [1] 8
## [1] 9
## [1] 10
## [1] 11
## [1] 12
## [1] 13
## [1] 14
## [1] 15
## [1] 16
## [1] 17
## [1] 18
## [1] 19
## [1] 20
## [1] 21
```

