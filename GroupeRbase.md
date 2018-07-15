
Masterclass  Open Data Tbilisi, Géorgia - juillet 2018 
<https://github.com/FofB/Premier-Projet/blob/master/image.png>
========================================================
author: groupeRbase
date: 15/07/2018
autosize: true


P
==================================================

CAS PRATIQUE D'UTILISATION DU LOGICIEL R
========================================================

For more details on authoring R presentations please visit <https://support.rstudio.com/hc/en-us/articles/200486468>.

- Introduction au logiciel R
- Exemple/Exercice sur un cas pratique

Pour cet exercice pratique, nous travaillerons sur une bases de données issue de <http://data.un.org/Data.aspx?q=life+expectancy&d=PopDiv&f=variableID%3a68>
Il s'agit de la base de données sur l'espérance de vie dans le monde sur 50 ans (1955-20100)

========================================================

Le principe de R ce sont les librairies qui permettent de faire plusieurs manipulations. 
Dans notre cas, nous allons utiliser quelques fonctions de la librairie tidyvers. Pour ce faire, nous allons charger au préalable, cette librarie. Cette librairie chargent plusieurs fonctions utiles dont ggplot et readr.


```r
library(tidyverse)
```

Après avoir charger la librairie tidyverse, nous allons charger notre base de données. Les instructions pour le chargement de la base de données sont indiquées ci-dessous. 


```r
library(readr)
UNdata_Export_20180715_035215106 <- read_csv("UNdata_Export_20180715_035215106.csv")

View(UNdata_Export_20180715_035215106)
```


Etape: Renommage de la Base de données
========================================================

Le nom de la base de données chargé étant assez complexe et pas facile à manipuler, nous allons rénommer la BDD afin de facilier les étapes ultérieures de la manipulation. Pour cela nous allons assigner à la BDD un nouveau nom (BD_Vie)


```r
BDD_Vie <- read_csv("UNdata_Export_20180715_035215106.csv")
```


Etape: On utilise glimpse pour avoir une idée de comment est organisée notre BDD
==========================================================

```r
glimpse(BDD_Vie)
```

```
Observations: 8,190
Variables: 4
$ `Country or Area` <chr> "Afghanistan", "Afghanistan", "Afghanistan",...
$ `Year(s)`         <chr> "2095-2100", "2090-2095", "2085-2090", "2080...
$ Variant           <chr> "Medium", "Medium", "Medium", "Medium", "Med...
$ Value             <dbl> 77.66, 76.96, 76.30, 75.64, 74.98, 74.35, 73...
```


Etape: Changement de nom des têtières
=====

Les têtières de notre base de données contient certains caractères qui ne facilitent pas les manipulations. Aussi pour simplifier les têtières, nous allons utiliser la fonction "names" qui permet de changer les noms des têtières. 

```r
names(BDD_Vie)<-c("Pays", "Année", "Variant", "Esperance")
```

Etape: Suppréssion d'une variable et création d'une nouvelle base de données
==========

Pour nos manipulations la variable "variant" ne nous intéresse pas. Nous allons donc l'enlever en utilisant la fonction (subset). Le résultat de cette manipulation sera stocker dans une nouvelle variable que nous appelerons (NewBDD_Vie). 

```r
NewBDD_Vie<- subset(BDD_Vie, select = -3)
```

Etape: Statistiques/Analyses basiques
==============
une fois notre BDD nettoyée et organisée selon notre convenance, on peut lui appliquer les analyses souhaitées. Il peut être intéressant de voir par exemple l'espérance de vie minimale et celle maximale. La fonction summary offre pour cela, d'excellents résultats. 

```r
summary(BDD_Vie$Esperance)
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
  14.49   65.36   73.97   71.69   81.24   94.50     960 
```

================

On constate dans les résultats, une valeur 960 correspondant à NA (Not Available). Cela signifie qu'il existe des pays dont l'espérance de vie n'est pas renseignée pour certaines périodes. l'idéal est de rétirer ces valeurs avant d'opérer les calculs. Pour cela, on utilise la fonction na.omit

```r
summary(na.omit(BDD_Vie$Esperance))
```

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  14.49   65.36   73.97   71.69   81.24   94.50 
```
Nous avons retirer ces valeurs NA, le resultat ainsi obtenu reflette mieux la réalité. l'espérance de vie minimum est de 14.59 ans et celle maximum est 94.5 ans. 
Etape: Et la suite???

==================================
nous connaissons l'espérance de vie minimale et celle maximale, ainsi que celle moyenne. Cependant nous ne savons ni l'année, ni le ou les pays . L'étape présente va indiquer ces données. il existe plusieurs fonctions pour arriver à ce résultat, mais nous utiliserons ici la fonction filter. 

```r
filter(na.omit(NewBDD_Vie), Esperance==14.49)
```

```
# A tibble: 1 x 3
  Pays     Année     Esperance
  <chr>    <chr>         <dbl>
1 Cambodia 1975-1980      14.5
```

```r
filter(na.omit(NewBDD_Vie), Esperance==94.5)
```

```
# A tibble: 1 x 3
  Pays            Année     Esperance
  <chr>           <chr>         <dbl>
1 China Macao SAR 2095-2100      94.5
```
==========================================
il est possible de créer une nouvelle base de données issues de la base de données NewBDD_Vie qui contient uniquement quelques pays. Nous allons nommée cette nouvelle base, (Af_Esp).


```r
Af_Esp<-(filter(NewBDD_Vie, Pays==c("Guinea", "Cameroon", "Niger", "Burkina Faso", "Senegal", "Madagascar", "Togo", "Chad","Benin")))
```

================================

```r
list(Af_Esp)
```

```
[[1]]
# A tibble: 29 x 3
   Pays         Année     Esperance
   <chr>        <chr>         <dbl>
 1 Benin        2055-2060      67.8
 2 Benin        2010-2015      59.9
 3 Benin        1965-1970      40.9
 4 Burkina Faso 2095-2100      76.3
 5 Burkina Faso 2050-2055      70.2
 6 Burkina Faso 2005-2010      55.3
 7 Burkina Faso 1960-1965      35.6
 8 Cameroon     2075-2080      76.4
 9 Cameroon     2030-2035      65.3
10 Cameroon     1985-1990      52.8
# ... with 19 more rows
```




