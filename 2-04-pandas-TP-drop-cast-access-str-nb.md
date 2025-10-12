---
jupytext:
  cell_metadata_json: true
  encoding: '# -*- coding: utf-8 -*-'
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.17.3
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# TP on the moon

+++

**Notions intervenant dans ce TP**

* suppression de colonnes avec `drop` sur une `DataFrame`
* suppression de colonne entièrement vide avec `dropna` sur une `DataFrame`
* accès aux informations sur la dataframe avec `info`
* valeur contenues dans une `Series` avec `unique` et `value_counts` 
* conversion d'une colonne en type numérique avec `to_numeric` et `astype` 
* accès et modification des chaînes de caractères contenues dans une colonne avec l'accesseur `str` des `Series`
* génération de la liste Python des valeurs d'une série avec `tolist`
   
**N'oubliez pas d'utiliser le help en cas de problème.**

**Répartissez votre code sur plusieurs cellules**

+++

1. importez les librairies `pandas` et `numpy`

```{code-cell} ipython3
# votre code
import pandas as pd
import numpy as np
```

2. 1. lisez le fichier de données `data/objects-on-the-moon.csv`
   2.  affichez sa taille et regardez quelques premières lignes

```{code-cell} ipython3
# votre code
df = pd.read_csv('data/objects-on-the-moon.csv')
df.shape
df.head(3)
```

3. 1. vous remarquez une première colonne franchement inutile  
     utiliser la méthode `drop` des dataframes pour supprimer cette colonne de votre dataframe  
     `pd.DataFrame.drop?` pour obtenir de l'aide

```{code-cell} ipython3
# votre code
df=df.drop('Unnamed: 0', axis=1)
```

4. 1. appelez la méthode `info` des dataframes (`non-null` signifie `non-nan` i.e. non manquant)
   1. remarquez une colonne entièrement vide

```{code-cell} ipython3
# votre code
df.info
```

5. 1. utilisez la méthode `dropna` des dataframes pour supprimer *en place* les colonnes qui ont toutes leurs valeurs manquantes  
     (on s'interdit un code qui ferait explicitement référence à la colonne `'Size'`)
   2. vérifiez que vous avez bien enlevé la colonne `'Size'`

```{code-cell} ipython3
# votre code:
df=df.dropna(how='all',axis=1)
df
```

6. 1. affichez la ligne d'`index` $88$, que remarquez-vous ?
   2. utilisez la méthode `dropna` des dataframes pour supprimer
      *en place* les lignes qui ont toutes leurs valeurs manquantes
      (et de nouveau sans faire référence à une ligne en particulier)

```{code-cell} ipython3
# votre code
df.loc[88]
df=df.dropna(how='all')
```

7. 1. utilisez l'attribut `dtypes` des dataframes pour voir le type de vos colonnes
   2. que remarquez vous sur la colonne des masses ?

```{code-cell} ipython3
# votre code
df.dtypes #masse pas un float ou int
```

8. 1. utilisez la méthode `unique` des `Series`pour en regarder le contenu de la colonne des masses
   2. que remarquez vous ?

```{code-cell} ipython3
# votre code
df['Mass (lb)'].unique() #certaines masses sont données en fonction valeur min ou max et pas entier
```

9. 1. conservez la colonne `'Mass (lb)'` d'origine  
      (par exemple dans une colonne de nom `'Mass (lb) orig'`)  
   1. utilisez la fonction `pd.to_numeric` pour convertir  la colonne `'Mass (lb)'` en numérique  
      en remplaçant les valeurs invalides par la valeur manquante (NaN)
   1. naturellement vous vérifiez votre travail en affichant le type de la série `df['Mass (lb)']`
   1. combien y a-t-il de données manquantes dans cette colonne ?

```{code-cell} ipython3
# votre code
df['Mass (lb) orig']=df['Mass (lb)'].copy()
df['Mass (lb)']=pd.to_numeric(df['Mass (lb)'], errors='coerce')
df['Mass (lb)'].iloc[-7]
manquante=df['Mass (lb)'].isna()
manquante.sum()
```

10. 1. cette solution ne vous satisfait pas, vous ne voulez perdre aucune valeur  
       (même au prix de valeurs approchées)  
    1. vous décidez vaillamment de modifier les `str` en leur enlevant les caractères `<` et `>`  
       afin de pouvoir en faire des entiers
    - *hint:*  
       les `pandas.Series` formées de chaînes de caractères sont du type `pandas` `object`  
       mais elle possèdent un accesseur `str` qui permet de leur appliquer les méthodes python des `str`  
       (comme par exemple `replace`)
        ```python
        df['Mass (lb) orig'].str
        ```
        remplacer les `<` et les `>` par des '' (chaîne vide)
     3. utilisez la méthode `astype` des `Series` pour la convertir finalement en `int`

```{code-cell} ipython3
# votre code
df['Mass (lb) orig']=df['Mass (lb) orig'].str.replace(">","")
df['Mass (lb) orig']=df['Mass (lb) orig'].str.replace("<","")
df['Mass (lb) orig'].unique()
df['Mass (lb) orig']=df['Mass (lb) orig'].astype('int64')
```

```{code-cell} ipython3
df.dtypes
```

11. 1. sachant `1 kg = 2.205 lb`  
   créez une nouvelle colonne `'Mass (kg)'` en convertissant les lb en kg  
   arrondissez les flottants en entiers en utilisant `astype`

```{code-cell} ipython3
# votre code
df['Mass (kg)']=df['Mass (lb) orig']/2.205
df['Mass (kg)']=df['Mass (kg)'].astype('int64')
df['Mass (kg)']
```

12. 1. Quels sont les pays qui ont laissé des objets sur la lune ?
    2. Combien en ont-ils laissé en pourcentage (pas en nombre) ?  
     *hint:* regardez les paramètres de `value_counts`

```{code-cell} ipython3

```

```{code-cell} ipython3
# votre code
laisse=df[df['Status']=='Intentionally crashed']
laisse=laisse['Country']
laisse.unique()
laisse.value_counts()
United_States = laisse.value_counts()['United States']/len(df[df['Country']=='United States'])
Soviet_Union = laisse.value_counts()['Soviet Union']/len(df[df['Country']=='Soviet Union'])
Japan = laisse.value_counts()['Japan']/len(df[df['Country']=='Japan'])
China = laisse.value_counts()['European Space Agency']/len(df[df['Country']=='European Space Agency'])
European_Space_Agency = laisse.value_counts()['China']/len(df[df['Country']=='China'])
India = laisse.value_counts()['India']/len(df[df['Country']=='India'])
United_States, Soviet_Union, Japan, China, European_Space_Agency, India
```

13. 1. quel est le poids total des objets sur la lune en kg ?
    2. quel est le poids total des objets laissés par les `United States`  ?

```{code-cell} ipython3
# votre code
poidstot=df['Mass (kg)'].sum()
US=df[df['Country']=='United States']
poidsUS=US['Mass (kg)'].sum()
poidstot, poidsUS
```

14. 1. quel pays a laissé l'objet le plus léger ?  

````{admonition} tip
:class: dropdown tip

voyez les méthodes `Series.idxmin()` et `Series.argmin()`
````

```{code-cell} ipython3
# votre code
df['Mass (kg)'].argmin()
df.iloc[df['Mass (kg)'].argmin()]['Country']
```

15. 1. y-a-t-il un Memorial sur la lune ?  
     *hint:*  
     en utilisant l'accesseur `str` de la colonne `'Artificial object'`  
     regardez si une des descriptions contient le terme `'Memorial'`
    2. quel est le pays qui a mis ce mémorial ?

```{code-cell} ipython3
# votre code
df['Artificial object'].str=='Memorial'
```

16. 1. faites la liste Python des objets sur la lune  
     *hint:* voyez la méthode `tolist()` des séries

```{code-cell} ipython3
# votre code
df['Artificial object'].tolist()
```

***
