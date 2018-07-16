
Masterclass  Open Data Tbilisi, Géorgia - juillet 2018
========================================================
author: groupeRbase
date: 15/07/2018
autosize: true


       CAS PRATIQUE D'UTILISATION DU LOGICIEL R

Pour cet exercice pratique, nous travaillerons sur une bases de données issue de http://data.un.org/
Il s'agit de la base de données sur l'espérance de vie dans le monde sur 150 ans (1950-2100). 


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

il est possible de créer une nouvelle base de données issues de la base de données NewBDD_Vie qui contient uniquement quelques pays. Nous allons nommée cette nouvelle base, (Af_Esp).


```r
Af_Esp<-(filter(NewBDD_Vie, Pays==c("Guinea", "Cameroon", "Niger", "Burkina Faso", "Senegal", "Madagascar", "Togo", "Chad","Benin")))
```

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

Il est possible ensuite à partir de cette selection, d'effectuer quelques analyses/statistiques descriptives.

```r
summary(Af_Esp)
```

```
     Pays              Année             Esperance    
 Length:29          Length:29          Min.   :35.59  
 Class :character   Class :character   1st Qu.:52.79  
 Mode  :character   Mode  :character   Median :64.07  
                                       Mean   :61.04  
                                       3rd Qu.:71.48  
                                       Max.   :78.79  
```

```r
filter(Af_Esp, Esperance==35.59)
```

```
# A tibble: 1 x 3
  Pays         Année     Esperance
  <chr>        <chr>         <dbl>
1 Burkina Faso 1960-1965      35.6
```

```r
filter(Af_Esp, Esperance==78.79)
```

```
# A tibble: 1 x 3
  Pays    Année     Esperance
  <chr>   <chr>         <dbl>
1 Senegal 2075-2080      78.8
```

La fonction summary permet d'avoir un aperçu de quelques valeurs significatives de la base de données. Il s'agit notamment de la moyenne, de l'espérance de vie maximale, minimale entre autres. 

En utilisant ces valeurs connues, il est ensuite possible grace à la fonction filter de connaitre en quelle année et quel pays avait l'espérance de vie la basse vs celle la plus hausse. 
On va éffectuer un filtre pour retenir les données sur uniquement les périodes (1960-65 et 2010-2015). On créera une nouvelle base de données pour chacune des périodes. 

```r
Période1<-(filter(na.omit(NewBDD_Vie), Année=="1960-1965"))
```

```r
Période3<-(filter(na.omit(NewBDD_Vie), Année=="2015-2020"))
```

```r
Période2<-(filter(na.omit(NewBDD_Vie), Année=="2010-2015"))
```
```r
list(Période1)
```

```
[[1]]
# A tibble: 241 x 3
   Pays                Année     Esperance
   <chr>               <chr>         <dbl>
 1 Afghanistan         1960-1965      33.4
 2 Africa              1960-1965      42.3
 3 Albania             1960-1965      64.8
 4 Algeria             1960-1965      47.3
 5 Angola              1960-1965      34.1
 6 Antigua and Barbuda 1960-1965      63.2
 7 Argentina           1960-1965      65.3
 8 Armenia             1960-1965      67.0
 9 Aruba               1960-1965      66.6
10 Asia                1960-1965      46.5
# ... with 231 more rows
```
```r
list(Période2)
```

```
[[1]]
# A tibble: 241 x 3
   Pays                Année     Esperance
   <chr>               <chr>         <dbl>
 1 Afghanistan         2010-2015      62.2
 2 Africa              2010-2015      60.2
 3 Albania             2010-2015      77.7
 4 Algeria             2010-2015      75.3
 5 Angola              2010-2015      60.2
 6 Antigua and Barbuda 2010-2015      75.8
 7 Argentina           2010-2015      76.0
 8 Armenia             2010-2015      74.0
 9 Aruba               2010-2015      75.4
10 Asia                2010-2015      71.8
# ... with 231 more rows
```
```r
list(Période3)
```

```
[[1]]
# A tibble: 241 x 3
   Pays                Année     Esperance
   <chr>               <chr>         <dbl>
 1 Afghanistan         2015-2020      64.2
 2 Africa              2015-2020      62.4
 3 Albania             2015-2020      78.5
 4 Algeria             2015-2020      76.4
 5 Angola              2015-2020      61.8
 6 Antigua and Barbuda 2015-2020      76.6
 7 Argentina           2015-2020      76.8
 8 Armenia             2015-2020      74.8
 9 Aruba               2015-2020      76.1
10 Asia                2015-2020      72.9
# ... with 231 more rows
```

Bibliographie sommaire
==============================

https://cran.r-project.org/




