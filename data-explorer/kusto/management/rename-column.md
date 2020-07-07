---
title: renommer la colonne-Azure Explorateur de données | Microsoft Docs
description: Cet article explique comment renommer une colonne dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25d0ff106d406c59c24d26542a8dad1e4992e311
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967381"
---
# <a name="rename-column"></a>. renommer la colonne

Modifie le nom d’une colonne de table existante.
Pour modifier le nom de plusieurs colonnes, voir [ci-dessous](#rename-columns).

**Syntaxe**

`.rename``column`[*DatabaseName* `.` ] *TableName* `.` *ColumnExistingName* `to` *ColumnNewName*

Où *DatabaseName*, *TableName*, *ColumnExistingName*et *ColumnNewName* sont les noms des entités respectives et suivent les [règles d’attribution de noms des identificateurs](../query/schema-entities/entity-names.md).

## <a name="rename-columns"></a>renommer des colonnes

Modifie les noms de plusieurs colonnes existantes dans la même table.

**Syntaxe**

`.rename``columns` *Col1* `=` [*DatabaseName* `.` [*TableName* `.` *col2*]] `,` ...

La commande peut être utilisée pour permuter les noms de deux colonnes (chacune est renommée en son nom).