---
title: current_database ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit current_database () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d68c35547c840cc1e16224c376e90dfabec296d7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348698"
---
# <a name="current_database"></a>current_database()

Retourne le nom de la base de données dans l’étendue (base de données pour laquelle toutes les entités de requête sont résolues si aucune autre base de données n’est spécifiée).

## <a name="syntax"></a>Syntaxe

`current_database()`

## <a name="returns"></a>Retourne

Nom de la base de données dans l’étendue en tant que valeur de type `string` .

## <a name="example"></a>Exemple

```kusto
print strcat("Database in scope: ", current_database())
```