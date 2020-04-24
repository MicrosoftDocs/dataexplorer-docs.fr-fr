---
title: renommer la colonne - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la colonne de renommant dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 02e692e01bb8eb0abd49c9673b000b722734db90
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520500"
---
# <a name="rename-column"></a>renommer la colonne

Change le nom d’une colonne de table existante.
Pour changer le nom de plusieurs colonnes, voir [ci-dessous](#rename-columns).

**Syntaxe**

`.rename``column` [*DatabaseName* `.`] *TableName* `.` *ColumnExistingName* `to` *ColumnNewName*

Où *DatabaseName*, *TableName*, *ColumnExistingName*, et *ColumnNewName* sont les noms des entités respectives et suivent les [règles de nommage d’identification](../query/schema-entities/entity-names.md).

## <a name="rename-columns"></a>renommer des colonnes

Modifie les noms de plusieurs colonnes existantes dans le même tableau.

**Syntaxe**

`.rename``columns` *Col1* `=` [*DatabaseName* `.` [*TableName* `.` *Col2*]] `,` ...

La commande peut être utilisée pour échanger les noms de deux colonnes (chacune est rebaptisée comme le nom de l’autre).)