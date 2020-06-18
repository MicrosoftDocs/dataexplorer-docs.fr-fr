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
ms.openlocfilehash: 9ddb94e005e88855bbeddd7d3cf8ab537a42d413
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943081"
---
# <a name="clear-query-results-cache"></a>Effacer le cache des résultats de requête

Efface tous les [résultats de requête mis en cache](../query/query-results-cache.md) effectués sur la base de données de contexte.

**Syntaxe**

`.clear` `database` `cache` `queryresults`

**Retourne**

Cette commande retourne une table avec les colonnes suivantes :

|Colonne    |Type    |Description
|---|---|---
|NodeId|`string`|Identificateur du nœud de cluster.
|Écritures|`long`|Nombre d’entrées effacées par le nœud.

**Exemple**

```kusto
.clear database cache queryresults
```

|NodeId|Écritures|
|---|---|
|Nœud1|42
|Nœud2|0
