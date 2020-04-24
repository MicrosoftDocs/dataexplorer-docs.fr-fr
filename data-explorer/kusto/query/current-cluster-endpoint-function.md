---
title: current_cluster_endpoint() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit current_cluster_endpoint () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 65ce130b4dd3e0a3125eefc6c410775647f9b964
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516845"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

Retourne le critère de terminaison réseau (nom DNS) du cluster actuel interrogé.

**Syntaxe**

`current_cluster_endpoint()`

**Retourne**

Le critère de terminaison réseau (nom DNS) du cluster `string`actuel étant interrogé, comme une valeur de type .

**Exemple**

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```