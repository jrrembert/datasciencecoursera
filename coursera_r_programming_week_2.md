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




