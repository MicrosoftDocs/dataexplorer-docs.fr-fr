---
title: current_database ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit current_database () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b4e0458c78023d35e002910de4c5946260b43b4f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252549"
---
# <a name="current_database"></a>current_database()

Retourne le nom de la base de données dans l’étendue (base de données pour laquelle toutes les entités de requête sont résolues si aucune autre base de données n’est spécifiée).

## <a name="syntax"></a>Syntaxe

`current_database()`

## <a name="returns"></a>Retours

Nom de la base de données dans l’étendue en tant que valeur de type `string` .

## <a name="example"></a>Exemple

```kusto
print strcat("Database in scope: ", current_database())
```