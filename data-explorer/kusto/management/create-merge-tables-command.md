---
title: .créer-fusion - Azure Data Explorer ( Microsoft Docs
description: Cet article décrit les tables .create-merge dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 408046e198710c4b825a399fcb90960411de1041
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744449"
---
# <a name="create-merge-tables"></a>.créer-fusion

Permet de créer et/ou d’étendre les schémas des tables existantes dans une seule opération en vrac, dans le cadre d’une base de données spécifique.

Nécessite [l’autorisation de l’utilisateur de base de données,](../management/access-control/role-based-authorization.md)ainsi que [l’autorisation d’administration de table](../management/access-control/role-based-authorization.md) pour étendre les tables existantes.

**Syntaxe**

`.create-merge``tables` *TableName1* ([columnName:columnType], ...) [`,` *TableName2* ([columnName:columnType], ...) ... ]

* Des tableaux spécifiés qui n’existent pas seront créés.
* Les tableaux spécifiés qui existent déjà verront leurs schémas étendus :
    * Des colonnes non existantes seront ajoutées à la _fin_ du schéma de la table existante.
    * Les colonnes existantes qui ne sont pas spécifiées dans la commande ne seront pas supprimées du schéma de la table existante.
    * Les colonnes existantes qui sont spécifiées avec un type de données différent dans la commande à celle dans le schéma du tableau existant conduira à un échec (aucune table ne sera créée ou étendue).

**Exemple** 

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**Sortie de retour**

| TableName | nom_base_de_données  | Dossier | DocString (en) |
|-----------|---------------|--------|-----------|
| MyLogs (En)    | TopComparison TopComparison |        |           |
| MyUsers (en)   | TopComparison TopComparison |        |           |
