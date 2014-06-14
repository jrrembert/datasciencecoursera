*[Disclaimer: these are notes derived from the Coursera MOOC: [R Programming](https://class.coursera.org/rprog-004), taught by Professors Roger D. Peng, Jeff Leek, and Brian Caffo of the John Hopkins Bloomberg School of Public Health. These are my personal notes taken via their lecture videos and slideware and may have annotations, code, or be structured differently from how their materials are presented. In some cases there may be typos or inaccuracies depending on the version of R used and packages loaded. There are no references or work pertaining to any of the graded exercises for the course and these materials should only be used as a quick reference for understanding certain aspects of the R language.]* 
 
#R Programming Week 2

##Control Structures

* ```if, else```
* ```for```
* ```while```
* ```repeat``` - execute an infinite loop
* `break` - exit
* `next` - skip a loop iteration
* `return`

If/Else Statements

```
if(x > 3) {
	y <- 10 ## or just 10
} else {
	y <- 0 ## or just 0
}
```

`Else` isn't necessary. Can also use `else if`.

For loops

```
x <- c("a","b","c","d")

for(i in 1:4) {
	print(x[i])
}
[1] "a"
[1] "b"
[1] "c"
[1] "d"
```

Repeat

* Usually run until convergence in values get within some threshold of a given number
* Have to call `break` to end or else runs forever

```
x0 <- 1
tol <- 1e-8

repeat {
	x1 <- computeEstimate()
	
	if(abs(x1 - x0) < tol) { ## Run until values exceed tol)
		break
	} else {
		x0 <- x1
	}
}
```

##Functions

Functions are **first-class objects* meaning they can be treated like an other R object

* Can be passed as args to other functions
* Usually don't do via console
* Can be nested to define a function within a function. Return value of functions is last expression in function body to be evaluated
* Have *named* arguments and *default* args
	* *formal* arguments are included in function def
	* `formals` function returns list of all function args
	* *defaults* generally assigned with '=' operator and don't need to be specified at the command line
	* Can be matched positionally or be name, following arguments are all equivalent
		* Not recommended to rearrange order too much as it can be confusing 

```
> mydata <- rnorm(100)
> sd(mydata)
> sd(x = mydata)
> sd(x = mydata, na.rm = FALSE)
> sd(na.rm = FALSE, x = mydata)
> sd(na.rm = FAKSE, mydata)
```

* Can partially match named arguments as long as the partial match matches something.

Defining a function

```
f <- function(a, b) {
	a^2
}
f(2) ## R functions are lazy so this works
## [1] 4 
```

The **"..."** Argument

The **...** argument indicates a variable num of args that are usually passed to other functions

* Usually used with extending another function and don't want to copy entire arg list of original
	
```
myplot <- function(x, y , type = "1", ...) {
	plot(x, y, type = type, ...)
}
```

* Generic functions use **...** so extra args can be passed to methods (more later)

```
> mean
function (x, ...)
UseMethod("mean")
```

* Also useful when number of arguments passed function cannot be known in advance
	* All args after the **...** must be named, cannot be positional. 

```
> args(paste) 
function (..., sep = " ", collapse = NULL)

> args(cat)
function (..., file = "", sep = " ", fill = FALSE, labels = NULL, 
    append = FALSE) 
```

##Coding Standards in R

Important to make code readable/understandable to others

Important

* Use text editor and save as text file (no extra formatting or markup)
	* RStudio editor is a text editor 
* Indent code 
	* At least 4 spaces recommended 
	* Can cause you to write code differently as well (prevents from using too many next functions/control structures)
* Limit width of code (usually 80 max)
* Limit length of functions
	* Limit to one activity 
	* Splitting functions helps debugging errors
	
##Scoping Rules

When R tries to bind a value to a symbol, it searches through a series of `environments` to find appropriate value. Order is roughly:

1. Search global environment for symbol name matching one requested
2. Search namespaces for each package on search list

```
> search()
 [1] ".GlobalEnv"        "package:stringr"   "package:digest"    "package:RCurl"     "package:bitops"   
 [6] "package:swirl"     "tools:rstudio"     "package:stats"     "package:graphics"  "package:grDevices"
[11] "package:utils"     "package:datasets"  "package:methods"   "Autoloads"         "package:base"     
```

*Environments* are:

* collection of (symbol, value) pairs, ie. x <- 1, x is symbol and 1 is value
* Every environment has parent env and an env can have multiple children
* Empty envs have no parent
* Function + environment = a *closure* or function closure


Packages


* Order of packages is important
* Startup packages can be modified by user
* R has separate namespaces for functions and non-functions so it's possible to have object named c and a function named c

Scoping Rules

* Main feature that separates R from S
	* Scoping rules determine how value is associated with function free var
	* R uses *lexical scoping* as an alternative to *dynamic scoping* 
	* Related to scoping rules is how R uses search *list* to bind value to symbol
	* Turn out lexical scoping is useful for simplfying statistical computations

Lexical scoping

* *Value of free variables are searched for in the environment in which the functions was defined*

```
f <- function(x, y) {
	x^2 + y / z
}
```

* z is a free var. Scoping rules determine how values are assigned to free vars.
	* Free variables are not formal arguments and are not local variables (defined within function body) 
	
Searching for value of a free var:

* If val not found in env, parent env searched
* Search continues down sequence of parents until hitting top-level environment; this is usually global env (workspace) or namespace of package
* After top-level env, search continues until hitting empty env; error is thrown after that if not found

Why is this relevant?

* Typically, functions defined in global env
* Logical to most people and usually right thing to do
* However, in R you can have functions within other functions
	* Can't do in C
* Environment of nested function is body of function it is nested in

```
make.power <- function(n) {
	pow <- function(x) {
		x^n
	}
	pow
}
```

This function returns another function as its value

```
> cube <- make.power(3) ## New function that takes a number and raises to 3rd power
> square <- make.power(2) ## New function that takes a number and raises to 2nd power
> cube(3) 
[1] 27
> square(3)
[1] 9
``` 

Exploring a function closure

* Use `ls` and `get`

```
> ls(environment(cube))
[1] "n" "pow"
> get("n", environment(cube))
[1] 3
> get("pow", environment(cube))
function(x) {
        x^n
    }
<environment: 0x7fdc111e5670>
```

Lexical scoping vs Dynamic Scoping

```
y <- 10

f <- function(x) {
	y <- 2 
	y <- 2 + g(x) ## g is free var
}

g <- function(x) {
	x*y ## y is free var
}

# What is value of f(3)?
> f(x)
> [1] 9

```

* For lexical scoping the value of y in function g is looked up in environment in which function was defined, in this case the global env so value of y is 10
* For dynamic scoping the value of y is looked up in environment from which function was called (sometimes referred to as calling environment
	* In R, calling env is called *parent frame*
* So value of y would be 2

Other Languages that support lexical scoping

* Python
* Perl
* Scheme
* Common Lisp (all languages converge to Lisp)

Consequences of lexical scoping

* In R, all objects must be stored in memory
* All functions must have pointers to respective envs, which could be anywhere
* In S-PLUS, free vars are always looked up in global namespace, so everything can be stored on disk because "defining env" of all functions is same

##Vectorized Operations

* Makes it easy to use on command-line without a lot of looping
	* Similar to Matlab
* Many operations vectorized to make code more efficient, concise, and easier to read

```
> x <- 1:4; y <- 6:9
> x + y
[1] 7 9 11 13
> x > 2
[1] FALSE FALSE TRUE TRUE
```

Vectorized Matrix Operations

```
> x <- matrix(1:4, 2, 2); y <- matrix(rep(10, 4), 2, 2)
> x * y ## element-wise multiplication
     [,1] [,2]
[1,]  10   30
[2,]  20   40
> x / y
     [,1] [,2]
[1,]  0.1  0.3
[2,]  0.2  0.4
> x %*% y ## true matrix multiplication
     [,1] [,2]
[1,]  40   40
[2,]  60   60
```

##Dates and Times in R

* Represented by `Date` type
* Times represented by the `POSIXct` or the `POSIXlt` class
	* `POSIXct` just large integer
	* `POSIXlt` stores as list and a bunch of other useful information 
* Dates stored internally as days since 1970-01-01
* Times stored internall as seconds since 1970-01-01

Dates represented by Date class and coerced from cha string using `as.Date()` function

Useful generic functions:
* `weekdays`
* `months`
* `quarters`

Coercing from character string
```
> x <- Sys.time()
> x
[1] "2014-06-14 13:20:34 PDT"
> p <- as.POSIXlt(x)
> names(unclass(p))
 [1] "sec"    "min"    "hour"   "mday"   "mon"    "year"   "wday"   "yday"   "isdst"  "zone"   "gmtoff"
> p$sec
[1] 34.00861
```

* Use `strptime` for date strings formatted in other ways (use `?strptime` for specific formatting options)
* Can use **+** and **-** mathematical operations on datetime objects
* Useful in that they keep track of leapyears, leap seconds, daylight savings, and time zones







