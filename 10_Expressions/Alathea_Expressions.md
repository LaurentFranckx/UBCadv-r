# Expressions
Alathea DL  
2014-12-02  

# Exercises

## Structure of expressions

### There’s no existing base function that checks if an element is a valid component of an expression (i.e., it’s a constant, name, call, or pairlist). Implement one by guessing the names of the “is” functions for calls, names, and pairlists.

### pryr::ast() uses non-standard evaluation. What’s its escape hatch to standard evaluation?

### What does the call tree of an if statement with multiple else conditions look like?

### Compare ast(x + y %+% z) to ast(x ^ y %+% z). What do they tell you about the precedence of custom infix functions?

### Why can’t an expression contain an atomic vector of length greater than one? Which one of the six types of atomic vector can’t appear in an expression? Why?

## Names

### You can use formals() to both get and set the arguments of a function. Use formals() to modify the following function so that the default value of x is missing and y is 10.


```r
g <- function(x = 20, y) {
      x + y
    }
```

### Write an equivalent to get() using as.name() and eval(). Write an equivalent to assign() using as.name(), substitute(), and eval(). (Don’t worry about the multiple ways of choosing an environment; assume that the user supplies it explicitly.)

## Calls

### The following two calls look the same, but are actually different. What’s the difference? Which one should you prefer?


```r
(a <- call("mean", 1:10))
#> mean(1:10)
(b <- call("mean", quote(1:10)))
#> mean(1:10)
identical(a, b)
#> [1] FALSE

class(a)
class(b)

eval(a)
eval(b)
```

`quote` returns `1:10` as an expression

### Implement a pure R version of do.call().


```r
my_do_call <- function(function_name, ...){
  # get the call to my_do_call
  .call <- match.call()
  
  # remove the "my_do_call" function from the call
  my_call <- as.list(.call[-1])
  
  # evaluate the new call
  eval(as.call(my_call))
}

my_do_call(mean, 1:10)
```

```
## [1] 5.5
```


### Concatenating a call and an expression with c() creates a list. Implement concat() so that the following code works to combine a call and an additional argument.


```r
concat(quote(f), a = 1, b = quote(mean(a)))
#> f(a = 1, b = mean(a))
```

Possible solution:

```r
concat <- function(what, a, b){
  as.call(list(what, substitute({a = a_arg}, list(a_arg = a))[[2]],  substitute({b = b_arg}, list(b_arg = b))[[2]])) 
}

concat(quote(f), a = 1, b = quote(mean(a)))
```


### Since list()s don’t belong in expressions, we could create a more convenient call constructor that automatically combines lists into the arguments. Implement make_call() so that the following code works.


```r
make_call(quote(mean), list(quote(x), na.rm = TRUE))
#> mean(x, na.rm = TRUE)
make_call(quote(mean), quote(x), na.rm = TRUE)
#> mean(x, na.rm = TRUE)
```

### How does mode<- work? How does it use call()?

### Read the source for pryr::standardise_call(). How does it work? Why is is.primitive() needed?

### standardise_call() doesn’t work so well for the following calls. Why?


```r
standardise_call(quote(mean(1:10, na.rm = TRUE)))
#> mean(x = 1:10, na.rm = TRUE)
standardise_call(quote(mean(n = T, 1:10)))
#> mean(x = 1:10, n = T)
standardise_call(quote(mean(x = 1:10, , TRUE)))
#> mean(x = 1:10, , TRUE)
```

### Read the documentation for pryr::modify_call(). How do you think it works? Read the source code.

### Use ast() and experimentation to figure out the three arguments in an if() call. Which components are required? What are the arguments to the for() and while() calls?


## Capturing the current call

## Pairlists

## Parsing and deparsing

## Walking the call tree with recursive functions

***
***

# Notes

## Structure of expressions

* use `quote()` to capture an expression


```r
require(pryr)
```

```
## Loading required package: pryr
```

```r
# Four components of expressions
# 1: constants
ast("a")
```

```
## \-  "a"
```

```r
ast(1)
```

```
## \-  1
```

```r
#2: names / symbols
ast(x)
```

```
## \- `x
```

```r
#3: calls (every operation is a function call)
ast(f())
```

```
## \- ()
##   \- `f
```

```r
ast(f(1,2))
```

```
## \- ()
##   \- `f
##   \-  1
##   \-  2
```

```r
ast(a + b)
```

```
## \- ()
##   \- `+
##   \- `a
##   \- `b
```

## Names

* Names are also called symbols. `as.symbol()` = `as.name()`

## Calls

* calls are similar to lists
* calls have `length`, `[[` and `[`
* first element is the function
* remaining elements are the arguments
* calls can be modified using `[]` and `<-`
* use `call` or `as.call` to create your own call

## Capturing the current call

## Pairlists

## Parsing and deparsing

## Walking the call tree with recursive functions
