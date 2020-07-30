---
title: series_iir ()-Azure Explorateur de données
description: Cet article décrit series_iir () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: e1b863b83e08fae680e1a387ca2fdd2a93d111a8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351435"
---
# <a name="series_iir"></a>series_iir()

Applique un filtre à réponse impulsionnelle infinie sur une série.  

La fonction prend une expression contenant un tableau numérique dynamique comme entrée, et applique un filtre à [réponse impulsionnelle infinie](https://en.wikipedia.org/wiki/Infinite_impulse_response) . En spécifiant les coefficients de filtre, la fonction peut être utilisée :
* pour calculer la somme cumulée de la série
* pour appliquer des opérations de lissage
* pour appliquer différents filtres passe- [haut](https://en.wikipedia.org/wiki/High-pass_filter), [passe-bande](https://en.wikipedia.org/wiki/Band-pass_filter)et passe- [bas](https://en.wikipedia.org/wiki/Low-pass_filter)

La fonction prend comme entrée la colonne qui contient le tableau dynamique et deux tableaux dynamiques statiques des coefficients *a* et *b* du filtre, et applique le filtre sur la colonne. Elle génère une nouvelle colonne de tableau dynamique, qui contient la sortie filtrée.  

## <a name="syntax"></a>Syntaxe

`series_iir(`*x* `,` *b* `,` *a*`)`

## <a name="arguments"></a>Arguments

* *x*: cellule de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md) .
* *b*: expression constante contenant les coefficients numérateur du filtre (stocké en tant que tableau dynamique de valeurs numériques).
* *r*: une expression constante, telle que *b*. Contient les coefficients dénominateurs du filtre.

> [!IMPORTANT]
> Le premier élément de `a` (autrement dit, `a[0]` ) ne doit pas être égal à zéro, afin d’éviter la division par 0. Consultez la [formule ci-dessous](#the-filters-recursive-formula).

## <a name="the-filters-recursive-formula"></a>Formule récursive du filtre

* Imaginez un tableau d’entrée X et coefficient les tableaux a et b de longueurs n_a et n_b respectivement. La fonction de transfert du filtre qui va générer le tableau de sortie Y est définie par :

<div align="center">
Y<sub>i</sub> = a<sub>0</sub><sup>-1</sup>(b<sub>0</sub>x<sub>i</sub> 
 + b<sub>1</sub>x<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>x<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>y<sub>i-1</sub>-a<sub>2</sub>y<sub>i-2</sub> - ...-a<sub>n<sub>a</sub>-1</sub>y<sub>i-n<sub>a</sub>-1</sub>)
</div>

## <a name="example"></a>Exemple

Calculer une somme cumulée. Utilisez le filtre IIR avec les coefficients *a*= [1,-1] et *b*= [1] :  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

Voici comment l’encapsuler dans une fonction :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
