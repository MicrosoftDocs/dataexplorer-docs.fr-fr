---
title: utiliser Operator-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’utilisation de l’opérateur dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 4b2a9d100cdb7125ebed21be69433fa3cee3a929
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324531"
---
# <a name="consume-operator"></a>consume, opérateur

Consomme le tabular data stream transmis à l’opérateur. 

L' `consume` opérateur est principalement utilisé pour déclencher l’effet secondaire de la requête sans retourner réellement les résultats à l’appelant.

```kusto
T | consume
```

## <a name="syntax"></a>Syntaxe

`consume` [ `decodeblocks` `=` *DecodeBlocks*]

## <a name="arguments"></a>Arguments

* *DecodeBlocks*: valeur booléenne constante. Si la valeur `true` est, ou si la propriété de la demande `perftrace` a la valeur `true` , l' `consume` opérateur n’énumére pas uniquement les enregistrements à son entrée, mais force en fait la décompression et le décodage de chaque valeur dans ces enregistrements.

L' `consume` opérateur peut être utilisé pour estimer le coût d’une requête sans remettre réellement les résultats au client.
(L’estimation n’est pas exacte pour diverses raisons. par exemple, `consume` est calculé distributively, donc ne `T | consume` transmet pas les données de la table entre les nœuds du cluster.)
