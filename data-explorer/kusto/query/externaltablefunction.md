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
ms.openlocfilehash: 4a3a1150996000742f5065df0eddc385074eaa48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348086"
---
# <a name="external_table"></a>external_table()

Fait référence à une table externe par son nom.

```kusto
external_table('StormEvent')
```

## <a name="syntax"></a>Syntaxe

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>Arguments

* *TableName*: nom de la table externe interrogée.
  Doit être un littéral de chaîne référençant une table externe de type `blob` ou `adl` . <!-- TODO: Document data formats supported -->

* *MappingName*: nom facultatif de l’objet de mappage qui mappe les champs dans le partitions de données (externe) réel aux colonnes générées par cette fonction.

**Notes**

Pour plus d’informations sur les tables externes, consultez [tables externes](schema-entities/externaltables.md) .

Consultez également [les commandes de gestion des tables externes](../management/externaltables.md).

La `external_table` fonction a des restrictions similaires à celles de la fonction [table](tablefunction.md) .