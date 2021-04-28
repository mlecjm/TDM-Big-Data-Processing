
# Objectifs

## Travail sur les fonctions Python intégrées

1.  map()
2.  filter()
3.  reduce()

## Multiprocessus

1.  multiprocessing.cpu_count
2.  multiprocessing.Pool
3.  map



#### Exercice 4.1 \[★\] 

Dans cet exercice, nous allons examiner la fonction intégrée de Python
appelée filter() qui peut être utilisée pour sélectionner les éléments
d\'une collection correspondant à une condition particulière.



``` {.python}
# Initialization
num = [i for i in range(1,20)]
print(num)
```



Nous utiliserons la documentation disponible pour les différentes
fonctions. Dans ce but, nous utiliserons un point d\'interrogation (?)
après le nom de la fonction ou d\'une classe comme indiqué ci-dessous.



``` {.python}
filter?
```



La fonction `filter(function, iterable)` prend deux paramètres : une
fonction et un itérable. La fonction agit sur chaque élément d\'un type
de données itérable.



Dans le premier exemple, nous utilisons `None` comme premier paramètre.
Dans ce cas, le filtre agira comme une fonction d\'identité et
retournera l\'itérable.



``` {.python}
# Use of filter function with None as the first parameter
num = [i for i in range(1,20)]
filtered = list(filter(None, num))
print(filtered)
```



Dans l\'exemple suivant, nous allons filtrer les nombres pairs de la
liste d\'entrée. Notez que nous avons écrit une fonction even() qui
renvoie `True` si le nombre d\'entrée est pair, sinon `False`.

`filer()` retournera les éléments de la liste qui ont retourné
`True`lorsqu\'ils ont été passés comme argument à la fonction even().



``` {.python}
def even(item):
    if (item % 2 == 0):
        return True
    return False

num = [i for i in range(1,20)]
filtered = list(filter(even, num))
print(filtered)
```



Dans l\'exemple suivant, nous avons une nouvelle fonction `odd()` qui
renvoie `True` lorsque le nombre en entrée est impair.

Nous utilisons cette nouvelle fonction comme entrée de la fonction
`filter()`.



``` {.python}
def odd(item):
    if (item % 2 == 0):
        return False
    return True

num = [i for i in range(1,20)]
filtered = list(filter(odd, num))
print(filtered)
```



**Question** Écrivez un programme utilisant filter() qui prend une liste
de chaînes de caractères et filtre les palindromes.



#### Exercice 4.2 \[★\] 

Que faire si nous voulons appliquer la même fonction sur plusieurs
éléments d\'une liste.

Prenons l\'exemple suivant : supposons que nous ayons une fonction qui
puisse retourner le carré d\'un nombre. Nous voulons maintenant
appliquer cette fonction à tous les nombres d\'une liste. Nous pouvons
écrire un programme avec une boucle pour y parvenir. Mais nous allons
écrire un programme plus petit pour y parvenir.



$f(x) = x ^ 2$

$g([a,b,...]) = [f(a), f(b), ..]$

$g([a,b,...]) = [a^2, b^2, ..]$



Python fournit une autre fonction intégrée appelée
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



Mais que faire si notre programme prend plusieurs entrées.

L\'exemple suivant montre ce cas. La fonction `product()` prend deux
nombres en entrée et renvoie leur produit.



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



Enfin, nous examinons une autre fonction appelée reduce() qui applique
une fonction de deux arguments de manière cumulative sur les membres de
la liste, de gauche à droite.



$f(x) = x ^ 2$

$g([a,b,...]) = [f(a), f(b), ..]$

$g([a,b,...]) = [a^2, b^2, ..]$

\$h(g(\[a,b,c,..\])) = (((a\^2 + b\^2) + c\^2) + \...) \$



``` {.python}
from functools import reduce

reduce?
```



Dans l\'exemple suivant, nous calculons la somme des membres d\'une
liste.

Nous passons la fonction sum_num() comme premier argument à la fonction
reduce. sum_num() prend deux nombres en entrée et renvoie leur nombre.



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



Dans l\'exemple suivant, nous utilisons la même fonction sum_num(), mais
sur des nombres réels.



``` {.python}
num = [random.uniform(0, i) for i in range(1,20)]
print(num)
sum_value = reduce(sum_num, num)
print(sum_value)
```



Dans l\'exemple suivant, nous utilisons une autre fonction product().



``` {.python}
from functools import reduce

def product(item1, item2):
    return item1 * item2

num = [i for i in range(1,20)]
print(num)
sum_num = reduce(product, num)
print(sum_num)
```



**Question**: Écrivez un programme qui prend une liste de matrices de
taille 2x2 et calcule la somme de toutes les matrices.



#### Exercice 4.3 \[★★\] 

Dans les exemples suivants, nous utilisons des expressions lambda et les
passons comme arguments aux fonctions filter(), map(), et reduce().

Dans l\'exemple suivant, l\'expression lambda `lambda x : x%2` prend x
en entrée et retourne la valeur de `x%2`. Cette approche est similaire à
celle que nous avons vue ci-dessus avec la fonction even().



``` {.python}
num = [i for i in range(1,20)]
filtered = list(filter(lambda x: x%2 == 0, num))
print(filtered)
```



Dans l\'exemple suivant, nous prenons l\'exemple avec la fonction odd()
et le remplaçons par une expression lambda.



``` {.python}
num = [i for i in range(1,20)]
filtered = list(filter(lambda x: x%2 != 0, num))
print(filtered)
```



Dans l\'exemple suivant, nous prenons l\'exemple avec la fonction
square() et le remplaçons par une expression lambda.



``` {.python}
num = [i for i in range(1,20)]
squared = list(map(lambda x: x *2, num))
print(squared)
```



Et si nous voulons passer deux arguments, comme dans l\'exemple
product() ci-dessus.



``` {.python}
num1 = [i for i in range(1,20)]
print(num1)
num2 = [i for i in range(10,20)]
print(num2)
product = list(map(lambda x, y: x *y , num1, num2))
print(product)
```



Dans les exemples suivants, nous utilisons l\'expression lambda avec la
fonction reduce().



``` {.python}
from functools import reduce
import random

num = [i for i in range(1,20)]
print(num)

sum_value = reduce(lambda x,y: x + y, num)
print(sum_value)
```



Comme dans l\'exemple avec sum_num(), nous testons des nombres réels
avec les expressions lambda.



``` {.python}
from functools import reduce
import random

num = [i for i in range(1,20)]
print(num)

sum_value = reduce(lambda x,y: x + y, num)
print(sum_value)
```



Maintenant, nous remplaçons le produit() par une expression lambda.



``` {.python}
from functools import reduce
import random

num = [i for i in range(1,20)]
print(num)

product_value = reduce(lambda x,y: x * y, num)
print(product_value)
```



**Question** Écrivez un programme utilisant map(), reduce() et des
expressions lambda pour compter la longueur totale de toutes les chaînes
de caractères dans une liste.



#### Exercice 4.4 \[★★\] 

Nous voulons utiliser le multitraitement pour calculer les valeurs en
parallèle. Pour ce faire, nous utiliserons le paquet multiprocessing.

D\'abord nous trouvons le nombre de processeurs dans notre machine..



``` {.python}
import multiprocessing as mp
mp.cpu_count?
```



``` {.python}
import multiprocessing as mp
print(mp.cpu_count())
```



Ensuite, nous allons créer un pool de processus pour le calcul et nous
utilisons la méthode Pool().



``` {.python}
import multiprocessing as mp
mp.Pool?
```



Dans l\'exemple suivant, nous créons un pool dont le nombre de processus
est égal au nombre de processeurs de notre machine.

Regardez comment nous transformons notre exemple précédent de map-reduce
dans le contexte du multiprocessus.



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



Dans l\'exemple suivant, nous voulons télécharger un certain nombre de
pages en parallèle. Nous passons la fonction download_page() comme
entrée à la fonction pool.map(). Le but de la fonction est de
télécharger les pages de Wikidata. Vérifiez le résultat du code suivant.

Changez le nombre de processus et testez le résultat.



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



Maintenant, nous voulons analyser les pages téléchargées. Dans
l\'exemple suivant, nous comptons le nombre d\'URLs contenant
\"wikipedia.org\".



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



**Question**: Écrivez un programme qui interroge Wikidata pour obtenir
100 URL d\'images de villes et qui télécharge les images sur votre
machine en utilisant multiprocessing et map(). Le programme doit ensuite
analyser chaque image téléchargée et trouver les deux couleurs
prédominantes de chaque image, toujours en utilisant multiprocessing et
map().

