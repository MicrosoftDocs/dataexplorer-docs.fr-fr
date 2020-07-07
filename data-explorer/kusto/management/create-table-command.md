---
title: . Create table-Azure Explorateur de données
description: Cet article décrit la création d’une table dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: b071c4af6bc25650d18b1b66130941f73af551ff
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967092"
---
# <a name="create-table"></a>.create table

Crée une nouvelle table vide.

La commande doit s’exécuter dans le contexte d’une base de données spécifique.

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.create``table` *TableName* ([ColumnName : ColumnType],...)  [ `with` `(` [ `docstring` `=` *Documentation*] [ `,` `folder` `=` *NomDossier*] `)` ]

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
