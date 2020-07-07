---
title: supprimer la colonne-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la suppression d’une colonne dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 770f787493828e897600485c282f3ccb12bb74c4
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966871"
---
# <a name="drop-column"></a>.drop column

Supprime une colonne d’une table.
Pour supprimer plusieurs colonnes d’une table, voir [ci-dessous](#drop-table-columns).

> [!WARNING]
> Cette commande est irréversible. Toutes les données de la colonne supprimée seront supprimées.
> Les commandes ultérieures pour rajouter cette colonne ne seront pas en mesure de restaurer les données.

**Syntaxe**

`.drop``column` *TableName* , `.` *ColumnName*

## <a name="drop-table-columns"></a>supprimer les colonnes de table

Supprime un nombre de colonnes d’une table.

> [!WARNING]
> Cette commande est irréversible. Toutes les données des colonnes supprimées seront supprimées.
> Les commandes ultérieures pour rajouter ces colonnes ne seront pas en mesure de restaurer les données.

**Syntaxe**

`.drop``table` *TableName* `columns` TableName `(` *Col1* [ `,` *col2*]...`)`