---
title: . créer des tables-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. créer des tables dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: ff8b3cfae6d3364b4d094f588c8130761fa5cb31
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966990"
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
