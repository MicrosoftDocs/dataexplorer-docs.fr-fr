---
title: .créer la table - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .créer table dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 1c84b9b6cec5a5bb7ab620871f59c188bf71cef4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744238"
---
# <a name="create-table"></a>.create table

Crée une nouvelle table vide.

La commande doit fonctionner dans le contexte d’une base de données spécifique.

Nécessite la [permission de l’utilisateur de la base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.create``table` *TableName* ([columnName:columnType], ...)  [`with` `(``docstring` `=` *Documentation*]`,` `folder` `=` [ *FolderName*] `)`]

Si la table existe déjà, la commande reviendra avec succès.

**Exemple** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**Sortie de retour**

Retourne le schéma de la table en format JSON, tout comme :

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> Pour créer plusieurs tables, utilisez la commande [de tables .créer](/create-tables.md) pour une meilleure performance et une charge plus faible sur le cluster.

## <a name="create-merge-table"></a>.créer-fusion table

Crée une nouvelle table ou étend une table existante. 

La commande doit fonctionner dans le contexte d’une base de données spécifique. 

Nécessite la [permission de l’utilisateur de la base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.create-merge``table` *TableName* ([columnName:columnType], ...)  [`with` `(``docstring` `=` *Documentation*]`,` `folder` `=` [ *FolderName*] `)`]

Si la table n’existe pas, fonctionne exactement comme ".créer table" commande.

Si le tableau T existe, et que vous envoyez<columns specification>une commande de la table T de fusion « créer-fusion », alors :

* Toute colonne <columns specification> qui n’existait pas auparavant en T sera ajoutée à la fin du schéma de T.
* Toute colonne en T <columns specification> qui n’est pas en ne sera pas supprimée de T.
* Toute colonne <columns specification> dans qui existe en T, mais avec un type de données différent entraînera l’échec de la commande.
