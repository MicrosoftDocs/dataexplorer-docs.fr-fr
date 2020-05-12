---
title: plug-in bag_unpack-Azure Explorateur de données
description: Cet article décrit bag_unpack plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: fd8b0968d90f1c5239cae80c3be9c2a32d0603d6
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225494"
---
# <a name="bag_unpack-plugin"></a>plug-in bag_unpack

Le `bag_unpack` plug-in décompresse une seule colonne de type `dynamic` en traitant chaque emplacement de niveau supérieur du jeu de propriétés en tant que colonne.

    T | evaluate bag_unpack(col1)

**Syntaxe**

*T* `|` `evaluate` `bag_unpack(` *Colonne* T `,` [ *OutputColumnPrefix* ]`)`

**Arguments**

* *T*: entrée tabulaire dont la *colonne* de colonne doit être décompressée.
* *Column*: colonne de *T* à décompresser. Doit être de type `dynamic`.
* *OutputColumnPrefix*: préfixe commun à ajouter à toutes les colonnes produites par le plug-in.
  facultatif.

**Retourne**

Le `bag_unpack` plug-in retourne une table avec autant d’enregistrements que son entrée tabulaire (*T*). Le schéma de la table est identique à celui de son entrée tabulaire, avec les modifications suivantes :

* La colonne d’entrée spécifiée (*colonne*) est supprimée.

* Le schéma est étendu avec autant de colonnes qu’il y a d’emplacements distincts dans les valeurs de jeu de propriétés de niveau supérieur de *T*. Le nom de chaque colonne correspond au nom de chaque emplacement, éventuellement préfixé par *OutputColumnPrefix*. Son type est soit le type de l’emplacement (si toutes les valeurs du même emplacement ont le même type), soit `dynamic` (si les valeurs diffèrent dans le type).

**Remarques**

Le schéma de sortie du plug-in dépend des valeurs de données, ce qui les rend « imprévisibles » en tant que données elles-mêmes. Par conséquent, plusieurs exécutions du plug-in avec des entrées de données différentes peuvent produire un schéma de sortie différent.

Les données d’entrée du plug-in doivent être telles que le schéma de sortie est conforme à toutes les règles d’un schéma tabulaire. En particulier :

1. Un nom de colonne de sortie ne peut pas être identique à une colonne existante dans l’entrée tabulaire *T* , sauf s’il s’agit de la colonne à décompresser (*colonne*) elle-même, car cela génère deux colonnes portant le même nom.

2. Tous les noms d’emplacements, lorsqu’ils sont préfixés par *OutputColumnPrefix*, doivent être des noms d’entité valides et être conformes aux règles d’attribution de noms d' [identificateurs](./schema-entities/entity-names.md#identifier-naming-rules).

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Smitha", "Age":30}),
]
| evaluate bag_unpack(d)
```

|Nom  |Age|
|------|---|
|Jean  |20 |
|Dave  |40 |
|Smitha|30 |
