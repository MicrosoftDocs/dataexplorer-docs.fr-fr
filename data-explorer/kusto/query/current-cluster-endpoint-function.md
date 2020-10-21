---
title: current_cluster_endpoint ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit current_cluster_endpoint () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd35837b0c1affbb2101e6c71a4fa638ef8e466
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252557"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

Retourne le point de terminaison réseau (nom DNS) du cluster en cours d’interrogation.

## <a name="syntax"></a>Syntaxe

`current_cluster_endpoint()`

## <a name="returns"></a>Retours

Point de terminaison réseau (nom DNS) du cluster en cours d’interrogation, en tant que valeur de type `string` .

## <a name="example"></a>Exemple

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```