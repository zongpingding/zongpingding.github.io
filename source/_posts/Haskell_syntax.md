---
title: Syntax of Haskell
categories: Haskell
date: 2024-07-23 00:00:00
---


## pattern match

### definition

By define a function, we can map some type to another type. Pattern match can apply to any data type: `Int`, `Char`, `String`, etc.

A simple patter match case, mapping a type belong to `Integral` to `String` (in other word, it is to match a varible whoes type that belong to `Integral`):

```haskell
-- pattern define
lucky :: (Integral a) => a->String
lucky 7 = "you are so lucky" 
lucky x = "you are not lucky"
```

**Notation:**

* any typevariable like `a`, `b`, ..., `z` or `abc`, `bcd`; Then patter will match any numbers but only numbers, for the `Integral` in function declaration. This pattern can be also used in other function type, like `Char -> String`. Remember to add a catch-all pattern in case of program crash.
* Patterns are checked form top to the bottom.

If we writing `lucky 'a'`, it will throw error:

```haskell
ghci> lucky 'a'

<interactive>:21:1: error:
    • No instance for (Integral Char) arising from a use of ‘lucky’
    • In the expression: lucky 'a'
      In an equation for ‘it’: it = lucky 'a'
```

which means that Type `Char` is not a instance of class `Integral`. (haha, something like instance in OOP ~~)

A factorial function implementation:

```haskell
factorial :: (Integral a) => a -> a  
factorial 0 = 1  
factorial n = n * factorial (n - 1)  
```

For that patterns are check from top to bottom, we can't place the second pattern on the top of the first one. Otherwise, this recursive will never end, for that the seconed catchs all numbers !

### usefull pattern

Some useful patterns list as below:

* `[]`: match list
* `(x:[])` / `[x]`: match the first element (It is just a syntactic sugar) 
* `(x:xs)`: match first to `x`, the rest to `xs` . In the case, the return type probably be a List. Otherwise it will raise error. `(_:xs)` matchs non-empty list.

> if the pattern consists of several variables, surround them with parentheses.

An example:

```haskell
tell :: (Show a) => [a] -> String  
tell [] = "The list is empty"  
tell (x:[]) = "The list has one element: " ++ show x  
tell (x:y:[]) = "The list has two elements: " ++ show x ++ " and " ++ show y  
tell (x:y:_) = "This list is long. The first two elements are: " ++ show x ++ " and " ++ show y 
```

Another implement of length function with pattern match and recursive:

```haskell
length' :: (Num b) => [a] -> b  
length' [] = 0  
length' (_:xs) = 1 + length' xs  
```



### as patterns

**Def (as patterns)**: binding a name refers to the whole thing whilst keep the pattern

An example of use:

```haskell
capital :: String -> String  
capital "" = "Empty string, whoops!"  
capital all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x]

-- ghci> capital "Dracula"  
-- "The first letter of Dracula is D"
```



## guards

**Def (guards)**: test if a value satisfies some properties. Something like `switch-case-` in C programming language.

### basic

Just give an axample to see how to use it:

``` haskell
densityTell :: (RealFloat a) => a -> String  
densityTell density  
    | density < 1.2 = "Wow! You're going for a ride in the sky!"  
    | density <= 1000.0 = "Have fun swimming, but watch out for sharks!"  
    | otherwise   = "If it's sink or swim, you're going to sink." 
```

 each guard `|` are followed by an boolean expression, if True, then the function is used, if False, jump to the next guard.

> * `otherwise` is of boolean type, the value is `True`
> * Note that there's no `=` right after the function name and its parameters

### where

`where` in `guards`, which can be used to make calculations less, an example:

``` haskell
densityTell :: (RealFloat a) => a -> a -> String  
densityTell mass volume  
    | density < air = "Wow! You're going for a ride in the sky!"  
    | density <= water = "Have fun swimming, but watch out for sharks!"  
    | otherwise   = "If it's sink or swim, you're going to sink."  
    where density = mass / volume  
          air = 1.2  
          water = 1000.0 
          
-- the variables 'density', 'air', 'water' is only visible 
-- in this function 'densityTell'
```

> align these variables proper in case that Haskell don't know that

Use `where` with **pattern match**, an example:

``` haskell
initials :: String -> String -> String  
initials firstname lastname = [f] ++ ". " ++ [l] ++ "."  
    where (f:_) = firstname  
          (l:_) = lastname  
```

You can also define functions in `where` section.Code as follows:

``` haskell
calcuDensities :: (RealFloat a) => [(a, a)] -> [a]
calcuDensities xs = [density m v | (m, v) <- xs]
    where density m v = m / v
    
-- ghci> calcuDensities [(1, 2), (1, 3), (1, 4)]
-- [0.5,0.3333333333333333,0.25]
```

## let-in

Syntax form: `let <bindings> in <expression>`

An example:

```haskell
cylinder :: (RealFloat a) => a -> a -> a  
cylinder r h = 
    let sideArea = 2 * pi * r * h  
        topArea = pi * r ^2  
    in  sideArea + 2 * topArea 
```

Or use it like `if` statement:

```haskell
4 * (let a = 9 in a + 1) + 2 
```

`Let` with **list comprehension**, then we don't need to define a additional function.

``` haskell
calcDensities :: (RealFloat a) => [(a, a)] -> [a]  
calcDensities xs = [density | (m, v) <- xs, let density = m / v, density < 1.2]
```

> remeber that: Haskell goes from left ro right  after pipe

Binding several variables inline need to separater with semicolon, that's:

``` haskell
(let a = 100; b = 200; c = 300 in a*b*c, let foo="Hey "; bar = "there!" in foo ++ bar)  

-- (6000000,"Hey there!") 
```

> `let-in` is local, whilst `where` is visible in all guards.

## case-of

The styntax is:

``` haskell
case expression of pattern -> result  
                   pattern -> result  
                   pattern -> result  
                   ... 
```

A simple example:

``` haskell
head' :: [a] -> a  
head' xs = case xs of [] -> error "No head for empty lists!"  
                      (x:_) -> x 
```

Pattern matching against something in the middle of an expression example:

``` haskell
describeList :: [a] -> String  
describeList xs = "The list is " ++ what xs  
    where what [] = "empty."  
          what [x] = "a singleton list."  
          what xs = "a longer list." 
```
