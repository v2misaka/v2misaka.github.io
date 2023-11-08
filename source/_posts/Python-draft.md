---
layout:
  - post
title: Python note
date: 2021-12-29 21:29:12
updated: 2021-12-29 21:29:12
categories:
- Programming
- Python
---
Here are my notes on learning Python.  
Hope that helps.

***Continuously updating.***

<!-- more -->
# Primitive Types

## Number


```python
print(10 + 3)
print(10 - 3)
print(10 * 3)
print(10 / 3)
print(10 // 3)
print(10 % 3)
print(10 ** 3)

print(abs(-2.9))
print(round(-2.9))
```

    13
    7
    30
    3.3333333333333335
    3
    1
    1000
    2.9
    -3
    

## String


```python
course = "   Python programming "  # Notice the blank
# Escape characters：\n \" \\
print(len(course))
```

    22
    

### methods for string


```python
print(course.upper())
print(course.lower())
print(course.title())

print(course.strip())
print(course.rstrip()+"a")
print(course.lstrip()+"a")

# notice the difference
print(course.find("pro"))
print(course.find("Pro"))

print("pro" in course)  # Judgment statement
print("sdif" not in course)

print(course.replace("p", "j"))

```

       PYTHON PROGRAMMING 
       python programming 
       Python Programming 
    Python programming
       Python programminga
    Python programming a
    10
    -1
    True
    True
       Python jrogramming 
    

### operations of string


```python
you = "xixi"
me = "xuxu"
you_plus_me = you + me
you_love_me = f"{you} love {me} {500+10*2}"
print(you_plus_me)
print(you_love_me)
```

    xixixuxu
    xixi love xuxu 520
    

## Boolean


```python
print(bool(0))
print(int(False))
```

    False
    0
    

## Input statement


```python
x = input("please input x:")
print("Here I inputed", x ,"as an example")
print(type(x))
# as a result, the type of the input character needs to be converted before it can be operated
print(int(x)+2)
print(type(x))

```

    Here I inputed 222 as an example
    <class 'str'>
    224
    <class 'str'>
    

# Control Flow


## Conditional Statements

### Comparison

comparison operators：> < >= <= == !=


```python
print(ord("B"))  # show the ASCII value
```

    66
    

#### Comparison Statements


```python
a = 2
b = 18
if a == b:
    print("a = b")
if a < b:
    print("a < b")
```

    a < b
    


```python
# a more simpler statement
age = 25
message = "Eligible" if age > 18 else "Not Eligible"
print(message)
if 18 <= age < 65: 
    print("Eligible")
```

    Eligible
    Eligible
    

##### compare: 'if' ; 'elif'


```python
a = 2
b = 18
if a == b:
    print("a = b")
if a < b:
    print("a < b")
elif (b-a) > 5:
    print("a<<b")  # 'elif' won't work when former 'if' works.
if (b-a) > 5:
    print("a<<b")  # 'if' will work nomatter former 'if' works or not.
else:
    print("a > b")
print("done")

```

    a < b
    a<<b
    done
    

### Logical

Logical operators：and； or； not


```python
high_income = False
good_credit = True

if high_income and good_credit:  # 与
    print("Eligible")
else:
    print("Not Eligible")
if high_income or good_credit:  # 或
    print("Eligible")
else:
    print("Not Eligible")
if (not high_income) and good_credit:  # 非
    print("Eligible")
else:
    print("Not Eligible")
```

    Not Eligible
    Eligible
    Eligible
    

## Loop Statements

### for


```python
for number in range(3):
    print("number =", number)
print(number)
for number in range(1, 4):
    print("number =", number)
for number in range(1, 10, 2):
    print("number =", number)
```

    number = 0
    number = 1
    number = 2
    2
    number = 1
    number = 2
    number = 3
    number = 1
    number = 3
    number = 5
    number = 7
    number = 9
    

#### for...else


```python
for number in range(1, 5):
    print("number =", number)
    if number == 3:
        print("Fail")
        break  # to jump out of the loop
else:
    print("Success")  # only when 'for' completed without jump out

for number in range(1, 5):
    print("number =", number)
else:
    print("Success")
```

    number = 1
    number = 2
    number = 3
    Fail
    number = 1
    number = 2
    number = 3
    number = 4
    Success
    

### Iterable


```python
print(type(range(3)))  # range
for x in range(3):
    print(x)

print(type("I love xixi"))  # string
for x in "I love xixi":
    print(x)

print(type([1, 2, 3, 145]))  # list
for x in [1, 2, 3, 145]:
    print(x)

```

    <class 'range'>
    0
    1
    2
    <class 'str'>
    I
     
    l
    o
    v
    e
     
    x
    i
    x
    i
    <class 'list'>
    1
    2
    3
    145
    

### while


```python
number = 100
while number > 0:
    print(number)
    number //= 2
```

    100
    50
    25
    12
    6
    3
    1
    

# Functions

## Definition


```python
def greet(first_name="Xixi", last_name="Zhou"):  # you can set default values for formal parameters.
    print(f"Hi {last_name} {first_name}")  # Variables in the definition of a function are all local.


# 2 empty lines after difinition
```

## Calling


```python
greet()  # the parameter will use the default value
greet("Shuran", "Xu")
greet(first_name="Shuran", last_name="Xu")
```

    Hi Zhou Xixi
    Hi Xu Shuran
    Hi Xu Shuran
    

## Types of functions

### 1- Perform a task:


```python
def task():
    print("I performed a task!!")


task()
```

    I performed a task!!
    

### 2- Return a value


```python
def value():
    return f"I returned a value!!"


message = value()
print(message)

print(task())
print(value())
```

    I returned a value!!
    I performed a task!!
    None
    I returned a value!!
    
