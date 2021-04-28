
# Goals

## Work on builtin Python functions

1.  map()
2.  filter()
3.  reduce()

## Multiprocessing

1.  multiprocessing.cpu_count
2.  multiprocessing.Pool
3.  map



#### Exercice 4.1 \[★\] {#exercice-41-}

In this exercise, we will take a look at the Python builtin function
called filter() which can be used to select items from a collection
matching a particular condition.



``` {.python}
# Initialization
num = [i for i in range(1,20)]
print(num)
```



We will make use of the available documentation for the different
functions. For this purpose, we will use a question mark (?) after the
name of the function or a class as shown below.



``` {.python}
filter?
```



The function `filter(function, iterable)` takes two parameters: a
function and an iterable. The function acts on each element of an
iterable data type.



In the first example, we use `None`as the first parameter. In this case,
filter will act as an identity function and returns the iterable.



``` {.python}
# Use of filter function with None as the first parameter
num = [i for i in range(1,20)]
filtered = list(filter(None, num))
print(filtered)
```



In the next example, we will filter out the even numbers from the input
list. Note that we have written a function even() which returns True if
the input number is even, else False.

filter will return the items from the list which returned True when
passed as argument to the function even().



``` {.python}
def even(item):
    if (item % 2 == 0):
        return True
    return False

num = [i for i in range(1,20)]
filtered = list(filter(even, num))
print(filtered)
```



In the following example, we have a new function odd() which returns
True when the input number is odd.

We use this new function as input to the filter() function.



``` {.python}
def odd(item):
    if (item % 2 == 0):
        return False
    return True

num = [i for i in range(1,20)]
filtered = list(filter(odd, num))
print(filtered)
```



**Question** Write a program using filter() that takes a list of strings
and filters out the palindromes.



#### Exercice 4.2 \[★\] {#exercice-42-}

What if we want to apply the same function on multiple elements in a
list.

Take for example, let\'s assume that we have a function that can return
the square of a number. Now we want to apply this to all the numbers in
a list. We can write a program with a loop to achieve this. But, we are
going to write a smaller program to achieve this.



$f(x) = x ^ 2$

$g([a,b,...]) = [f(a), f(b), ..]$

$g([a,b,...]) = [a^2, b^2, ..]$



Python provides another builtin function called
`map(function, iterable, ...)`.



``` {.python}
map?
```



``` {.python}
def square(item):
    return item * item

num = [i for i in range(1,20)]
squared = list(map(square, num))
print(filtered)
```



But what if our program takes multiple inputs.

The following example shows this cases. The function product() takes two
numbers as input and returns their product.



``` {.python}
def product(item1, item2):
    return item1 * item2

num1 = [i for i in range(1,20)]
print(num1)
num2 = [i for i in range(10,20)]
print(num2)
product_value = list(map(product, num1, num2))
print(filtered)
```



Finally, we look at another function called reduce() that applies a
function of two arguments cumulatively on the members of the list, from
left to right.



$f(x) = x ^ 2$

$g([a,b,...]) = [f(a), f(b), ..]$

$g([a,b,...]) = [a^2, b^2, ..]$

\$h(g(\[a,b,c,..\])) = (((a\^2 + b\^2) + c\^2) + \...) \$



``` {.python}
from functools import reduce

reduce?
```



In the following example, we calculate the sum of members of a list.

We pass the function sum_num() as the first argument to the reduce
function. sum_num() takes two numbers as input and returns their number.



``` {.python}
from functools import reduce
import random

def sum_num(item1, item2):
    return item1 + item2

num = [i for i in range(1,20)]
print(num)

sum_value = reduce(sum_num, num)
print(sum_value)
```



In the next example, we make use of the same function sum_num(), but on
real numbers.



``` {.python}
num = [random.uniform(0, i) for i in range(1,20)]
print(num)
sum_value = reduce(sum_num, num)
print(sum_value)
```



In the next example, we use another function product().



``` {.python}
from functools import reduce

def product(item1, item2):
    return item1 * item2

num = [i for i in range(1,20)]
print(num)
sum_num = reduce(product, num)
print(sum_num)
```



**Question**: Write a program that takes a list of matrices of size 2x2
and computes the sum of all matrices.



#### Exercice 4.3 \[★★\] {#exercice-43-}

In the following examples, we use lambda expressions and pass them as
arguments to the functions filter(), map(), and reduce().

In the following example the lambda expression `lambda x: x%2` takes x
as input and returns the value for `x%2`. This is similar to the
approach we saw above with the function even().



``` {.python}
num = [i for i in range(1,20)]
filtered = list(filter(lambda x: x%2 == 0, num))
print(filtered)
```



In the following example, we take the example with the function odd()
and replace it by a lambda expression.



``` {.python}
num = [i for i in range(1,20)]
filtered = list(filter(lambda x: x%2 != 0, num))
print(filtered)
```



In the following example, we take the example with the function square()
and replace it by a lambda expression.



``` {.python}
num = [i for i in range(1,20)]
squared = list(map(lambda x: x *2, num))
print(squared)
```



What if we want to pass two arguments, like in the example product()
above.



``` {.python}
num1 = [i for i in range(1,20)]
print(num1)
num2 = [i for i in range(10,20)]
print(num2)
product = list(map(lambda x, y: x *y , num1, num2))
print(product)
```



In the following examples, we use lambda expression with the reduce()
function.



``` {.python}
from functools import reduce
import random

num = [i for i in range(1,20)]
print(num)

sum_value = reduce(lambda x,y: x + y, num)
print(sum_value)
```



Like in the example with sum_num(), we test real numbers with the lambda
expressions.



``` {.python}
from functools import reduce
import random

num = [i for i in range(1,20)]
print(num)

sum_value = reduce(lambda x,y: x + y, num)
print(sum_value)
```



Now we replace the product() with a lambda expression.



``` {.python}
from functools import reduce
import random

num = [i for i in range(1,20)]
print(num)

product_value = reduce(lambda x,y: x * y, num)
print(product_value)
```



**Question** Write a program using map(), reduce() and lambda
expressions to count the total length of all strings in a list.



#### Exercice 4.4 \[★★\] {#exercice-44-}

Next, we want to use multiprocessing to compute the values in parallel.
For this purpose we will use multiprocessing package.

First we find the number of processors in our machine.



``` {.python}
import multiprocessing as mp
mp.cpu_count?
```



``` {.python}
import multiprocessing as mp
print(mp.cpu_count())
```



Next, we will create a pool of processes for the calculation and we make
use of the Pool() method.



``` {.python}
import multiprocessing as mp
mp.Pool?
```



In the following example, we create a pool with the number of processes
equal to the number of processors in our machine.

Take a look how we tranform our previous example of map-reduce in the
mulitprocessing context.



``` {.python}
from functools import reduce
import multiprocessing as mp

cpu_count = mp.cpu_count()

def squared(x):
    return x * x

num = [i for i in range(1,20)]
with mp.Pool(processes=cpu_count) as pool:
    list_squared = pool.map(squared, num)
    print(list_squared)
    product_value = reduce(lambda x,y: x * y, list_squared)
    print(product_value)
```



In the following example, we want to download a number of pages in
parallel. We pass the download_page() as an input to the pool.map()
function. The goal of the function is to download Wikidata pages. Check
the output of the following code.

Change the number of processes and test the output.



``` {.python}
import requests

def download_page(item):
    r = requests.get('https://www.wikidata.org/wiki/Special:EntityData/' + item + '.json')
    # success
    if r.status_code == 200:
        with open(item + ".json", "w") as w:
            w.write(str(r.json()))
        w.close()
    return r.status_code

process_count = 2
pages = ["Q1", "Q2", "Q3", "Q4", "Q5", "Q6"]
with mp.Pool(processes=process_count) as pool:
    status = pool.map(download_page, pages)
    print(status)
```



Now, we want to analyse the downloaded pages. In the following example,
we count the number of URLs containing \"wikipedia.org\".



``` {.python}
import os

def analyse_file(filename):
    with open(filename, "r") as w:
        data = w.read()
        tokens = data.split(",")
        urls = list(filter(lambda w: "wikipedia.org" in w, tokens))
        return len(urls)
    return 0

files = os.listdir(".")
json_files = list(filter(lambda f: ".json" in f, files))

with mp.Pool(processes=cpu_count) as pool:
    counts = pool.map(analyse_file, json_files)
    print(counts)
    total_count = reduce(lambda x,y: x + y, counts)
    print(total_count)
```



**Question**: Write a program that queries Wikidata to obtain 100 image
URLs of cities and downloads the images to your machine using
multiprocessing and map(). The program must then analyse every
downloaded image and find two predominant colours of each image, again
using multiprocessing and map().

