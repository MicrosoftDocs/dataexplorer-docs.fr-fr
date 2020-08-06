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
ms.openlocfilehash: 13b244eb151d140e3626412188ac9bc9de242cc6
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802976"
---
# <a name="external_table"></a>external_table()

Fait référence à une table externe par son nom.

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * La `external_table` fonction a des restrictions similaires à celles de la fonction [table](tablefunction.md) .
> * [Tables externes](schema-entities/externaltables.md)
> * [Commandes pour la gestion des tables externes](../management/externaltables.md)

## <a name="syntax"></a>Syntaxe

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>Arguments

* *TableName*: nom de la table externe interrogée.
  Doit être un littéral de chaîne référençant une table externe de type `blob` ou `adl` . <!-- TODO: Document data formats supported -->

* *MappingName*: nom facultatif de l’objet de mappage qui mappe les champs dans le partitions de données (externe) réel aux colonnes générées par cette fonction.
