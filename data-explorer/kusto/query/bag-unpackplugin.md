---
title: plug-in bag_unpack-Azure Explorateur de données
description: Cet article décrit bag_unpack plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 1823a9d875c6294f360fbce77fcb5e1d2c968019
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818559"
---
# <a name="bag_unpack-plugin"></a>plug-in bag_unpack

Le `bag_unpack` plug-in décompresse une seule colonne de type `dynamic` en traitant chaque emplacement de niveau supérieur du jeu de propriétés en tant que colonne.

    T | evaluate bag_unpack(col1)

**Syntaxe**

*T* `|` `evaluate` `bag_unpack(` *Colonne* T [ `,` *OutputColumnPrefix* ] [ `,` *columnsConlict* ] [ `,` *ignoredProperties* ]`)`

**Arguments**

* *T*: entrée tabulaire dont la *colonne* de colonne doit être décompressée.
* *Column*: colonne de *T* à décompresser. Doit être de type `dynamic`.
* *OutputColumnPrefix*: préfixe commun à ajouter à toutes les colonnes produites par le plug-in. Cet argument est facultatif.
* *columnsConlict*: sens de la résolution des conflits de colonnes. Cet argument est facultatif. Quand un argument est fourni, il est supposé qu’il s’agit d’un littéral de chaîne correspondant à l’une des valeurs suivantes :
    - `error`-la requête génère une erreur (valeur par défaut)
    - `replace_source`-la colonne source est remplacée
    - `keep_source`-la colonne source est conservée
* *ignoredProperties*: ensemble facultatif de propriétés de conteneur à ignorer. Quand un argument est fourni, il est supposé être une constante de `dynamic` tableau avec un ou plusieurs littéraux de chaîne.

**Retourne**

Le `bag_unpack` plug-in retourne une table avec autant d’enregistrements que son entrée tabulaire (*T*). Le schéma de la table est le même que celui de son entrée tabulaire, avec les modifications suivantes :

* La colonne d’entrée spécifiée (*colonne*) est supprimée.

* Le schéma est étendu avec autant de colonnes qu’il y a d’emplacements distincts dans les valeurs de jeu de propriétés de niveau supérieur de *T*. Le nom de chaque colonne correspond au nom de chaque emplacement, éventuellement préfixé par *OutputColumnPrefix*. Son type est soit le type de l’emplacement (si toutes les valeurs du même emplacement ont le même type), soit `dynamic` (si les valeurs diffèrent dans le type).

**Notes**

Le schéma de sortie du plug-in dépend des valeurs de données, ce qui les rend « imprévisibles » en tant que données elles-mêmes. Par conséquent, plusieurs exécutions du plug-in utilisant des entrées de données différentes peuvent produire un schéma de sortie différent.

Les données d’entrée du plug-in doivent être telles que le schéma de sortie respecte toutes les règles d’un schéma tabulaire. En particulier :

1. Un nom de colonne de sortie ne peut pas être identique à une colonne existante dans l’entrée tabulaire *T* , sauf s’il s’agit de la colonne à décompresser (*colonne*) elle-même, car cela génère deux colonnes portant le même nom.

2. Tous les noms d’emplacements, lorsqu’ils sont préfixés par *OutputColumnPrefix*, doivent être des noms d’entité valides et respecter les règles d’attribution de noms d' [identificateurs](./schema-entities/entity-names.md#identifier-naming-rules).

**Exemples**

Développement d’un conteneur :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d)
```

|Name  |Age|
|------|---|
|Jean  |20 |
|Dave  |40 |
|Jasmine|30 |

Le développement d’un conteneur et l’utilisation `OutputColumnPrefix` de l’option pour produire des noms de colonne commençant par le préfixe « Property_ » :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, 'Property_')
```

|Property_Name|Property_Age|
|---|---|
|Jean|20|
|Dave|40|
|Jasmine|30|

Développement d’un conteneur et utilisation `columnsConlict` de l’option pour résoudre les conflits entre les colonnes et colonnes existantes produites par l' `bag_unpack()` opérateur.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConlict='replace_source') // Use new name
```

|Name|Age|
|---|---|
|Jean|20|
|Dave|40|
|Jasmine|30|

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConlict='keep_source') // Keep old name
```

|Name|Age|
|---|---|
|Old_name|20|
|Old_name|40|
|Old_name|30|

Le développement d’un conteneur et l’utilisation `ignoredProperties` de l’option pour ignorer certaines propriétés existantes dans le conteneur de propriétés.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20, "Address": "Address-1" }),
    dynamic({"Name": "Dave", "Age":40, "Address": "Address-2"}),
    dynamic({"Name": "Jasmine", "Age":30, "Address": "Address-3"}),
]
// Ignore 'Age' and 'Address' properties
| evaluate bag_unpack(d, ignoredProperties=dynamic(['Address', 'Age']))
```

|Name|
|---|
|Jean|
|Dave|
|Jasmine|
