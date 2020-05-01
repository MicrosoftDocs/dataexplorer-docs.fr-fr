---
title: . Renommez les tables table et. Rename-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. renommer les tables table et. Rename dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a1644021d1e2df294782d3b24e111d3c57af5f91
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617576"
---
# <a name="rename-table-and-rename-tables"></a>. renommer les tables table et. Rename

Modifie le nom d’une table existante.

La `.rename tables` commande modifie le nom d’un certain nombre de tables dans la base de données sous la forme d’une transaction unique.

Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.rename``table` *OldName* OldName `to` *NewName*

`.rename``tables` *NewName*NewName = *OldName* [`ifexists`] [`,` ...]

> [!NOTE]
> * *OldName* est le nom d’une table existante. Une erreur est générée et l’intégralité de la commande échoue (n’a aucun effet) si *OldName* ne nomme pas une table `ifexists` existante, sauf si est spécifié (dans ce cas, cette partie de la commande Rename est ignorée).
> * *NewName* est le nouveau nom de la table existante qui s’appelait *OldName*.
> * Si `ifexists` est spécifié, il modifie le comportement de la commande pour ignorer le changement de nom des parties de tables inexistantes.

**Remarques**

Cette commande fonctionne uniquement sur les tables de la base de données dans l’étendue.
Les noms de tables ne peuvent pas être qualifiés avec des noms de cluster ou de base de données.

Cette commande ne crée pas de tables et ne supprime pas les tables existantes.
La transformation décrite par la commande doit être telle que le nombre de tables dans la base de données ne change pas.

La commande **prend** en charge l’échange de noms de tables ou de permutations plus complexes, à condition qu’elles adhèrent aux règles ci-dessus. Par exemple, ingérer des données dans plusieurs tables de mise en lots, puis les échanger avec des tables existantes dans une transaction unique.

**Exemples**

Imaginez une base de données avec les tables `A`suivantes `B`: `C`,, `A_TEMP`et.
La commande suivante `A` permute et `A_TEMP` (afin que la `A_TEMP` table soit appelée `A`maintenant, et inversement `B` `NEWB`), renomme et conserve `C` en l’aspect. 

```kusto
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

La séquence de commandes suivante :
1. Crée une table temporaire
1. Remplace une table existante ou inexistante par la nouvelle table

```kusto
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```
