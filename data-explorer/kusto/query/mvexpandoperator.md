---
title: opérateur MV-Expand-Azure Explorateur de données
description: Cet article décrit l’opérateur MV-Expand dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: a0d19f3466381bef3848365c2af27109e9699087
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271313"
---
# <a name="mv-expand-operator"></a>mv-expand, opérateur

Développe un tableau à valeurs multiples ou un conteneur de propriétés.

`mv-expand`est appliqué sur une colonne de type [dynamique](./scalar-data-types/dynamic.md), de sorte que chaque valeur de la collection obtient une ligne distincte. Toutes les autres colonnes d’une ligne développée sont dupliquées. 

**Syntaxe**

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [ `with_itemindex=` *IndexColumnName*] *ColumnName* [ `,` *ColumnName* ...] [ `limit` *RowLimit*]

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [*Name* `=` ] *ArrayExpression* [ `to typeof(` *TypeName* `)` ] [, [*Name* `=` ] *ArrayExpression* [ `to typeof(` *TypeName* `)` ]...] [ `limit` *RowLimit*]

**Arguments**

* *ColumnName :* dans le résultat, les tableaux dans la colonne nommée sont développés en plusieurs lignes. 
* *ArrayExpression :* expression produisant un tableau. Si ce formulaire est utilisé, une nouvelle colonne est ajoutée et la colonne existante est conservée.
* *Name :* nom de la nouvelle colonne.
* Nom de la *:* Indique le type sous-jacent des éléments du tableau, qui devient le type de la colonne produite par l’opérateur.
    Notez que les valeurs du tableau qui ne sont pas conformes à ce type ne seront pas converties ; au lieu de cela, ils prennent une `null` valeur.
* *RowLimit :* nombre maximal de lignes générées à partir de chaque ligne d’origine. La valeur par défaut est 2147483647. 
*Remarque*: la forme héritée et obsolète de l’opérateur `mvexpand` a une limite de lignes par défaut de 128.
* *IndexColumnName :* Si `with_itemindex` est spécifié, la sortie inclut une colonne supplémentaire (nommée *IndexColumnName*), qui contient l’index (à partir de 0) de l’élément dans la collection développée d’origine. 

**Retourne**

Plusieurs lignes pour chacune des valeurs dans n’importe quel tableau dans la colonne nommée ou dans l’expression de tableau.
Si plusieurs colonnes ou expressions sont spécifiées, elles sont développées en parallèle. ainsi, pour chaque ligne d’entrée, il y a autant de lignes de sortie qu’il y a d’éléments dans l’expression développée la plus longue (les listes plus courtes sont complétées par des valeurs null). Si la valeur d’une ligne est un tableau vide, la ligne se développe en Nothing (ne s’affiche pas dans le jeu de résultats). Si la valeur d’une ligne n’est pas un tableau, la ligne est conservée telle quelle dans le jeu de résultats. 

La colonne développée est toujours de type dynamique. Utilisez une conversion telle que `todatetime()` ou `tolong()` si vous souhaitez calculer ou agréger des valeurs.

Deux modes de développement de conteneurs de propriétés sont pris en charge :
* `bagexpansion=bag`: les conteneurs de propriétés sont développés en conteneurs de propriétés à entrée unique. Il s’agit du développement par défaut.
* `bagexpansion=array`: Les conteneurs de propriétés sont développés en structures de tableau de valeurs de clé à deux éléments `[` *key* `,` *value* `]` , ce qui permet d’accéder uniformément aux clés et aux valeurs (par exemple, en exécutant une agrégation de comptage distinct sur les noms de propriété). 

**Exemples**

Une simple expansion d’une seule colonne :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"Prop1" : "a"}|
|1|{"Prop2" : "b"}|


Si vous développez deux colonnes, vous devez d’abord « compresser » les colonnes applicables, puis les développer :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|b|c|
|---|---|---|
|1|{"Prop1" : "a"}|5|
|1|{"Prop2" : "b"}||

Si vous souhaitez obtenir un produit cartésien de deux colonnes extensibles, développez l’une après l’autre :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{"Prop1" : "a"}|5|
|1|{"Prop2" : "b"}|5|


Développement d’un tableau avec `with_itemindex` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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


**Autres exemples**

Consultez [nombre de graphiques d’activités dynamiques dans le temps](./samples.md#concurrent-activities).

**Voir aussi**

- [MV-opérateur apply](./mv-applyoperator.md) .
- [résumez make_list ()](makelist-aggfunction.md) qui exécute la fonction opposée.
- le plug-in [bag_unpack ()](bag-unpackplugin.md) permettant de développer des objets JSON dynamiques dans des colonnes à l’aide de clés de conteneur de propriétés.
