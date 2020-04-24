---
title: Colonnes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit des colonnes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 17406eb94f8e29f4b8ab87653ab41df737fa15ef
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509484"
---
# <a name="columns"></a>Colonnes

Chaque [table](tables.md) à Kusto, et chaque flux de données tabulaire, est une grille rectangulaire de colonnes et de rangées. Chaque colonne dans le tableau a un nom et un [type de données scalaire](../scalar-data-types/index.md)spécifique . Les colonnes d’une table ou d’un flux de données tabulaires sont commandées, de sorte qu’une colonne a également une position spécifique dans la collection de colonnes de la table.

**Remarques**  

* Les noms de colonnes sont sensibles aux cas.
* Les noms de colonne suivent les règles pour les [noms d’entités](./entity-names.md).
* La limite maximale de colonnes par tableau est de 10 000.

Dans les requêtes, les colonnes sont généralement des références par nom seulement. Ils ne peuvent apparaître que dans les expressions, et l’opérateur de requête sous lequel l’expression apparaît détermine le flux de données de table ou de tabulaire, de sorte que le nom de la colonne n’a pas besoin d’être plus étendue. Par exemple, dans la requête suivante, nous avons un flux de données tabulaires `c`anonyme (défini par l’opérateur [de datatable](../datatableoperator.md) qui a une seule colonne, . Le flux de données tabulaires est ensuite filtré par un prédicat sur la valeur de cette colonne, produisant un nouveau flux de données tabulaires anonyme avec les mêmes colonnes, mais moins de lignes. [L’opérateur en tant qu’opérateur](../asoperator.md) nomme alors le flux de données tabulaire et sa valeur est retournée comme les résultats de la requête.
Notez en particulier `c` comment la colonne est référencée par son nom sans qu’il soit nécessaire de référencer son conteneur (en fait, ce conteneur n’a pas de nom) :

```kusto
datatable (c:int) [int(-1), 0, 1, 2, 3]
| where c*c >= 2
| as Result
```

> Comme c’est souvent le courant dans le monde des bases de données relationnelles, les lignes sont parfois appelées **enregistrements** et les colonnes sont parfois **appelées attributs**.

Les détails sur la gestion des colonnes peuvent être trouvés sous [les colonnes de gestion](../../management/columns.md).