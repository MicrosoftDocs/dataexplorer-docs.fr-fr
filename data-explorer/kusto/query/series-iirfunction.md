---
title: series_iir() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_iir() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 96452265e07a8a8b057dc70bec520be6ab298dd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508294"
---
# <a name="series_iir"></a>series_iir()

Applique un filtre De réponse Infinite Impulse sur une série.  

Prend une expression contenant un tableau numérique dynamique comme entrée et applique un filtre [De réponse Infinite Impulse.](https://en.wikipedia.org/wiki/Infinite_impulse_response) En spécifiant les coefficients de filtre, il peut être utilisé, par exemple, pour calculer la somme cumulative de la série, pour appliquer des opérations de lissage, ainsi que divers [filtres à cols élevés,](https://en.wikipedia.org/wiki/High-pass_filter) [à pas de bande](https://en.wikipedia.org/wiki/Band-pass_filter) et à faible [passage.](https://en.wikipedia.org/wiki/Low-pass_filter) La fonction prend comme entrée la colonne contenant le tableau dynamique et deux tableaux dynamiques statiques des coefficients du filtre *a* et *b,* et applique le filtre sur la colonne. Elle génère une nouvelle colonne de tableau dynamique, qui contient la sortie filtrée.  
 

**Syntaxe**

`series_iir(`*x* `,` *b* `,` *a*`)`

**Arguments**

* *x*: Cellule de tableau dynamique qui est un éventail de valeurs numériques, généralement la sortie résultante des opérateurs [de make-series](make-seriesoperator.md) ou [de make_list.](makelist-aggfunction.md)
* *b*: Expression constante contenant les coefficients de chiffres du filtre (stockés comme un tableau dynamique de valeurs numériques).
* *a*: Une expression constante, comme *b*. Contient les coefficients dénominateurs du filtre.

> [!IMPORTANT]
> Le premier `a` élément de (c’est-à-dire `a[0]`) ne doit pas être nul (pour éviter la division par 0; voir la formule ci-dessous).

**En savoir plus sur la formule récursive du filtre**

* Compte tenu d’un tableau d’entrée X et coefficients tableaux a, b de longueurs n_a et n_b respectivement, la fonction de transfert du filtre, générant le tableau de sortie Y, est définie par (voir aussi dans Wikipedia):

<div align="center">
Y<sub>i</sub> - a<sub>0</sub><sup>-1</sup>(b<sub>0</sub>X<sub>i</sub> 
 + b<sub>1</sub>X<sub>i-1</sub> + ... b<sub>n<sub>b</sub>b -1</sub>X<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>Y<sub>i-1</sub>-a<sub>2</sub>Y<sub>i-2</sub> - ... - a<sub>n<sub>a</sub>-1</sub>Y<sub>i-n<sub>a</sub>-1</sub>)
</div>

**Exemple**

Le calcul de la somme cumulative peut être effectué par le filtre iir avec des coefficients *un*[1,-1] et *b*[1] :  

```kusto
let x = range(1.0, 10, 1);
print x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mv-expand x, y
```

| x | y |
|:--|:--|
|1.0|1.0|
|2.0|3.0|
|3.0|6.0|
|4.0|10.0|

Voici comment l’envelopper dans une fonction:

```kusto
let vector_sum=(x:dynamic)
{
  let y=array_length(x) - 1;
  toreal(series_iir(x, dynamic([1]), dynamic([1, -1]))[y])
};
print d=dynamic([0, 1, 2, 3, 4])
| extend dd=vector_sum(d)
```

|d            |jj  |
|-------------|----|
|`[0,1,2,3,4]`|`10`|