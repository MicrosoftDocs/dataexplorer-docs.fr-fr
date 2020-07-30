---
title: current_cluster_endpoint ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit current_cluster_endpoint () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2c3ddbee55e729ae8afbb6c1fbcc213bd6bfd9ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348715"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

Retourne le point de terminaison réseau (nom DNS) du cluster en cours d’interrogation.

## <a name="syntax"></a>Syntaxe

`current_cluster_endpoint()`

## <a name="returns"></a>Retourne

Point de terminaison réseau (nom DNS) du cluster en cours d’interrogation, en tant que valeur de type `string` .

## <a name="example"></a>Exemple

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```