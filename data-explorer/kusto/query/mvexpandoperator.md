---
title: mv-expand operator - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur mv-expand dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: 01a4b56155d1c29727670b4e827cb7b9968ef7a9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512289"
---
# <a name="mv-expand-operator"></a>mv-expand, opérateur

Élargit le tableau multi-valeur ou le sac de propriété.

`mv-expand`est appliqué sur une colonne de type [dynamique](./scalar-data-types/dynamic.md)de sorte que chaque valeur de la collection obtient une ligne distincte. Toutes les autres colonnes d’une ligne développée sont dupliquées. 

**Syntaxe**

*T* `| mv-expand ` `bagexpansion=`[`bag` | `array`(`with_itemindex=`)] [*IndexColumnName*] *ColumnName* [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` `bagexpansion=`[`bag` | `array`( )] [*Nom* `=``to typeof(`] *ArrayExpression* [*Typename*`)`]`to typeof(`[, [*Nom* `=`] *ArrayExpression* [*Typename*`)`] ...] [`limit` *Rowlimit*]

**Arguments**

* *ColumnName :* dans le résultat, les tableaux dans la colonne nommée sont développés en plusieurs lignes. 
* *ArrayExpression :* expression produisant un tableau. Si ce formulaire est utilisé, une nouvelle colonne est ajoutée et la colonne existante est conservée.
* *Name :* nom de la nouvelle colonne.
* *Nom de type:* Indique le type sous-jacent des éléments du tableau, qui devient le type de colonne produite par l’opérateur.
    Notez que les valeurs du tableau qui ne sont pas conformes à ce type ne seront pas converties; plutôt, ils vont `null` prendre une valeur.
* *RowLimit :* nombre maximal de lignes générées à partir de chaque ligne d’origine. La valeur par défaut est 2147483647. 
*Remarque*: L’héritage et `mvexpand` la forme obsolète de l’opérateur a une limite de ligne par défaut de 128.
* *IndexColumnName:* Si `with_itemindex` elle est spécifiée, la sortie comprendra une colonne supplémentaire (nommée *IndexColumnName*), qui contient l’index (à partir de 0) de l’élément de la collection élargie originale. 

**Retourne**

Plusieurs lignes pour chacune des valeurs dans n’importe quel tableau dans la colonne nommée ou dans l’expression de tableau.
Si plusieurs colonnes ou expressions sont spécifiées, elles sont étendues en parallèle, donc pour chaque ligne d’entrée, il y aura autant de lignes de sortie qu’il y a d’éléments dans l’expression élargie la plus longue (les listes plus courtes sont rembourrées avec des nulls). Si la valeur d’une rangée est un tableau vide, la ligne s’étend à rien (ne s’affichera pas dans l’ensemble de résultat). Si la valeur d’une rangée n’est pas un tableau, la ligne est conservée comme c’est le cas dans l’ensemble de résultats. 

La colonne développée est toujours de type dynamique. Utilisez une conversion telle que `todatetime()` ou `tolong()` si vous souhaitez calculer ou agréger des valeurs.

Deux modes de développement de conteneurs de propriétés sont pris en charge :
* `bagexpansion=bag`: les conteneurs de propriétés sont développés en conteneurs de propriétés à entrée unique. Il s’agit du développement par défaut.
* `bagexpansion=array`: Les sacs de propriété `[`sont élargis en structures de tableau de*valeur* `]` *clés*`,`à deux éléments, permettant un accès uniforme aux clés et aux valeurs (ainsi que, par exemple, l’exécution d’une agrégation distincte sur les noms de propriété). 

**Exemples**

Une simple expansion d’une seule colonne :
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|"prop1":"a"|
|1|"prop2":"b"|


L’expansion de deux colonnes va d’abord «zip» les colonnes applicables, puis les étendre:

```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|b|c|
|---|---|---|
|1|"prop1":"a"|5|
|1|"prop2":"b"||

Si vous voulez obtenir un produit cartésien d’expansion de deux colonnes, étendre l’une après l’autre:
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|"prop1":"a"|5|
|1|"prop2":"b"|5|


Expansion d’un `with_itemindex`tableau avec :
```kusto
range x from 1 to 4 step 1 
| summarize x = make_list(x) 
| mv-expand with_itemindex=Index  x 
```

|x|Index|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|


**Plus d’exemples**

Voir [le nombre de graphiques des activités en direct au fil du temps](./samples.md#concurrent-activities).

**Voir aussi**

- [opérateur mv-appliquer.](./mv-applyoperator.md)
- [résumer make_list()](makelist-aggfunction.md) qui remplit la fonction opposée.
- [bag_unpack()](bag-unpackplugin.md) plugin pour l’expansion des objets dynamiques JSON dans les colonnes à l’aide de clés de sac de propriété.