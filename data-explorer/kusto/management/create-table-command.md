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
ms.openlocfilehash: 8cfdbe1420745620fcaaf6af81e4f750ca25c1cd
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321605"
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
> Pour créer plusieurs tables, utilisez la [`.create tables`](create-tables-command.md) commande pour obtenir de meilleures performances et une charge inférieure sur le cluster.
