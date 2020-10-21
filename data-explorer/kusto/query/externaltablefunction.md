---
title: external_table ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit external_table () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: b966dbd43d1f40842240eaebf7d4008450e1f746
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343196"
---
# <a name="external_table"></a>external_table()

Fait référence à une [table externe](schema-entities/externaltables.md) par son nom.

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * La `external_table` fonction a des restrictions similaires à celles de la fonction [table](tablefunction.md) .
> * Les [limites de requête](../concepts/querylimits.md) standard s’appliquent également aux requêtes de table externe.

## <a name="syntax"></a>Syntax

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>Arguments

* *TableName*: nom de la table externe interrogée.
  Doit être un littéral de chaîne référençant une table de type externe `blob` `adl` ou `sql` .

* *MappingName*: nom facultatif de l’objet de mappage qui mappe les champs dans le partitions de données (externe) réel aux colonnes générées par cette fonction.

## <a name="next-steps"></a>Étapes suivantes

* [Commandes de contrôle générales de table externe](../management/external-table-commands.md)
* [Créer et modifier des tables externes dans Stockage Azure ou Azure Data Lake](../management/external-tables-azurestorage-azuredatalake.md)
* [Créer et modifier des tables SQL externes](../management/external-sql-tables.md)