---
title: current_database() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit current_database() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c61717bbc8d202624b36088df5aed2ba3f3a8d2d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516743"
---
# <a name="current_database"></a>current_database()

Renvoie le nom de la base de données dans la portée (base de données contre laquelle toutes les entités de requête sont résolues si aucune autre base de données n’est spécifiée).

**Syntaxe**

`current_database()`

**Retourne**

Le nom de la base de `string`données dans la portée comme une valeur de type .

**Exemple**

```kusto
print strcat("Database in scope: ", current_database())
```