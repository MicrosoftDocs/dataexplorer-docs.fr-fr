---
title: materialized_view () (fonction Scope)-Azure Explorateur de données
description: Cet article décrit la fonction materialized_view () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: e8dee5e1c33328eb70f984b20f61d4585abae550
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057125"
---
# <a name="materialized_view-function"></a>fonction materialized_view ()

Fait référence à la partie matérialisée d’une [vue matérialisée](../management/materialized-views/materialized-view-overview.md). 

La `materialized_view()` fonction prend en charge l’interrogation de la partie *matérialisée* uniquement de la vue, tout en spécifiant la latence maximale que l’utilisateur est disposé à tolérer. Cette option n’est pas garantie de retourner les enregistrements les plus récents, mais doit toujours être plus performante que l’interrogation de la vue entière. Cette fonction est utile pour les scénarios dans lesquels vous êtes disposé à sacrifier une actualisation des performances, par exemple dans les tableaux de bord de télémétrie.

<!--- csl --->
```
materialized_view('ViewName')
```

## <a name="syntax"></a>Syntaxe

`materialized_view(`*ViewName* `,` [*max_age*]`)`

## <a name="arguments"></a>Arguments

* *ViewName*: nom du `materialized view` .
* *max_age*: facultatif. S’il n’est pas fourni, seule la partie *matérialisée* de la vue est retournée. Si elle est fournie, la fonction retourne la partie _matérialisée_ de la vue si la dernière heure de matérialisation est supérieure à [ @now -max_age]. Dans le cas contraire, la vue entière est retournée (identique à l’interrogation directe de *viewName* ). 

## <a name="examples"></a>Exemples

Interrogez la partie *matérialisée* de la vue uniquement, indépendamment du moment où elle a été matérialisée pour la dernière fois.

<!-- csl -->
```
materialized_view("ViewName")
```

Interrogez la partie *matérialisée* uniquement si elle a été matérialisée au cours des 10 dernières minutes. Si la partie matérialisée est plus de 10 minutes, retournez la vue complète. Cette option est censée être moins performante que l’interrogation de la partie matérialisée.

<!-- csl -->
```
materialized_view("ViewName", 10m)
```

## <a name="notes"></a>Notes

* Une fois qu’une vue est créée, elle peut être interrogée comme n’importe quelle autre table de la base de données, y compris pour participer à des requêtes entre clusters/bases de données croisées.
* Les vues matérialisées ne sont pas incluses dans les recherches ou les unions génériques.
* La syntaxe de l’interrogation de la vue est le nom de la vue (par exemple, une référence de table).
* L’interrogation de la vue matérialisée renverra toujours les résultats les plus récents, en fonction de tous les enregistrements ingérés dans la table source. La requête combine la partie matérialisée de la vue avec tous les enregistrements dématérialisés de la table source. Pour plus d’informations, consultez en [arrière-plan](../management/materialized-views/materialized-view-overview.md#how-materialized-views-work) pour plus d’informations.
