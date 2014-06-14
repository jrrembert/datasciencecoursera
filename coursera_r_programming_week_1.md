#R Programming Week 1

##History

* R is a dialect of S, a statistical anguage developed by John Chambers and others at Bell Labs in the 1976.
* R created in New Zealand by Ross Ihaka and Robert Gentlemen in 1991.

Features

* Active developer community
* Free to use
* Great IDE (RStudio)
* Runs on almost any platform/OS
* Sophisticated graphic capabilities in relation to other statistical packages

Drawbacks

* Essentially based on 4-year old tech
* Little bit for dynamic or 3d graphics
* New features and updates dependent on demand and community interest
* Objects generally stored in physical memory

Design

* 2 parts
	* Base R system from CRAN
	* Everything else 
* Divided into modular packages
	* About 4000 available on CRAN
	* Many more on bioconductor.org
	* Personal packages on the web
	
##Data Types

5 basic "atomic" classes of objects

* Character
* Numeric
* Integer
* Complex
* Logical 

Most basic object is a **vector**

* Must contain objects of the same type
* One exception: can have **lists** which can contain objects of different types.
* Empty vectors can be created with the ```vector()``` function

Numbers

* Default is double precission
* Integers must be explicity specified with "L" suffix

Attributes

* names, dirnames, dimensions, class, length, other stuff

Entering input

* At the R prompt, we type expressions. The ```<-``` sumbol is the assignment operator.

```
> x <- 1 # This is a comment
> print(x) # Explicitly print object
[1] 1 
> x
[1] 1 # Autoprinted object
```
* The [1] is a vector and 1 is the first element

Integer sequence (indicated with a colon)

```
> x <- 1:5
> x 
[1] 1 2 3 4 5 
```

Creating Vectors

* A vector of objects

```
> x <- c(0.5, 0.6) ## numeric
> x <- c(TRUE, FALSE) ## logical
> x <- c(T, F) ## logical
> x <- c("a", "b") ## character
> x <- c(9:29) ## integer
> x <- c(1+0i, 2+4i) ## complex
```

* Using ```vector()``` function

```
> x <- vector("numeric", length = 10) ##
> x
[1] 0 0 0 0 0 0 0 0 0 0
```

Mixing objects

* When different objects are mixed in a vector, all elements in vector are coerced into same class

```
> y <- c(1.7, "a") ## character
```

* Objects can be explicitly coerced using ```as.* functions, if available.

```
> x <- 0:6
> class(x)
[1] "integer"
> as.numeric(x)
[1] 0 1 2 3 4 5 6
> as.logical(x)
[1] FALSE TRUE TRUE TRUE TRUE TRUE TRUE
```

* Nonsensical as NAs

Matrices

* Vectors with *dimension* attribute
* All elements must be same class

```
> m <- matrix(nrow = 2, ncol = 3)
> m
	[,1][,2][,3]
[1,] NA  NA  NA
[2,] NA  NA  NA
> dim(m)
[1] 2 3
> attributes(m)
$dim
[1] 2 3
> m <- matrix(1:6, nrow = 2, ncol = 3)
> m
	[,1][,2][,3]
[1,]  1   3   5
[2,]  2   4   6
> m <- 1:10
> m
[1] 1 2 3 4 5 6 7 8 9 10
> dim(m) <- c(2, 5)
> m
	[,1][,2][,3][,4][,5]
[1,]  1   3   5   7   9
[2,]  2   4   6   8  10
```

Column and Row-Binding

```
> x <- 1:3
> y <- 10:12
> cbind(x, y)
     x y
[1,] 1 10
[2,] 2 11
[3,] 3 12
> rbind(x, y)
  [,1][,2][,3]
x   1   2   3
y  10  11  12
```

Lists

* Special type of vector that can contain elements of different classes.

```
> x <- list(1, "a")
> x
[[1]]
[1] 1

[[2]]
[1] "a"
```

Factors

* Used to represent categorical data
* Can be ordered or unordered
* Treated specially by modelling functions like ```lm()``` and ```glm()```
* Using factors with labels is *better* than integers because factors are self-describing

```
> x <- factor(c("yes", "yes", "no"))
> x
[1] yes yes no
Levels: no yes
> table(x)
x
no yes
1 2
> unclass(x)
[1] 2 2 1
attr(,"levels")
[1] "no"  "yes"
```

* Order of levels can be using ```levels``` argument to ```factor()```

```
> x <- factor(c("yes", "yes", "no"), levels = c("yes", "no"))
> x
[1] yes yes no
Levels: yes no
```

Missing values

* A ```NaN``` value is ```NA``` but not the other way around
* ```NA``` values have a class also, so there are character NAs etc.
 
 ```
 > x <- c(1, NA, NaN)
 > is.na(x)
 [1] FALSE TRUE TRUE
 > is.nan(x)
 [1] FALSE FALSE TRUE
 ```
 
 Data Frames
 
 * Store tabular data
 * Special type of list where every element has to have same length
 * Each element of list can be though of as column and length of each element is number of rows
 * Unlike matrices, elements do not have to be same class
 * Have special attribute called ```row.names```
 * Usually created by calling ```read.table()``` or ```read.csv()```
 * Can be converted to matrix by calling ```data.matrix()```
 
 ``` 
 > x <- data.frame(foo = 1:4, bar = c(T, T, F, F))
 > x
   foo bar
 1  1  TRUE
 2  2  TRUE
 3  3  FALSE
 4  4  FALSE
 > nrow(x)
 [1] 4
 > ncol(x)
 [1] 2
 ```
 
 Names
 
 * R objects can have names, which is very useful for writing readable code and self-describing objects
 
 ```
 > x <- 1:3
 > names(x)
 NULL
 > names(x) <- c("foo", "bar", "norf")
 > x
 foo bar norf
 1 2 3
 > names(x)
 [1] "foo" "bar" "norf"
 ```
 
 * Lists can also have names
 
 ```
 > x <- list(a = 1, b = 2)
 > x
 $a
 [1] 1
 $b
 [1] 2
```

* Matrices too

```
> m <- matrix(1:4, nrow = 2, ncol = 2)
> dimnames(m) <- list(c("a", "b"), c("c", "d"))
> m
  c d
a 1 3
b 2 4
```

##Subsetting

There are a number of operators to extract R object subsets

* ```[``` always return an object of same class as original
* ```[[``` is used to extract element of a list or data frame
* ```$``` used to extract elements of a list or data frame by name

```
> x <- c("a", "b", "c")
> x[1]
[1] "a"
> x[2]
[1] "b"
> x[1:2]
[1] "a" "b"
> x[x > "a"]
[1] "b" "c"
> u <- x > "a"
> u
[1] FALSE TRUE TRUE
> x[u]
[1] "b" "c"
```

Subsetting a matrix

```
> x <- matrix(1:6, 2, 3)
> x[1, 2]
[1] 3
> x[2, 1]
[1] 2
> x[1,] ## Indices can be missing
[1] 1 3 5
> x[,2]
[1] 3 4
> x[1, 2, drop = FALSE] ## Returns as 1x1 matrix rather than vector of length 1
    [,1]
[1,]  3
> x[1, , drop = FALSE] ## Subsetting single col or row will give vector by default, not matrix
    [1,][2,][3,]
[1,]  1   3   5
```

Subsetting lists

```
> x <- list(foo = 1:4, bar = 0.6)
> x[1]
$foo
[1] 1 2 3 4
> x [[1]]
[1] 1 2 3 4
> x$bar
[1] 0.6
>x[["bar"]]
[1] 0.6
> x["bar"]
$bar
[1] 0.6
```

* ```[[``` operator can be used with *computed* indices; ```$``` can only be used with literal names

```
> name <- "foo"
> x[[name]]
[1] 1 2 3 4
> x$name
NULL
```

Subsetting Nested List Elements

* ```[[``` can take an integer sequence

``` 
> x <- list(a = list(10, 12, 14), b = c(3.14, 2.81))
> x[[c(1,3)]]
[1] 14
```

Partial Matching

* Partial matching of names allowed with ```[[``` and ```$``


```
> x <- list(aardvark = 1:5)
> x$a
[1] 1 2 3 4 5
> x[["a"]] ## By default, [[ doesn't match
NULL
> x[["a", exact = FALSE]]
[1] 1 2 3 4 5
```

Removing NA Values

```
> x <- c(1, 2, NA)
> bad <- is.na(x)
> x[!bad]
[1] 1 2
```

* Used ```complete.cases``` to remove NAs from multiple objects

##Reading and Writing Data

A few principal functions in R

* ```read.table```, ```read.csv```, for reading tabular data
* ```readLines```, for reading lines of a text file
* ```source```, for reading in R code files (```inverse``` of ```dump```)
* ```dget```, for reading in R code files (```inverse``` of ```dput```)
* ```load```, for reading in saved workspaces
* ```unserialize```, for reading single R objects in binary form

Analogous functions for writing data to files

* write.table
* writeLines
* dump
* dput
* save
* serialize

Reading data with read.table

* `file`, name of file or connection
* `header`, logical indicating if file has header line
* `sep`, string indicating how cols are separated
* `colClasses`, a character vector indicating class of each col in dataset
* `nrows`
* `comment.char`, character string indicating comment character
* `skip`, the number of lines to skip from beginning
* `stringsAsFactors`, should character variables be coded as factors?

* For small datasets, this is good enough:

```
data <- read.table("foo.txt")
```

* ```read.csv``` is identical except default separator is comma

* For larger datasets:

	* Read help page for read.table for hints
	* Make rough calc of memory required to store dataset. If dataset is larger that amount of RAM on comp, stop there
	* Set `comment.char = ""` if there are no commented lines in file
	* Set ```nrows```. Doesn't make R run faster but helps with memory usage. Can use Unix tool ```wc``` to calc number of lines in file
	* Use ```colClasses``` argument if you know classes for each column in data frame. Can drastically speed up processing (often twice as much)
	
```
## Quick and dirty way to find all column classes
initial <- read.table("datatable.txt", nrows = 100)
classes <- sapply(initial, class)
tabAll <- read.table("datatable.txt", colClasses = classes)
```

Calculating Memory Requirements

* Consider a data frame with 1,500,000 rows and 120 columns, all with numeric data. How much memory needed to store data frame?
	* 1,500,000 x 120 x 8 bytes/numeric
	* 1440000000 bytes
	* 1440000000 / 220 bytes/MB
	* 1,373.29 MB
	* 1.34 GB
	
Textual Formats

* ```dumping``` and ```dputing``` are useful because the resulting text format is editable, and recoverable in case of corruption
* Unline writing table or csv file, these preserver metadata.
* Textual formats work better with VCSs
* Can be longer-lived
* Adhere to Unix philosophy (store all kinds of data in space efficient way)
* Downside: format is not very space-efficient

dput-ting R Objects

```
> y <- data.frame(a = 1, b = "a")
> dput(y)
> structure(list(a = 1, 
				 b = structure(IL, .Label = "a", 
				 		       class = "factor")),
		   .Names = c("a", "b"), row.names = c(NA, -IL), 
		   class = "data.frame")
> dput(y, file = "y.R")
> new.y <- dget("y.R")
> new.y
  a b 
1 1 a
```

Dumping R Objects

* Multiple objects can be deparsed using ```dump``` function and read back in using ```source```

```
> x <- "foo"
> y <- data.frame(a = 1, b = "a")
> dump(c("x", "y"), file = "data.R")
> rm(x, y)
> source("data.R")
> y
  a b
1 1 a
> x
[1] "foo"
 ```
 
 Interfaces to Outside World
 
 * Data are read in using *connection* interfaces
 	* ```file```, connection to file
 	* ```gzfile```, connection to gzip file
 	* ```bzfile```, connection to bzip2 file
 	* ```url```, open webpage connection

 ```
 > str(file)
function (description = "", open = "", blocking = TRUE, encoding = getOption("encoding"))
 ```
 
 * Description is name of file
 * Open is code indicating
 	* "r" for read-only
 	* "w" for writing (and initializing new file)
 	* "a" for appending
 	* "rb", "wb", "ab" for reading, writing, or appending in binary mode (Windows)
 	
Connections

```
con <- file("foo.txt", "r")
data <- read.csv(con)
close(con)
```

is the same as

```
data <- read.csv("foo.txt")
```

Reading Lines of a Text File

```
> con <- gzfile("words.gz")
> x <- readLines(con, 10)
> x
```

* ```writeLines``` takes a character vector and writes each element one line at a time to a text file

Using `readLines` on webpage

```
## This might take time
> con <- url("http://www.jhsph.edu", "r")
> x <- readLines(con)
> head(x)
[1] "<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 4.0 Transitional//EN\">"
[2] ""
[3] "<html>"
[4] "<head>"
[5] "\t<meta http-equiv=\"Content-Type\" content=\"text/html;charset=utf-8
```


		
















 

