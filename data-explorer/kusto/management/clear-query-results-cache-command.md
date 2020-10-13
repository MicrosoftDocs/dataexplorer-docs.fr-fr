---
title: Effacer le cache des résultats de la requête-Azure Explorateur de données
description: Découvrez comment effacer les résultats de requête mis en cache dans Azure Explorateur de données. Découvrez la commande à utiliser et consultez un exemple.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 72678453211ada2a6366b771153eeb11a717d7a3
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002943"
---
# <a name="clear-query-results-cache"></a>Effacer le cache des résultats de requête

Efface tous les [résultats de requête mis en cache](../query/query-results-cache.md) effectués sur la base de données de contexte.

**Syntaxe**

`.clear` `database` `cache` `query_results`

**Retourne**

Cette commande retourne une table avec les colonnes suivantes :

|Colonne    |Type    |Description
|---|---|---
|NodeId|`string`|Identificateur du nœud de cluster.
|Count|`long`|Nombre d’entrées supprimées par le nœud.

**Exemple**

```kusto
.clear database cache queryresults
```

|NodeId|Écritures|
|---|---|
|Nœud1|42
|Nœud2|0
