---
title: colonne de goutte - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la colonne de goutte dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 56ba5499b27b517b3080ee27ac317aa1e0917edd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521214"
---
# <a name="drop-column"></a>colonne de goutte

Retire une colonne d’une table.
Pour laisser tomber plusieurs colonnes d’une table, voir [ci-dessous](#drop-table-columns).

> [!WARNING]
> Cette commande est irréversible. Toutes les données de la colonne supprimée seront supprimées.
> Les commandes futures pour ajouter cette colonne en arrière ne seront pas en mesure de restaurer les données.

**Syntaxe**

`.drop``column` *TableName* `.` *ColumnName*

## <a name="drop-table-columns"></a>colonnes de table de goutte

Supprime un certain nombre de colonnes d’une table.

> [!WARNING]
> Cette commande est irréversible. Toutes les données dans les colonnes supprimées seront supprimées.
> Les commandes futures pour ajouter ces colonnes en arrière ne seront pas en mesure de restaurer les données.

**Syntaxe**

`.drop``table` *TableName* `columns` `(` *Col1* [`,` *Col2*]...`)`