---
title: external_table ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit external_table () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 30de02ba0ae18fbfd7944a97ad95d78dbe51066b
ms.sourcegitcommit: 08c54dabc1efe3d4e2d2581c4b668a6b73daf855
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2020
ms.locfileid: "89510676"
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

* [Commandes de contrôle générales de table externe](../management/externaltables.md)
* [Créer et modifier des tables externes dans Stockage Azure ou Azure Data Lake](../management/external-tables-azurestorage-azuredatalake.md)
* [Créer et modifier des tables SQL externes](../management/external-sql-tables.md)
