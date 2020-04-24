---
title: . Create table-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la création d’une table dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 25554b5485562179d849e846fc5e71c587815e86
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108420"
---
# <a name="create-table"></a>.create table

Crée une nouvelle table vide.

La commande doit s’exécuter dans le contexte d’une base de données spécifique.

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.create``table` *TableName* ([ColumnName : ColumnType],...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` Documentation] [ `folder` NomDossier] `)`] `=`

Si la table existe déjà, la commande renvoie la réussite.

**Exemple** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**Sortie de retour**

Retourne le schéma de la table au format JSON, comme suit :

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> Pour créer plusieurs tables, utilisez la commande [. Create tables](create-tables-command.md) pour améliorer les performances et réduire la charge sur le cluster.

## <a name="create-merge-table"></a>. Create-fusionner la table

Crée une table ou étend une table existante. 

La commande doit s’exécuter dans le contexte d’une base de données spécifique. 

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.create-merge``table` *TableName* ([ColumnName : ColumnType],...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` Documentation] [ `folder` NomDossier] `)`] `=`

Si la table n’existe pas, fonctionne exactement comme une commande « . Create table ».

Si la table T existe et que vous envoyez une commande « . Create-Merge<columns specification>table T () », procédez comme suit :

* Toute colonne dans <columns specification> qui n’existait pas précédemment dans t sera ajoutée à la fin du schéma de t.
* Toute colonne dans T qui ne se trouve <columns specification> pas dans ne sera pas supprimée de t.
* Toute colonne de <columns specification> qui existe dans T, mais avec un type de données différent, entraîne l’échec de la commande.
