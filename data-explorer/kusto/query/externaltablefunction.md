---
title: external_table() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit external_table () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 9fd03fb3c8452702c3db27a5e0466e8608c04eb9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515451"
---
# <a name="external_table"></a>external_table()

Références d’une table externe par nom.

```kusto
external_table('StormEvent')
```

**Syntaxe**

`external_table``(` *TableName* `,` [ *MappingName* ]`)`

**Arguments**

* *TableName*: Le nom de la table externe en cours de question.
  Doit être une chaîne littérale référence à `blob` `adl`une table externe de nature ou . <!-- TODO: Document data formats supported -->

* *MappingName*: Nom facultatif de l’objet cartographique qui cartographie les champs dans les éclats réels (externes) de données aux colonnes de sortie par cette fonction.

**Remarques**

Voir [les tableaux externes](schema-entities/externaltables.md) pour plus d’informations sur les tables externes.

Voir aussi [les commandes pour la gestion des tables externes](../management/externaltables.md).

La `external_table` fonction a des restrictions similaires à la fonction de [table.](tablefunction.md)