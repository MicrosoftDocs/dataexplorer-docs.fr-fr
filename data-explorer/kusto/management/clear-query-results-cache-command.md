---
title: Effacer le cache des résultats de la requête-Azure Explorateur de données
description: Cet article décrit la commande de gestion pour l’effacement du schéma de base de données mis en cache dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 27806155d105a109c7419d04d945fbc854c44286
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349922"
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
