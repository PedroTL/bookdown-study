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

**Packages**



## List element in R

### Creating List


```r
Name <- c("Pedro", "Lilian")
RollNum <- c(23, 24)

sample_list <- list(Name, RollNum)
sample_list
```

```
## [[1]]
## [1] "Pedro"  "Lilian"
## 
## [[2]]
## [1] 23 24
```

### Name List Element in R


```r
names(sample_list) <- c("Name", "Roll_Num")
sample_list
```

```
## $Name
## [1] "Pedro"  "Lilian"
## 
## $Roll_Num
## [1] 23 24
```

### Accessing Elements of List


```r
sample_list[1]
```

```
## $Name
## [1] "Pedro"  "Lilian"
```

```r
sample_list$Name
```

```
## [1] "Pedro"  "Lilian"
```

```r
sample_list[1][1]
```

```
## $Name
## [1] "Pedro"  "Lilian"
```

## Vectors

### Creating Vectors


```r
c(1, 4, 7)
```

```
## [1] 1 4 7
```

```r
# Check length of the vector
length(c(1, 4, 7))
```

```
## [1] 3
```

```r
# Assign to a variable
Num_variable <- c (1, 4, 7)

# Vector Names, containing names of person appearing in an Exam
Names <- c("Pedro", "Lilian", "Dirlene")

# Extract the first element of Names vector.
Names[1]
```

```
## [1] "Pedro"
```

```r
# Creating another vector and them combining
Missed_names <- c("Luis", "Rafael")

# Including Missed_names in the Names vector
Names <- c(Names, Missed_names)
Names
```

```
## [1] "Pedro"   "Lilian"  "Dirlene" "Luis"    "Rafael"
```

### Class of Vector

R possesses a simple generic function mechanism which can be used for an object-oriented style of programming. Method dispatch takes place based on the class of the first argument to the generic function.

typeof determines the (R internal) type or storage mode of any object


```r
class(Names)
```

```
## [1] "character"
```

```r
class(Num_variable)
```

```
## [1] "numeric"
```

```r
typeof(Num_variable)
```

```
## [1] "double"
```

### Adding Vectors of different type

The mix_vector we created had multiple data types-Numeric and Character, however R converts the multiple data type to a single data type through a process called coercion. Logical values are converted to numbers: TRUE is converted to 1 and FALSE to 0.

- Values are converted to the simplest type required to represent all information.
- The ordering is roughly logical < integer < numeric < complex < character < list
- Objects of type raw are not converted to other types.
- Objects can also be explicitly coerced using “as.”" function. For example to coerce mix vector to numeric use:-


```r
mix_vector <- c(1, 2, "Pedro")
class(mix_vector)
```

```
## [1] "character"
```

```r
mix_vector <- as.numeric(mix_vector)
```

```
## Warning: NAs introduzidos por coerção
```


```r
Names[1] <- "Roberto" # Changing the first element of a vector
```

### Accessing elements of vectors


```r
Names[-1]
```

```
## [1] "Lilian"  "Dirlene" "Luis"    "Rafael"
```

```r
Names[c(-1, -2)] # All elements except the first and second elements will be printed. We can also save it, this will remove the first 2 Names
```

```
## [1] "Dirlene" "Luis"    "Rafael"
```

### Creating vector using in Built Functions

Create a sequential vector from 1 to 10 which increases by 1 in length


```r
seq(1, 10, by = 1) 
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10
```

Create a sequential vector from 1 to 10 which increases by 0.5 in length


```r
seq(1, 10, by = 0.5) 
```

```
##  [1]  1.0  1.5  2.0  2.5  3.0  3.5  4.0  4.5  5.0  5.5  6.0  6.5  7.0  7.5  8.0
## [16]  8.5  9.0  9.5 10.0
```

### Lets try to repeat vectors


```r
rep(c(1, 2), times = c(5, 5))
```

```
##  [1] 1 1 1 1 1 2 2 2 2 2
```

- typeof(Vector): This method tells you the data type of the vector.
- Sort(Vector): This method helps us to sort the items in the Ascending order.
- length(Vector): This method counts the number of elements in a vector.
- head(Vector, limit): This method return the top six elements (if you Omit the limit). If you specify the limit as 4 then, it returns the first 4 elements.
- tail(Vector, limit): It returns the last six elements (if you Omit the limit). If you specify the limit as 2, then it returns the last two elements.

### Arithmetic Operator in Vectors in R


```r
V1 <- c(1, 2, 3, 4)
V2 <- c(5, 6, 7, 8)
```

Perform addition


```r
V1 + V2
```

```
## [1]  6  8 10 12
```

Operation was performed elementwise. First element of V1 was added to the first element of V2


```r
V12_division <- V2 / V1
V12_division
```

```
## [1] 5.000000 3.000000 2.333333 2.000000
```

```r
typeof(V12_division)
```

```
## [1] "double"
```

Converting to Numeric


```r
V12_division <- as.integer(V12_division)
```

## Logical operations in R

Checks if both variables are equal/grater/grater than or equal. If true, then returns TRUE else FALSE


```r
a <- 2
b <- 3
a == b
```

```
## [1] FALSE
```

```r
a > b
```

```
## [1] FALSE
```

```r
a >= b
```

```
## [1] FALSE
```

```r
a <= b
```

```
## [1] TRUE
```


```r
a <- c(1, 2, 3, 4)
b <- c(5, 6, 7, 8)
c <- c(1, 2, 3, 4)
d <- c(1, 2, 5, 6)
e <- c(1, 2)
```

When we perform Logical Operation in Vectors the operations is elementwise and cyclical. 


```r
a == b
```

```
## [1] FALSE FALSE FALSE FALSE
```

```r
a==c
```

```
## [1] TRUE TRUE TRUE TRUE
```

```r
b==d
```

```
## [1] FALSE FALSE FALSE FALSE
```

```r
a>b
```

```
## [1] FALSE FALSE FALSE FALSE
```

```r
b>a
```

```
## [1] TRUE TRUE TRUE TRUE
```

### Comparing vectors of different length?

When applying an operation to two vectors that requires them to be the same length, R automatically recycles, or repeats, elements of the shorter one, until it is long enough to match the longer Vector.


```r
a>e
```

```
## [1] FALSE FALSE  TRUE  TRUE
```

```r
a>=e
```

```
## [1] TRUE TRUE TRUE TRUE
```

```r
b>=e
```

```
## [1] TRUE TRUE TRUE TRUE
```

### Logical operands in R


```r
ab <- a&b
ab
```

```
## [1] TRUE TRUE TRUE TRUE
```

## Matrices

Matrices are two-dimensional data structures in R and are arranged in a rectangular layout. Matrices can contain only one data type. We can create matrices of any of the six data types we discussed before. A matrix can also be thought of as a vector in two dimension.


```r
matrix(1:6, nrow = 3, ncol = 2)
```

```
##      [,1] [,2]
## [1,]    1    4
## [2,]    2    5
## [3,]    3    6
```

```r
matrix(1:6, 3,2)
```

```
##      [,1] [,2]
## [1,]    1    4
## [2,]    2    5
## [3,]    3    6
```

As you have guessed from the above output, R will automatically pick the number of rows and columns based on the order of input. Let’s specify one of the dimension (either col or row) and see the output


```r
matrix(1:6, ncol = 3)
```

```
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6
```


```r
M <- matrix(1:20, ncol = 4)
colnames(M) <- c("A", "B", "C", "D")
rownames(M) <- c("E", "F", "G", "H", "I")
M
```

```
##   A  B  C  D
## E 1  6 11 16
## F 2  7 12 17
## G 3  8 13 18
## H 4  9 14 19
## I 5 10 15 20
```


```r
M["F", "D"]
```

```
## [1] 17
```

```r
M[, "D"]
```

```
##  E  F  G  H  I 
## 16 17 18 19 20
```

### Extracting Row/Column from Matrices


```r
M[2, c(1, 3)] 
```

```
##  A  C 
##  2 12
```

### Operations in Matrices


```r
M1 <- matrix(1:15, nrow = 5)
M1
```

```
##      [,1] [,2] [,3]
## [1,]    1    6   11
## [2,]    2    7   12
## [3,]    3    8   13
## [4,]    4    9   14
## [5,]    5   10   15
```

```r
M2 <- matrix(1:15, nrow = 5)
M2
```

```
##      [,1] [,2] [,3]
## [1,]    1    6   11
## [2,]    2    7   12
## [3,]    3    8   13
## [4,]    4    9   14
## [5,]    5   10   15
```

```r
M3 <- matrix(2:10, nrow = 5)
```

```
## Warning in matrix(2:10, nrow = 5): comprimento dos dados [9] não é um
## submúltiplo ou múltiplo do número de linhas [5]
```

```r
M3
```

```
##      [,1] [,2]
## [1,]    2    7
## [2,]    3    8
## [3,]    4    9
## [4,]    5   10
## [5,]    6    2
```
Additon Operation on Matrices of same dimension


```r
M1 + M2
```

```
##      [,1] [,2] [,3]
## [1,]    2   12   22
## [2,]    4   14   24
## [3,]    6   16   26
## [4,]    8   18   28
## [5,]   10   20   30
```

```r
M1 * M2
```

```
##      [,1] [,2] [,3]
## [1,]    1   36  121
## [2,]    4   49  144
## [3,]    9   64  169
## [4,]   16   81  196
## [5,]   25  100  225
```
What if want to add Matrices of different dimensions? We can check dimension of Matrices using dim function


```r
dim(M1)
```

```
## [1] 5 3
```

```r
dim(M3)
```

```
## [1] 5 2
```
As a basic rule for matrix multiplication number of rows of Matrix1 should be equal to number of columns in Matrix2 when multiplying Matrix1 with Matrix2. 


```r
M4 <- matrix(1:9, nrow = 3)
M5 <- matrix(10:18, nrow = 3)
M4
```

```
##      [,1] [,2] [,3]
## [1,]    1    4    7
## [2,]    2    5    8
## [3,]    3    6    9
```

```r
M5
```

```
##      [,1] [,2] [,3]
## [1,]   10   13   16
## [2,]   11   14   17
## [3,]   12   15   18
```
Result of matrix multiplication


```r
M4%*%M5
```

```
##      [,1] [,2] [,3]
## [1,]  138  174  210
## [2,]  171  216  261
## [3,]  204  258  312
```

You can see the elementwise operation result in R.


```r
M4*M5
```

```
##      [,1] [,2] [,3]
## [1,]   10   52  112
## [2,]   22   70  136
## [3,]   36   90  162
```

```r
M1/M2
```

```
##      [,1] [,2] [,3]
## [1,]    1    1    1
## [2,]    1    1    1
## [3,]    1    1    1
## [4,]    1    1    1
## [5,]    1    1    1
```

What if one of the matrices’ row or column is shorter than the other? Will recycling occur?

As an exercise create a 3X3 matrices and add it to M1.


```r
M3x <- matrix(1:9, nrow = 3)
M3x
M1
M3x + M1
```

### Transposing a Matrix

You may also want to transpose your data in R. This is used to interchange rows and columns i.e rows become columns and columns become rows in new transposed matrix For example.


```r
M1
```

```
##      [,1] [,2] [,3]
## [1,]    1    6   11
## [2,]    2    7   12
## [3,]    3    8   13
## [4,]    4    9   14
## [5,]    5   10   15
```

```r
M3 <- t(M1)
M3
```

```
##      [,1] [,2] [,3] [,4] [,5]
## [1,]    1    2    3    4    5
## [2,]    6    7    8    9   10
## [3,]   11   12   13   14   15
```

As you can see from the output, the rows have become columns and the columns have become rows. This can also be used to reshape a DataFrame.

### Common Matrix Operations in R

Sum of rows in Matrix


```r
rowSums(M1)
```

```
## [1] 18 21 24 27 30
```

Sum of columns in Matrix


```r
colSums(M1)
```

```
## [1] 15 40 65
```

Mean of rows in Matrix


```r
rowMeans(M1)
```

```
## [1]  6  7  8  9 10
```

### Naming Matrix row and column


```r
rownames(M4) <- c("A", "B", "C")
colnames(M4) <- c("D", "E", "F")
M4
```

```
##   D E F
## A 1 4 7
## B 2 5 8
## C 3 6 9
```

We can also extract specific elements of a Matrix by specifying row and columns


```r
M4[3, ]
```

```
## D E F 
## 3 6 9
```

```r
M4[, 3]
```

```
## A B C 
## 7 8 9
```

```r
M4[2, 3]
```

```
## [1] 8
```

### DataFrames

### What is a Data Frame?

DataFrame is a two-dimensional labelled data structure which can have columns of multiple data types. Imagine you have a spread sheet that contains stock related information.We can use data.frame function to create a dataframe in R programming. Lets create a dataframe with 3 rows and two columns.


```r
Data <- data.frame(
  stock_name = c("AACL", "AAK", "MANI"),
  date = as.Date(c("11-3-2020", "12-3-2020", "12-3-2020")),
  Price = c(12,3,224),
  stringsAsFactors = FALSE
)

Data
```

```
##   stock_name       date Price
## 1       AACL 0011-03-20    12
## 2        AAK 0012-03-20     3
## 3       MANI 0012-03-20   224
```

```r
class(Data)
```

```
## [1] "data.frame"
```


```r
data()
data(mtcars)
head(mtcars)
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```

```r
head(mtcars, 3)
```

```
##                mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4     21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag 21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710    22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
```

```r
tail(mtcars)
```

```
##                 mpg cyl  disp  hp drat    wt qsec vs am gear carb
## Porsche 914-2  26.0   4 120.3  91 4.43 2.140 16.7  0  1    5    2
## Lotus Europa   30.4   4  95.1 113 3.77 1.513 16.9  1  1    5    2
## Ford Pantera L 15.8   8 351.0 264 4.22 3.170 14.5  0  1    5    4
## Ferrari Dino   19.7   6 145.0 175 3.62 2.770 15.5  0  1    5    6
## Maserati Bora  15.0   8 301.0 335 3.54 3.570 14.6  0  1    5    8
## Volvo 142E     21.4   4 121.0 109 4.11 2.780 18.6  1  1    4    2
```

We can also use mtcars data to perform few operations in mtcars:- Lets check mtcars’s number of columns and rows


```r
ncol(mtcars)
```

```
## [1] 11
```

```r
nrow(mtcars)
```

```
## [1] 32
```

```r
?mtcars
```

```
## starting httpd help server ... done
```

```r
summary(mtcars)
```

```
##       mpg             cyl             disp             hp       
##  Min.   :10.40   Min.   :4.000   Min.   : 71.1   Min.   : 52.0  
##  1st Qu.:15.43   1st Qu.:4.000   1st Qu.:120.8   1st Qu.: 96.5  
##  Median :19.20   Median :6.000   Median :196.3   Median :123.0  
##  Mean   :20.09   Mean   :6.188   Mean   :230.7   Mean   :146.7  
##  3rd Qu.:22.80   3rd Qu.:8.000   3rd Qu.:326.0   3rd Qu.:180.0  
##  Max.   :33.90   Max.   :8.000   Max.   :472.0   Max.   :335.0  
##       drat             wt             qsec             vs        
##  Min.   :2.760   Min.   :1.513   Min.   :14.50   Min.   :0.0000  
##  1st Qu.:3.080   1st Qu.:2.581   1st Qu.:16.89   1st Qu.:0.0000  
##  Median :3.695   Median :3.325   Median :17.71   Median :0.0000  
##  Mean   :3.597   Mean   :3.217   Mean   :17.85   Mean   :0.4375  
##  3rd Qu.:3.920   3rd Qu.:3.610   3rd Qu.:18.90   3rd Qu.:1.0000  
##  Max.   :4.930   Max.   :5.424   Max.   :22.90   Max.   :1.0000  
##        am              gear            carb      
##  Min.   :0.0000   Min.   :3.000   Min.   :1.000  
##  1st Qu.:0.0000   1st Qu.:3.000   1st Qu.:2.000  
##  Median :0.0000   Median :4.000   Median :2.000  
##  Mean   :0.4062   Mean   :3.688   Mean   :2.812  
##  3rd Qu.:1.0000   3rd Qu.:4.000   3rd Qu.:4.000  
##  Max.   :1.0000   Max.   :5.000   Max.   :8.000
```

```r
str(Data)
```

```
## 'data.frame':	3 obs. of  3 variables:
##  $ stock_name: chr  "AACL" "AAK" "MANI"
##  $ date      : Date, format: "0011-03-20" "0012-03-20" ...
##  $ Price     : num  12 3 224
```

## Functions in R

### Inbuilt functions in R

Functions have arguments, which are a kind of placeholder. Let’s say we want to create a function that can add three numbers. While writing this function we need to pass argument to the function. In following case, i have passed arguments as a,b and c.


```r
addition <- function(a, b, c) {
  a+b+c
}

addition(1,2,3)
```

```
## [1] 6
```

Let’s create a function to convert Fahrenheit to degree Celsius. General expression to convert Fahrenheit to celsius is Celsius,c =(5/9)*(Fahrenheit-32) The function takes Fahrenheit as input and returns output in degree Celsius.


```r
DegreeToFahrenheit <- function(F){
  (5/9) * (F-32)
}
DegreeToFahrenheit(100)
```

```
## [1] 37.77778
```

### Functions without Arguments


```r
cube_of_n <- function() {
  for(x in 1:3) {
    print(x^3)
  }
}
cube_of_n()
```

```
## [1] 1
## [1] 8
## [1] 27
```

## Apply Family Functions

### apply()

apply function takes three arguments apply(X,Margin,FUN) where X is an array or matrix, Margin takes value of 1 or 2 where it implies whether to apply the function row wise or column wise and FUN shows what kind of function to apply such as sum, mean, median etc.


```r
M1 <- matrix(C <- (1:15), nrow=5)
M1
```

```
##      [,1] [,2] [,3]
## [1,]    1    6   11
## [2,]    2    7   12
## [3,]    3    8   13
## [4,]    4    9   14
## [5,]    5   10   15
```

Lets apply “apply” function to fins sum of columns of matrix M1


```r
M1_colsum <- apply(M1, 2, sum)
M1_colsum
```

```
## [1] 15 40 65
```

We can also use apply function to find mean of all columns


```r
M1_colmean <- apply(M1, 2, mean)
M1_colmean
```

```
## [1]  3  8 13
```

Similarly we can use apply function to find maximum value of each column in R matrices.


```r
M1_colmax <- apply(M1, 2, max)
M1_colmax
```

```
## [1]  5 10 15
```

### lapply()

Lapply is applied for operations on a list of objects and returns a list object of same length.

Lets see application of lapply on a list:


```r
Names <- c("Pedro", "Lilian", "Roberto", "Dirlene", "Paulo")
Names_lower <- lapply(Names, tolower)
Names_lower
```

```
## [[1]]
## [1] "pedro"
## 
## [[2]]
## [1] "lilian"
## 
## [[3]]
## [1] "roberto"
## 
## [[4]]
## [1] "dirlene"
## 
## [[5]]
## [1] "paulo"
```


```r
Names <- c("Pedro", "Lilian", "Roberto", "Dirlene", "Paulo")
Names_upper <- lapply(Names, toupper)
Names_upper
```

```
## [[1]]
## [1] "PEDRO"
## 
## [[2]]
## [1] "LILIAN"
## 
## [[3]]
## [1] "ROBERTO"
## 
## [[4]]
## [1] "DIRLENE"
## 
## [[5]]
## [1] "PAULO"
```

### sapply()

sapply takes a list vector or dataframe as an input and returns the output in vector or matrix form Lets use sapply function in the previous example and check the result:


```r
Names <- c("Pedro", "Lilian", "Roberto", "Dirlene", "Paulo")
Names_lower <- lapply(Names, tolower)
Names_lower
```

```
## [[1]]
## [1] "pedro"
## 
## [[2]]
## [1] "lilian"
## 
## [[3]]
## [1] "roberto"
## 
## [[4]]
## [1] "dirlene"
## 
## [[5]]
## [1] "paulo"
```

As you can see from the output using sapply, the result is in a matrix form.

### tapply()

tapply() applies function or operation on subset of vector broken down by a given factor variable. Let’s load iris dataset and tapply on iris dataset.


```r
data(iris)
summary(iris)
```

```
##   Sepal.Length    Sepal.Width     Petal.Length    Petal.Width   
##  Min.   :4.300   Min.   :2.000   Min.   :1.000   Min.   :0.100  
##  1st Qu.:5.100   1st Qu.:2.800   1st Qu.:1.600   1st Qu.:0.300  
##  Median :5.800   Median :3.000   Median :4.350   Median :1.300  
##  Mean   :5.843   Mean   :3.057   Mean   :3.758   Mean   :1.199  
##  3rd Qu.:6.400   3rd Qu.:3.300   3rd Qu.:5.100   3rd Qu.:1.800  
##  Max.   :7.900   Max.   :4.400   Max.   :6.900   Max.   :2.500  
##        Species  
##  setosa    :50  
##  versicolor:50  
##  virginica :50  
##                 
##                 
## 
```


```r
tapply(iris$Sepal.Length, iris$Species, mean)
```

```
##     setosa versicolor  virginica 
##      5.006      5.936      6.588
```

In our iris dataset we have three types of species and we want to calculate average Sepal Length for each of the Species.

Similarly, we can calculate median Sepal Length for the three types of species.

## Exercises  

Exercise 1: Apply Function to Each Row in a Data Frame. Given a data frame df with columns "A" and "B" and "C", calculate the sum of each row and store the results in a new column "Sum" in the same data frame.


```r
df <- data.frame(A = c(3, 5, 3),
                 B = c(4, 5, 2),
                 C = c(12, 3, 1))

df$sum <-apply(df, 1, function(x) sum(x))
```

Exercise 2: lapply to Perform Element-wise Operation on a List. Create a list my_list with three numeric vectors. Use lapply to square each element of the vectors and return a new list with the squared elements.


```r
my_list <- as.list(as.numeric(c(1, 3, 4)))
my_vector <- as.numeric(c(1, 3, 4))

new_list <- lapply(my_list, function(x) x^2)
new_list2 <- lapply(my_vector, function(x) x^2)
```

Exercise 3: Use function to Extract Diagonal Elements of a Matrix. Create a square matrix my_matrix (e.g., 3x3).


```r
Matrix <- matrix(data= 1:9, nrow = 3, ncol = 3)
Matrix
```

```
##      [,1] [,2] [,3]
## [1,]    1    4    7
## [2,]    2    5    8
## [3,]    3    6    9
```

```r
a <- function (x) {
  result <- numeric(0)
  for(i in 1:nrow(x)) {
    for(j in 1:ncol(x)) {
      if (i == j) {
        result <- c(result, x[i, j])
      }
    }
  }
result
}

b <- function (x) {
  x^2
}

b(Matrix)
```

```
##      [,1] [,2] [,3]
## [1,]    1   16   49
## [2,]    4   25   64
## [3,]    9   36   81
```

Exercise 4: tapply to Calculate the Mean by Groups. Given a data frame df with columns "Group" and "Value", use tapply to calculate the mean value for each group in the "Group" column.


```r
df2 <- data.frame(Group = c("A", "B", "C", "A", "B", "C"),
                  Value = c(11, 33, 22, 12, 31, 12))

Group <- df2$Group

tapply(df2$Value, Group, mean)
```

```
##    A    B    C 
## 11.5 32.0 17.0
```

Exercise 5: Apply Function to Specific Columns in a Data Frame. Given a data frame df with columns "A", "B", "C", and "D", use apply to calculate the maximum value for columns "A" and "B", and the minimum value for columns "C" and "D" separately.


```r
df3 <- data.frame(A = as.numeric(c(1, 3, 5)),
                  B = as.numeric(c(2, 3, 11)),
                  C = as.numeric(c(3, 4, 6)),
                  D = as.numeric(c(4, 2, 1)))
df3
```

```
##   A  B C D
## 1 1  2 3 4
## 2 3  3 4 2
## 3 5 11 6 1
```

```r
apply(df3[, 1:2], 2, function(x) max(x)) 
```

```
##  A  B 
##  5 11
```

```r
apply(df3[, 3:4], 2, function(x) min(x)) 
```

```
## C D 
## 3 1
```
