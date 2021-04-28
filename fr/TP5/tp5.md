
# Goals

## Utilisation de pyspark

-   filter, map and reduce
-   Utilisation des fichiers CSV et JSON
-   SQL et pyspark
-   Écriture et lecture de fichiers ORC et Parquet



#### Exercice 5.1 \[★\] {#exercice-51-}

Tout d\'abord, nous allons installer pyspark

`$ pip install pyspark`



Si l\'installation a réussi, le code suivant sera exécuté avec succès.



``` {.python}
from pyspark import SparkConf
from pyspark.context import SparkContext
```



Nous allons utiliser la configuration par défaut pour créer un contexte
spark. Ce contexte spark sera utilisé tout au long de nos exemples.

Tout d\'abord, nous lisons un fichier CSV qui contient deux colonnes :
languageLabel et year.



``` {.python}
sc = SparkContext.getOrCreate(SparkConf())
lines = sc.textFile("../../data/pl.csv")
```



Nous allons utiliser les méthodes map() et reduce() avec les expressions
lambda.

Notez que dans l\'exercice suivant, nous n\'utilisons pas les fonctions
intégrées de Python. Le but de cet exemple est de compter le nombre de
caractères ligne par ligne.



``` {.python}
line_length = lines.map(lambda line: len(line))
total_length = line_length.reduce(lambda a,b: a + b)
print(total_length)
```



Dans cet exemple, nous obtenons le nombre de tokens séparés par des
virgules pour chaque ligne. Enfin, nous calculons le nombre total de
tokens.



``` {.python}
line_token_count = lines.map(lambda line: len(line.split(",")))
total_token_count = line_token_count.reduce(lambda a,b: a + b)
print(total_token_count)
```



Nous modifions légèrement le code et utilisons l\'espace comme
séparateur pour compter le nombre de tokens.



``` {.python}
line_token_count = lines.map(lambda line: len(line.split(" ")))
total_token_count = line_token_count.reduce(lambda a,b: a + b)
print(total_token_count)
```



Jusqu\'à présent, nous n\'avons considéré qu\'un seul fichier à la fois.

Que faire si nous voulons manipuler et analyser plusieurs fichiers CSV?

La méthode textFile() peut également gérer de tels cas.



``` {.python}
sc = SparkContext.getOrCreate(SparkConf())
lines = sc.textFile("../../data/*.csv")

line_length = lines.map(lambda line: len(line))
total_length = line_length.reduce(lambda a,b: a + b)
print(total_length)
```



Notre prochain objectif est de collecter toutes les lignes stockées dans
plusieurs fichiers et d\'y accéder en utilisant une seule variable.

Ceci est possible en utilisant la méthode collect().



``` {.python}
sc = SparkContext.getOrCreate(SparkConf())
lines = sc.textFile("../../data/*.csv")

all_lines = lines.collect()
print(len(all_lines))
for line in all_lines:
    print(line)
```



Comme la fonction intégrée de Python map(), il est également possible de
passer une fonction définie par l\'utilisateur comme entrée de la
fonction map() de pyspark.

Dans l\'exemple suivant, nous comptons le nombre d\'occurrences d\'un
mot particulier dans tous les fichiers CSV.

Dans ce but, nous avons écrit une fonction count_Language() qui compte
le nombre d\'occurrences de \"Language\" dans une ligne.



``` {.python}
import re

sc = SparkContext.getOrCreate(SparkConf())
lines = sc.textFile("../../data/*.csv")

def count_Language(line):
    return len(re.findall("Language", line))

line_length = lines.map(count_Language)
total_length = line_length.reduce(lambda a,b: a + b)
print(total_length)
```



**Question** Téléchargez 50 pages HTML. Écrivez un programme utilisant
Spark pour compter le nombre total de fois où `<div>` ou `<div/>` est
présent dans tous ces fichiers téléchargés.



#### Exercice 5.2 \[★★\] {#exercice-52-}

Notre prochain objectif est de travailler avec des fichiers JSON et de
créer des dataframes pandas.

Cependant, nous aimerions utiliser le support des dataframes fourni par
Spark.

Nous créons d\'abord une session Spark.



``` {.python}
from pyspark.sql import SparkSession

spark = SparkSession.builder.getOrCreate()
```



Nous lisons un fichier JSON et le convertissons en dataframe pandas.

Ce dataframe pandas est ensuite converti en dataframe Spark.



``` {.python}
from pandas import json_normalize
import pandas as pd
import json

data = json.load(open('../../data/pl.json'))
dataframe = json_normalize(data)
dataframe = dataframe.astype(dtype= {
      "languageLabel" : "<U200", "year" : "int64"})

df = spark.createDataFrame(dataframe)
```



Nous affichons maintenant le dataframe Spark.



``` {.python}
df.show()
```



Il est également possible de spécifier le nombre de lignes à afficher.



``` {.python}
df.show(30)
```



Le code suivant affichera le schéma du dataframe.



``` {.python}
df.printSchema()
```



Dans le code suivant, nous écrivons une fonction filtre pour obtenir les
langages de programmation qui ont été publiés en 1952.



``` {.python}
def pandas_filter_func(iterator):
    for pandas_df in iterator:
        yield pandas_df[pandas_df.year == 1952]

df.mapInPandas(pandas_filter_func, schema=df.schema).show()
```



Dans le code suivant, nous appliquons une fonction définie par
l\'utilisateur sur la colonne année pour obtenir le siècle où un langage
de programmation a été publié pour la première fois.



``` {.python}
from pyspark.sql.functions import pandas_udf

@pandas_udf('long')
def century(series: pd.Series) -> pd.Series:
    return (series / 100) + 1

df.select(century(df.year)).show()
```



Comme pour pandas, il est possible de regrouper les données par valeurs
de colonnes spécifiques.

Dans l\'exemple suivant, nous voulons obtenir le nombre de langages de
programmation publiés pour la première fois chaque année.



``` {.python}
df.groupby('year').count().show()
```



Nous avons jusqu\'à présent utilisé des fichiers CSV et JSON.

Mais pour une performance optimisée, les fichiers ORC et parquet sont
suggérés.

Dans les exemples suivants, nous voyons comment nous pouvons écrire des
fichiers ORC et parquet en utilisant un dataframe Spark.



``` {.python}
df.write.orc('languages.orc')
spark.read.orc('languages.orc').show()
```



``` {.python}
df.write.parquet('languages.parquet')
spark.read.parquet('languages.parquet').show()
```



``` {.python}
df.write.csv('languages.csv')
spark.read.csv('languages.csv').show()
```



Avant de continuer, vérifiez votre répertoire actuel et voyez comment
ces dataframes ont été écrits.



**Question** Écrire une requête sur Wikidata pour télécharger les noms
de tous les logiciels ainsi que leur première date de sortie. Écrivez un
programme utilisant Spark pour obtenir le nombre de logiciels sortis
chaque année.



#### Exercice 5.3 \[★★\] {#exercice-53-}

Il est possible de travailler en utilisant le langage SQL sur des
dataframes spark.

Pour cela, nous allons créer des vues temporaires et exécuter des
requêtes SQL.

L\'exemple suivant va retourner et afficher toutes les langues.



``` {.python}
df.createOrReplaceTempView("languages")
spark.sql("SELECT * from languages").show()
```



L\'exemple suivant utilise une requête SQL pour obtenir le nombre de
langages de programmation.



``` {.python}
spark.sql("SELECT count(*) from languages").show()
```



La requête suivante renvoie le nombre de langues publiées pour la
première fois en 1952.



``` {.python}
spark.sql("SELECT count(*) from languages where year=1952").show()
```



La requête suivante est similaire à l\'exemple groupby vu précédemment.



``` {.python}
spark.sql("SELECT year, count(*) from languages Group by year ORDER by year").show(100)
```



**Question** À la place des fonctions d\'agrégation de Spark, utilisez
SQL et Spark pour obtenir le nombre de logiciels sortis chaque année en
utilisant les données téléchargées dans l\'exercice précédent.



For more examples, check [official
document](https://spark.apache.org/docs/latest/api/python/getting_started/quickstart.html)

