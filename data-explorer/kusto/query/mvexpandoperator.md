---
title: Opérateur mv-expand – Azure Data Explorer
description: Cet article décrit l’opérateur mv-expand dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.localizationpriority: high
ms.openlocfilehash: d9d245da4acd43eb8d5e6a0eeadfa525cbd407a3
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96303338"
---
# <a name="mv-expand-operator"></a>mv-expand, opérateur

Développe un tableau de valeurs multiples ou un jeu de propriétés.

L’opérateur `mv-expand` est appliqué à un tableau ou à une colonne de jeu de propriétés de type [dynamique](./scalar-data-types/dynamic.md) afin que chaque valeur de la collection obtienne une ligne distincte. Toutes les autres colonnes d’une ligne développée sont dupliquées. 

## <a name="syntax"></a>Syntaxe

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*IndexColumnName*] *ColumnName* [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] ...] [`limit` *Rowlimit*]

## <a name="arguments"></a>Arguments

* *ColumnName :* dans le résultat, les tableaux dans la colonne nommée sont développés en plusieurs lignes. 
* *ArrayExpression :* expression produisant un tableau. Si ce formulaire est utilisé, une nouvelle colonne est ajoutée et la colonne existante est conservée.
* *Name :* nom de la nouvelle colonne.
* *Typename :* Indique le type sous-jacent des éléments du tableau, qui devient le type de la colonne produite par l’opérateur `mv-apply`. L’opération d’application de type est uniquement cast et n’inclut pas d’analyse ou de conversion de type. Les éléments de tableau qui ne sont pas conformes au type déclaré deviennent des valeurs `null`.
* *RowLimit :* nombre maximal de lignes générées à partir de chaque ligne d’origine. La valeur par défaut est 2147483647. 

  > [!NOTE]
  > `mvexpand` est une forme héritée et obsolète de l’opérateur `mv-expand`. La version héritée a une limite de lignes par défaut de 128.

* *IndexColumnName :* Si `with_itemindex` est spécifié, la sortie inclut une colonne supplémentaire (nommée *IndexColumnName*), qui contient l’index (à partir de 0) de l’élément dans la collection développée d’origine. 

## <a name="returns"></a>Retours

Plusieurs lignes pour chacune des valeurs dans n’importe quel tableau, qui se trouvent dans la colonne nommée ou dans l’expression de tableau.
Si plusieurs colonnes ou expressions sont spécifiées, elles sont développées en parallèle. Pour chaque ligne d’entrée, il y aura autant de lignes de sortie qu’il y a d’éléments dans l’expression développée la plus longue (les listes plus courtes sont remplies de valeurs null). Si la valeur d’une ligne est un tableau vide, la ligne ne se développe en rien (ne s’affiche pas dans le jeu de résultats). Toutefois, si la valeur d’une ligne n’est pas un tableau, la ligne est conservée telle quelle dans le jeu de résultats. 

La colonne développée est toujours de type dynamique. Utilisez une conversion telle que `todatetime()` ou `tolong()` si vous souhaitez calculer ou agréger des valeurs.

Deux modes de développement de conteneurs de propriétés sont pris en charge :
* `bagexpansion=bag`: les conteneurs de propriétés sont développés en conteneurs de propriétés à entrée unique. Ce mode est le développement par défaut.
* `bagexpansion=array` : les conteneurs de propriétés sont développés en structures de tableau `[`*clé*`,`*valeur*`]` à deux éléments, permettant un accès uniforme aux clés et valeurs (également, par exemple, exécutant une agrégation de comptage distinct de noms de propriété). 

## <a name="examples"></a>Exemples

### <a name="single-column"></a>Colonne unique

Développement simple d’une colonne unique :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1":"a"}|
|1|{"prop2":"b"}|

### <a name="zipped-two-columns"></a>Deux colonnes zippées

Si vous développez deux colonnes, vous devez d’abord « zipper » les colonnes applicables, puis les développer :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5, 4, 3])]
| mv-expand b, c
```

|a|b|c|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}|4|
|1||3|

### <a name="cartesian-product-of-two-columns"></a>Produit cartésien de deux colonnes

Si vous souhaitez obtenir un produit cartésien du développement de deux colonnes, développez l’une après l’autre :

<!-- csl: https://kuskusdfv3.kusto.windows.net/Kuskus -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)
  [
  1,
  dynamic({"prop1":"a", "prop2":"b"}),
  dynamic([5, 6])
  ]
| mv-expand b
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{  "prop1": "a"}|5|
|1|{  "prop1": "a"}|6|
|1|{  "prop2": "b"}|5|
|1|{  "prop2": "b"}|6|

### <a name="convert-output"></a>Convertir la sortie

Si vous souhaitez forcer la sortie d’un opérateur mv-expand vers un certain type (la valeur par défaut est dynamique), utilisez `to typeof` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:string, b:dynamic, c:dynamic)["Constant", dynamic([1,2,3,4]), dynamic([6,7,8,9])]
| mv-expand b, c to typeof(int)
| getschema 
```

ColumnName|ColumnOrdinal|DateType|ColumnType
-|-|-|-
a|0|System.String|string
b|1|System.Object|dynamique
c|2|System.Int32|int

Notez que la colonne `b` obtenue est `dynamic` alors que la colonne `c` obtenue est `int`.

### <a name="using-with_itemindex"></a>Utilisation de with_itemindex

Développement d’un tableau avec `with_itemindex` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1
| summarize x = make_list(x)
| mv-expand with_itemindex=Index x
```

|x|Index|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|
 
## <a name="see-also"></a>Voir aussi

* Pour d’autres exemples, consultez [Graphique du nombre d’activités en direct au fil du temps](./samples.md#chart-concurrent-sessions-over-time).
* Opérateur [mv-apply](./mv-applyoperator.md).
* [résumer make_list ()](makelist-aggfunction.md), qui est la fonction opposée de l’opérateur mv-expand.
* Plug-in [bag_unpack ()](bag-unpackplugin.md) pour le développement d’objets JSON dynamiques dans des colonnes à l’aide de clés de jeu de propriétés.
