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
ms.openlocfilehash: 956c24fa70df89f5bf99d4de12a8b07da6cb6912
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359979"
---
# <a name="mv-expand-operator"></a>mv-expand, opérateur

Développe des tableaux dynamiques à valeurs multiples ou des conteneurs de propriétés en plusieurs enregistrements.

`mv-expand` peut être décrit comme l’opposé des opérateurs d’agrégation qui rassemblent plusieurs valeurs en un seul tableau ou un seul conteneur de propriétés de type [dynamique](./scalar-data-types/dynamic.md), comme `summarize` ... `make-list()` et `make-series`.
Chaque élément dans le tableau (scalaire) ou le conteneur de propriétés génère un nouvel enregistrement dans la sortie de l’opérateur. Toutes les colonnes de l’entrée qui ne sont pas développées sont dupliquées dans tous les enregistrements de la sortie.

## <a name="syntax"></a>Syntaxe

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*nom_colonne_index*] *nom_colonne* [`to typeof(` *nom_type*`)`] [`,` *nom_colonne* ...] [`limit` *limite_lignes*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] *nom* `=` *expression_tableau* [`to typeof(`*nom_type*`)`] [, [*nom* `=`] *expression_tableau* [`to typeof(`*nom_type*`)`] ...] [`limit` *limite_lignes*]

## <a name="arguments"></a>Arguments

* *nom_colonne*, *expression_tableau* : Une référence de colonne, ou une expression scalaire, avec une valeur de type `dynamic`, qui contient un tableau ou un conteneur de propriétés. Les éléments individuels de plus haut niveau du tableau ou du conteneur de propriétés sont développés en plusieurs enregistrements.<br>
  Quand *expression_tableau* est utilisé et que *nom* n’est pas égal à un nom de colonne d’entrée, la valeur développée est étendue en une nouvelle colonne dans la sortie.
  Sinon, le *nom_colonne* existant est remplacé.

* *Name :* nom de la nouvelle colonne.

* *Typename :* Indique le type sous-jacent des éléments du tableau, qui devient le type de la colonne produite par l’opérateur `mv-expand`. L’opération d’application de type est uniquement cast et n’inclut pas d’analyse ou de conversion de type. Les éléments de tableau qui ne sont pas conformes au type déclaré deviennent des valeurs `null`.

* *RowLimit :* nombre maximal de lignes générées à partir de chaque ligne d’origine. La valeur par défaut est 2147483647. 

  > [!NOTE]
  > `mvexpand` est une forme héritée et obsolète de l’opérateur `mv-expand`. La version héritée a une limite de lignes par défaut de 128.

* *IndexColumnName :* Si `with_itemindex` est spécifié, la sortie inclut une autre colonne (nommée *nom_colonne_index*), qui contient l’index (commençant à 0) de l’élément dans la collection développée d’origine. 

## <a name="returns"></a>Retours

Pour chaque enregistrement de l’entrée, l’opérateur retourne zéro, un ou plusieurs enregistrements dans la sortie, comme déterminé de la façon suivante :

1. Les colonnes d’entrée qui ne sont pas développées apparaissent dans la sortie avec leur valeur d’origine.
   Si un seul enregistrement d’entrée est développé en plusieurs enregistrements de sortie, la valeur est dupliquée dans tous les enregistrements.

1. Pour chaque *nom_colonne* ou *expression_tableau* développé, le nombre d’enregistrements de sortie est déterminé pour chaque valeur, comme expliqué [ci-dessous](#modes-of-expansion). Pour chaque enregistrement d’entrée, le nombre maximal d’enregistrements de sortie est calculé. Tous les tableaux ou conteneurs de propriétés sont développés « en parallèle » afin que les valeurs manquantes (le cas échéant) soient remplacées par des valeurs Null.

1. Si la valeur dynamique est Null, un seul enregistrement est produit pour cette valeur (Null).
   Si la valeur dynamique est un tableau ou un conteneur de propriétés vide, aucun enregistrement n’est produit pour cette valeur.
   Sinon, il est produit autant d’enregistrements qu’il y a d’éléments dans la valeur dynamique.

Les colonnes développées sont de type `dynamic`, sauf si elles sont typées explicitement avec la clause `to typeof()`.

### <a name="modes-of-expansion"></a>Modes d’expansion

Deux modes d’expansion de conteneur de propriétés sont pris en charge :

* `bagexpansion=bag` ou `kind=bag` : Les conteneurs de propriétés sont développés en conteneurs de propriétés à entrée unique. Il s'agit du mode par défaut.
* `bagexpansion=array` ou `kind=array` : Les conteneurs de propriétés sont développés en structures de tableau à deux éléments `[`*clé*`,`*valeur*`]`, permettant un accès uniforme aux clés et aux valeurs. Ce mode permet aussi par exemple d’effectuer une agrégation avec comptage distinct sur les noms de propriété. 

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

Pour forcer la sortie d’un opérateur mv-expand avec un certain type (le type par défaut est « dynamic »), utilisez `to typeof` :

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

Notez que la colonne `b` est retournée avec le type `dynamic`, tandis que `c` est retournée avec le type `int`.

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
