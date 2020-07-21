---
title: Effacer le cache des résultats de la requête-Azure Explorateur de données
description: Cet article décrit la commande de gestion pour l’effacement du schéma de base de données mis en cache dans Azure Explorateur de données.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 68d6493176696f0241467303f166b8c7160859b7
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565446"
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
