---
title: bag_unpack plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit bag_unpack plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 374ff3ca8101e24a53926a78a1b8781447f32dbc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518222"
---
# <a name="bag_unpack-plugin"></a>bag_unpack plugin

Le `bag_unpack` plugin déballe une `dynamic` seule colonne de type en traitant chaque emplacement haut de gamme sac de propriété comme une colonne.

    T | evaluate bag_unpack(col1)

**Syntaxe**

*T* `|` `evaluate` `bag_unpack(` *Column* Colonne `,` T [ *OutputColumnPrefix* ]`)`

**Arguments**

* *T*: L’entrée tabulaire dont *la colonne de colonne* doit être déballée.
* *Colonne*: La colonne de *T* à déballer. Doit être de type `dynamic`.
* *OutputColumnPrefix*: Préfixe commun à ajouter à toutes les colonnes produites par le plugin.
  facultatif.

**Retourne**

Le `bag_unpack` plugin renvoie une table avec autant d’enregistrements que son entrée tabulaire (*T*). Le schéma de la table est le même que celui de son entrée tabulaire avec les modifications suivantes :

* La colonne d’entrée spécifiée (*Colonne*) est supprimée.

* Le schéma est étendu avec autant de colonnes qu’il ya des fentes distinctes dans les valeurs de sac de propriété de haut niveau de *T*. Le nom de chaque colonne correspond au nom de chaque fente, préfixée optionnellement par *OutputColumnPrefix*. Son type est soit le type de fente (si toutes les `dynamic` valeurs d’une même fente ont le même type), ou (si les valeurs diffèrent dans le type).

**Remarques**

Le schéma de sortie du plugin dépend des valeurs de données, ce qui le rend aussi « imprévisible » que les données elles-mêmes. Par conséquent, les exécutions multiples du plugin avec différentes entrées de données peuvent produire des schémas de sortie différents.

Les données d’entrée du plugin doivent être telles que le schéma de sortie se conforme à toutes les règles d’un schéma tabulaire. En particulier :

1. Un nom de colonne de sortie ne peut pas être le même qu’une colonne existante dans l’entrée tabulaire *T* à moins que ce soit la colonne à déballer (*Colonne*) elle-même, car cela produira deux colonnes du même nom.

2. Tous les noms de fente, lorsqu’ils sont préfixés par *OutputColumnPrefix*, doivent être des noms d’entité valides et se conformer aux [règles de nommage de l’identifiant.](./schema-entities/entity-names.md#identifier-naming-rules)

**Exemple**

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
|John  |20 |
|Dave  |40 |
|Smitha Smitha|30 |