---
title: . créer des tables-Azure Explorateur de données
description: Cet article décrit. créer des tables dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: 28ee7e462245497dd14d3a14a1c0703cc71a8933
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321588"
---
# <a name="create-tables"></a>.create tables

Crée de nouvelles tables vides en tant qu’opération en bloc.

La commande doit s’exécuter dans le contexte d’une base de données spécifique.

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.create``tables` *TableName1* ([ColumnName : ColumnType],...) [ `,` *TableName2* ([ColumnName : ColumnType],...)...] [ `with` `(` `folder` `=` *NomDossier*] `)` ]

Si une table existe déjà, la commande renvoie la réussite.
 
**Exemple** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
