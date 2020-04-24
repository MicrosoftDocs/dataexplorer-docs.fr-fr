---
title: consommateur opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de consommation dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 65c2f2befc074042131b5c0d705fa942a1622035
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517117"
---
# <a name="consume-operator"></a>consume, opérateur

Consomme le flux de données tabulaire remis à l’opérateur. 

L’opérateur `consume` est principalement utilisé pour déclencher l’effet secondaire de requête sans réellement retourner les résultats à l’appelant.

```kusto
T | consume
```

**Syntaxe**

`consume`[`decodeblocks` `=` *DécoderBlocks*]

**Arguments**

* *Décodage :* Une valeur Boolean constante. S’il `true`est réglé sur `perftrace` , `true`ou `consume` si la propriété de demande est configurée, l’exploitant ne s’insinuira pas seulement les dossiers à son entrée, mais forcera en fait chaque valeur dans ces dossiers à être décomposée et décodée.

L’opérateur `consume` peut être utilisé pour estimer le coût d’une requête sans réellement livrer les résultats au client.
(L’estimation n’est pas exacte pour `consume` diverses raisons; par `T | consume` exemple, est calculée de façon distributive, donc ne transmettra pas les données de la table entre les nœuds du cluster.)

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->