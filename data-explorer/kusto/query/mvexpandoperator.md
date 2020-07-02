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
ms.openlocfilehash: ee9c4b236344e21bbbc1da68b76710b15b519baa
ms.sourcegitcommit: 56bb7b69654900ed63310ac9537ae08b72bf7209
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85814209"
---
# <a name="mv-expand-operator"></a>mv-expand, opérateur

Développe un tableau à valeurs multiples ou un conteneur de propriétés.

`mv-expand`est appliqué sur un tableau de type [dynamique](./scalar-data-types/dynamic.md)ou une colonne de jeu de propriétés afin que chaque valeur de la collection obtient une ligne distincte. Toutes les autres colonnes d’une ligne développée sont dupliquées. 

**Syntaxe**

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [ `with_itemindex=` *IndexColumnName*] *ColumnName* [ `,` *ColumnName* ...] [ `limit` *RowLimit*]

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [*Name* `=` ] *ArrayExpression* [ `to typeof(` *TypeName* `)` ] [, [*Name* `=` ] *ArrayExpression* [ `to typeof(` *TypeName* `)` ]...] [ `limit` *RowLimit*]

**Arguments**

* *ColumnName :* dans le résultat, les tableaux dans la colonne nommée sont développés en plusieurs lignes. 
* *ArrayExpression :* expression produisant un tableau. Si ce formulaire est utilisé, une nouvelle colonne est ajoutée et la colonne existante est conservée.
* *Name :* nom de la nouvelle colonne.
* Nom de la *:* Indique le type sous-jacent des éléments du tableau, qui devient le type de la colonne produite par l’opérateur. Les valeurs non conformes dans le tableau ne sont pas converties. Au lieu de cela, ces valeurs prendront une `null` valeur.
* *RowLimit :* nombre maximal de lignes générées à partir de chaque ligne d’origine. La valeur par défaut est 2147483647. 

  > [!Note]
  > La forme héritée et obsolète de l’opérateur `mvexpand` a une limite de ligne par défaut de 128.

* *IndexColumnName :* Si `with_itemindex` est spécifié, la sortie inclut une colonne supplémentaire (nommée *IndexColumnName*), qui contient l’index (à partir de 0) de l’élément dans la collection développée d’origine. 

**Retourne**

Plusieurs lignes pour chacune des valeurs d’un tableau qui se trouvent dans la colonne nommée ou dans l’expression de tableau.
Si plusieurs colonnes ou expressions sont spécifiées, elles sont développées en parallèle. Pour chaque ligne d’entrée, il y aura autant de lignes de sortie qu’il y a d’éléments dans l’expression développée la plus longue (les listes plus courtes sont complétées par des valeurs null). Si la valeur d’une ligne est un tableau vide, la ligne se développe en Nothing (ne s’affiche pas dans le jeu de résultats). Toutefois, si la valeur d’une ligne n’est pas un tableau, la ligne est conservée telle quelle dans le jeu de résultats. 

La colonne développée est toujours de type dynamique. Utilisez une conversion telle que `todatetime()` ou `tolong()` si vous souhaitez calculer ou agréger des valeurs.

Deux modes de développement de conteneurs de propriétés sont pris en charge :
* `bagexpansion=bag`: les conteneurs de propriétés sont développés en conteneurs de propriétés à entrée unique. Ce mode est l’extension par défaut.
* `bagexpansion=array`: Les conteneurs de propriétés sont développés en structures de tableau de valeurs de clé à deux éléments `[` *key* `,` *value* `]` , ce qui permet d’accéder uniformément aux clés et aux valeurs (par exemple, en exécutant une agrégation de comptage distinct sur les noms de propriété). 

## <a name="examples"></a>Exemples

### <a name="single-column"></a>Une seule colonne

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

### <a name="zipped-two-columns"></a>Deux colonnes compressées

Si vous développez deux colonnes, vous devez d’abord « compresser » les colonnes applicables, puis les développer :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5, 4, 3])]
| mv-expand b, c
```

|a|b|c|
|---|---|---|
|1|{"Prop1" : "a"}|5|
|1|{"Prop2" : "b"}|4|
|1||3|

### <a name="cartesian-product-of-two-columns"></a>Produit cartésien de deux colonnes

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

### <a name="convert-output"></a>Convertir la sortie

Si vous souhaitez forcer la sortie d’un MV-Expand à un certain type (la valeur par défaut est Dynamic), utilisez `to typeof` :

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

La colonne d’avis sort du cadre de la `b` `dynamic` `c` sortie de `int` .

### <a name="using-with_itemindex"></a>Utilisation de with_itemindex

Développement d’un tableau avec `with_itemindex` :

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

* Pour plus d’exemples, consultez [nombre de graphiques d’activités dynamiques dans le temps](./samples.md#chart-concurrent-sessions-over-time) .
* [MV-opérateur apply](./mv-applyoperator.md) .
* [résumez make_list ()](makelist-aggfunction.md), qui est la fonction opposée de MV-Expand.
* le plug-in [bag_unpack ()](bag-unpackplugin.md) permettant de développer des objets JSON dynamiques dans des colonnes à l’aide de clés de conteneur de propriétés.
