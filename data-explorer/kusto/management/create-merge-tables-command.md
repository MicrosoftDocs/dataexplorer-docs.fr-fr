---
title: . créer-fusionner des tables-Azure Explorateur de données
description: Cet article décrit la commande. créer-fusionner des tables dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f80ea54ece66440dc7a6b40d9d571f04bd3e26b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011514"
---
# <a name="create-merge-tables"></a>. créer-fusionner des tables

Vous permet de créer et d’étendre les schémas de tables existantes en une seule opération en bloc, dans le contexte d’une base de données spécifique.

> [!NOTE]
> Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).
> Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour étendre des tables existantes.

**Syntaxe**

`.create-merge``tables` *TableName1* ([ColumnName : ColumnType],...) [ `,` *TableName2* ([ColumnName : ColumnType],...)...]

* Les tables spécifiées qui n’existent pas sont créées.
* Les schémas des tables spécifiées qui existent déjà sont étendus.
    * Les colonnes inexistantes seront ajoutées à la _fin_ du schéma de la table existante.
    * Les colonnes existantes qui ne sont pas spécifiées dans la commande ne sont pas supprimées du schéma de la table existante.
    * Les colonnes existantes qui sont spécifiées avec un type de données dans la commande qui est différente de celle des schémas de la table existante entraînent un échec. Aucune table n’est créée ou étendue.

**Exemple**

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**Sortie de retour**

| TableName | nom_base_de_données  | Dossier | DocString |
|-----------|---------------|--------|-----------|
| Mylogs)    | Comparaison de la |        |           |
| MyUsers   | Comparaison de la |        |           |
