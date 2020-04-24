---
title: .rename table et .rename tables - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .rename table et .rename tables dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: db3e38c562573f52df70979b71fe72d8947158bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520483"
---
# <a name="rename-table-and-rename-tables"></a>.renommer table et .rename tables

Change le nom d’une table existante.

La `.rename tables` commande change le nom d’un certain nombre de tables dans la base de données en tant que transaction unique.

Nécessite [l’autorisation admin de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.rename``table` *OldName* `to` *NewName*

`.rename``tables` *NewName* = *OldName* [`ifexists`] [`,` ...]

> [!NOTE]
> * *OldName* est le nom d’une table existante. Une erreur est soulevée et l’ensemble de la commande échoue (n’a `ifexists` aucun effet) si *OldName* ne nomme pas une table existante, sauf indication de la spécifiée (auquel cas cette partie de la commande de renom est ignorée).
> * *NewName* est le nouveau nom de la table existante qui s’appelait *autrefois OldName*.
> * Si `ifexists` elle est spécifiée, elle modifie le comportement de la commande pour ignorer le changement de nom des parties des tables inexistantes.

**Remarques**

Cette commande fonctionne uniquement sur les tableaux de la base de données.
Les noms de table ne peuvent pas être qualifiés avec les noms de cluster ou de base de données.

Cette commande ne crée pas de nouvelles tables, ni ne supprime les tables existantes.
La transformation décrite par la commande doit être telle que le nombre de tableaux dans la base de données ne change pas.

La commande **prend** en charge l’échange de noms de table, ou des permutations plus complexes, tant qu’ils respectent les règles ci-dessus. Par exemple, ingérer des données dans plusieurs tables de mise en scène, puis les échanger avec des tables existantes en une seule transaction.

**Exemples**

Imaginez une base de `A` `B`données `C`avec `A_TEMP`les tableaux suivants: , , , et .
La commande suivante `A` `A_TEMP` échangera et `A_TEMP` (de sorte `A`que la table sera maintenant `B` `NEWB`appelée , `C` et l’inverse), renommer à , et de garder tel qu’est. 

```
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

La séquence suivante des commandes :
1. Crée une nouvelle table temporaire
1. Remplace une table existante ou non existante par la nouvelle table

```
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```