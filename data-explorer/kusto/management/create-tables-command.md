---
title: .créer des tables - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .créer des tables dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f76c4d4a2780b58d9596537aea183d026d086fc5
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744028"
---
# <a name="create-tables"></a>.create tables

Crée de nouvelles tables vides comme une opération en vrac.

La commande doit fonctionner dans le contexte d’une base de données spécifique.

Nécessite la [permission de l’utilisateur de la base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.create``tables` *TableName1* ([columnName:columnType], ...) [`,` *TableName2* ([columnName:columnType], ...) ... ]

Si une table existe déjà, la commande retournera le succès.
 
**Exemple** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
