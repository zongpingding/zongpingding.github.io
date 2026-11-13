---
title: Introduction to Haskell
categories: Haskell
math: true
date: 2024-07-23 00:00:00
---


## Basic
{% note danger %}
Reference: https://learnyouahaskell.github.io/chapters.html
{% endnote %}

### ghci

`ghci`  is REPL like other language. some  operator is:

* +,  -,  *,  /, ^: arithmetic operator
* ==: means equal
* /=: means not equal
* ||, &&, not: boolean operator
* True, False

string is decleared by `"<string>"`.

### function

#### call function

In Haskell function are called by `function name`, `<space>`, `parameter` (separatered by `<space>`)

> Unlike other language, call function by writing function name and then writing paramenters in parentheses, usually separatered by comma.

A simple function `succ` (return the successor of  an object if defined)

```haskell
succ 8	

-- 9
```

Other 2 functions are `min`, `max`, which will return the lesser or greater one.

> these 2 functions accept only 2 parameters that can be put in order



**Def (Function Application)**: calling a function then put parameter(s) after it



function application has the highest precedence. so the code:

```haskell
succ 9 + max 5 4 + 1

-- 10 + 5 + 1
-- 16
```

To change the precedence: just use paratheses '()'. so 

```haskell
succ 9 * 10  -- 10*10=100

-- different form

succ (9 * 10) -- 90+1=91
```



**Def (infix function):** a function takes 2 parameters

infix function can be called in 2 forms:

```haskell
div 92 10
92 `div` 10
```



**Def (definition/name):** a function doesn't take any paramenter, which means we can't change it once we have defined.

#### define funnction

Define a function is similar to call it. 

* first write function name
* type out a `<space>`, then the paramenter(s), separated by `<space>`
* type out `=`, after that we define what the function does

Take this form as $f(x)=x+x$, for functions has 2 parameters, take as $f(x, y)=x+y$. So simple, is it ?  BTW, below is a function( without any paramenter) as well.

```haskell
hello = "Hello HasKell!"
```

> * apostrophe "'" can be part of a function name, but usually used in functions not lazy (recall lazy-evaluation in TeX).
> * function name can't begin with uppercase letters, it will be used in 

Take a function 'doubleX' for example (or directly writing this in ghci):

```haskell
double x = x + x
```

we can punch the function in a text file, like 'test.hs', then run ghci here. After loading it by 

```haskell
:l test
```

we can use this function directly in ghci like

```haskell
double 10

-- 20
```

For functions that have 2 parameters, see below:

```haskell
doubleUS x y=x*2+y*2
```

Some Notations:

* Redefine a function by decaler it again. 
* Functions in Haskell don't have to be in any particular order, something like TeX, haha.

### If statement

Like 'if', 'else' in other languages, HasKell has:

* if
* then
* else



**Def (expression):** in haskell, every function and every expression must return something. All of the follows are expressions:

```haskell
5
'a'
"abc"
1+2
succ 2
```



Thus, the `else` part is mandatory in `if` statement. An example of if statement:

```haskell
doubleSmallNum x=if x>100
	then x
	else x*2
```



### data structure

we'll introduce some basic data structures in this section, such as:

#### list

##### introduction

List store several elements of the same type, list denoted by brackets and values sparatered by commas in it. What we'll introduce are:

* list
* string (which is list)
* list comprehensions

Some lists:

```haskell
primeNum = [2, 3, 5, 7, 11]

someAlphabets = ['h', 'e', 'l', 'l', 'o']

-- can't mix characters with integers
```

> String is just syntactic sugar for (char) list, that is string "Hello" is equivlant to `someAlphabets` above.

##### basic functions

Functions about list:

*  `++`: accept 2 list, HasKell will go through every element in the first parameter.
*  `:(cons operator)`: put the first paramenter the head of the second. It takes a number and a list of numbers or a character and a list of characters
*  `!!`: get an element by a given index, indices start at 0.

Some Examples:

```haskell
[1, 2] ++ [3, 4, 5]
['h', 'e', 'l'] ++ ['l', 'o']

1:[1, 2]
'h':['e', 'l', 'l', 'o']

[1, 2, 3]
-- equvilant to 
1:2:3:[]
```



A notation, the following 3 items are different.

```haskell
[]

[[]]

[[], []]
```

Some operations about list:

* List can be nested,  length not required, but type must be the same.

* Lists can be compared if the stuff they contain can be compared whit operators: >=, <=, ==

##### other functions

Some useful list functions :

* `head`: head of a list
* `tail`: return a list that chops the first
* `last`: return the last element
* `init`: return a list that chops the last

> when use the 'head, tail, last, init' function, please check if the list empty !

* `length`: ~
* `null`: check if list empty, if empty return True, else False.
* `reverse`: ~
* `take`: extracts that many elements from the beginning
* `drop`: drops that many elements from the beginning, return the remaining elements.

some other functions:

```haskell
maximum
minimum
sum
product
elem -- if an element belongs to a list
```



##### List range

Use `..` to construct a list range:

```haskell
[1..5]
-- [1, 2, 3, 4, 5]

['a'..'d']
-- ['a', 'b', 'c', 'd']
```

How to assign a step? This is like `\foreach` syntax in TikZ,  just assign a simple pattern:

```haskell
[1,3..10]

-- [1, 3, 5, 7, 9]
```

> * but don't given too ambiguous list pattern or some complex pattern.
> * avoid use float point numbers in range



##### infinite list

If Not specifying the upper limit can construct infinite list. A example of Lazy + Infinite list (for Haskell is Lazy, it'll extract 10 elements instead of infinite elements):

```haskell
take 10 [27, 54..]

-- [27,54,81,108,135,162,189,216,243,270]
```

Some usefule functions to construct infinite list:

* cycle: ~
* repeat: just like cycle an one element list



##### list comprehension

This conception originially comes from set comprehension in Mathematics. Take a set comprehension:
$$
S = \{2\cdot x | x\in \mathbb{N}, x\le 10\}
$$
split this comprehension into 3 parts:

* a output function $2\cdot x$ before pipe '|'
* $x$ is draw from set $\mathbb{N}$
* predicate(s) $x\le 10$​ after the binding part, separatered by comma(s)

In Haskell, list comprehension is much similar to the above:

```haskell
[x^2 | x<- [1..10], odd x]

-- [1,9,25,49,81]
```

> If we consider the symbols `<-` to mathematical symbol $\in$ which it's similar to, List comprehension is almost the same as set comprehension.

 We can also draw from several list in list comprehension, which will produce all combinations of the given lists and then join them by the output function. A simple example:
$$
S = \{x\cdot y | x\in \{1, 2, ..., 5\}, y\in \{1, 2, ...5\} \}
$$
By that, the Haskell form is:

```haskell
[x*y | x<- [1..5], y<-[1..5]]

-- [1,2,3,4,5,2,4,6,8,10,3,6,9,12,15,4,8,12,16,20,5,10,15,20,25]
```

Some application of list comprehension:

1> create the length function, which we will named as `length'`. 

```haskell
length' xs = sum[1 | _ <- xs]
```

in the above, there is a symbol `_`, which means we don't care what the variable name, just use `_`.

2> remove the lowercase letters in a string.

```haskell
removeNonUppercase st = [ c | c <- st, c `elem` ['A'..'Z']]   
```

#### tuples

##### basic

2 ways that tuples different from list:

* the tuples type depends on how many component it has and the types of the components
*  a tuple can contain a combinations of several types, means it's non-homogenous 

A big advantage is that we can create a list contains several tuples, then there will be less bugs. A vectors list:

```haskell
[(1, 2), (3, 4), (5, 7)]
-- in this form, Haskell will check it for us, whilst

[[1, 2], [3, 3, 4], [4, 5]]
-- will not throw an error,but this is not what we want
```

> While there are singleton lists, there's no such thing as a singleton tuple, it will just be the value it contains

##### basic functions

Some useful functions:

* `fst`: takes a tuple of size 2, return the firtst element
* `snd`: ~, return the second element
* `zip`: takes 2 lists (not require these 2 list the same length) and zip them tegother in 1 list by joining the match elements into paris.

A example for zip function:

```haskell
zip [1 .. 5] ["one", "two", "three", "four", "five"] 

-- [(1,"one"),(2,"two"),(3,"three"),(4,"four"),(5,"five")]  
```

##### a problem

**Problem:** which right triangle that has integers for all sides and all sides equal to or smaller than 10 has a perimeter of 24? (assume $a<b<c$, where $a,b,c$ is the egde of this triangle)

We use tuples and list comprehension to solve this problem:

```haskell
rightTriangles' = [ (a,b,c) | c <- [1..10], b <- [1..c], a <- [1..b], a^2 + b^2 == c^2, a+b+c == 24]  

-- [(6,8,10)] 
```

**Notations:** we can ignore the order of these 3 predicates, that's 

```haskell
rightTriangles' = [ (a,b,c) | b <- [1..c], a <- [1..b], c<-[1..10], a^2 + b^2 == c^2, a+b+c == 24]

-- throw error:<interactive>:7:40: error: Variable not in scope: c
```

## Types and Typeclasses

### types

basic types (type name is b begin with uppercase letter)

* Int
* Interger
* Float
* Double
* Bool
* Char

Use `:t` in ghci to show type of a function or an expression. For that checking type of an expression is simple, we then move the functions.

We can given an explicit type declaration to the function we define as a good practice, but for some simple function, Haskell can infer the type of a function. A simple example is:

```haskell
removeNonUppercase :: [Char] -> [Char]  
removeNonUppercase st = [ c | c <- st, c `elem` ['A'..'Z']]  
```

The above symbol `::` means "has type of". How to declare a function which has more than 1 paramenters ?

```haskell
addThree :: Int -> Int -> Int -> Int  
addThree x y z = x + y + z  
```

> Instead of writing as `addThree :: Int,Int,Int -> Int`, we separate theme by `->` . Later on we'll see why.

### typevariable

To refer to a Specified type, we use `Typevariable` which always write as a lowercase letter such as "a, b, c, etc". Let's check a function type:

```haskell
ghci> :t head
-- head :: [a] -> a
head :: GHC.Stack.Types.HasCallStack => [a] -> a
```

which means that function `head` take a List consists of elements of the same type and this type can ba any, like `Int`, `Float`, etc. 

### typeclass

#### definition

What is TypeClass ? In my opinion, it can be see as:

**Def (typeclass):** a typeclass defines the behavior of some types belongs to it.

> The typeclass is not like the class in object oriented languages.

Let check the `type interface` of function `==`.

```
ghci> :t (==)
(==) :: Eq a => a -> a -> Bool
```

We analysis this output step by step:

* `::`: means function `==` has type of the following
* `=>`: **class constraint** symbol
* `Eq a`: means that the following types of the 2 variables must be the same (for the typevariable is the same, it's `a`, then both of them may be `Int`, `Float`, etc) and belong to class `Eq`

How to understand the definition that "TypeClass defines the behaviors of  some types belong to it" ? In the above example, type `a` must be a member of class `Eq`, so that this type of parameters can be compare. **Compare** is the behavior the `Eq	` class gives the type `a`. Indeed, there is some type that can be compare, that is `function`. We can't compare 2 functions obviously.

#### some typeclass

Some typeclasses as follows:

* Eq
* Ord
* Enum

* Bounded
* Num
* Intergral: `Int` and `Integer` 
* Floating

* Show
* Read

`read` and `show` are 2 functions belong to class `Read` and `Show` respectively. Function `show` takes a value whoes type is member of `Show` class and presents it as string. 

Whilst `read` takes a string and return a type which is member of class `Read`, sometimes, we need to declare the return type explictly. Such as the follows:

```haskell
read "3"::Int
-- must decalare explicitly

(read "3"::Int) + 5
-- optional, Haskell can automatically infer the return type
```
