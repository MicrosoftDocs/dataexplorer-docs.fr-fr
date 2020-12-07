---
title: Déposez les vues matérialisées-Azure Explorateur de données
description: Cet article décrit la commande DROP MATERIALIZED VIEW dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: yifats
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 312b8dbd15f9ee570d1693f7bdbb77b9988d8207
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320607"
---
# <a name="drop-materialized-view"></a>.drop materialized-view 

Supprime une vue matérialisée.

Requiert des autorisations d’administrateur [de base de données ou de](../access-control/role-based-authorization.md) vue matérialisée.

## <a name="syntax"></a>Syntaxe

`.drop``materialized-view` *MaterializedViewName*

## <a name="properties"></a>Propriétés

| Propriété | Type| Description |
|----------------|-------|-----|
| MaterializedViewName| String| Nom de la vue matérialisée.|

## <a name="returns"></a>Retours

La commande retourne les vues matérialisées restantes dans la base de données, qui est la sortie de la commande [Show MATERIALIZED VIEW](materialized-view-show-commands.md#show-materialized-view) .

## <a name="example"></a> Exemple

```kusto
.drop materialized-view ViewName
```

## <a name="output"></a>Output

|Paramètre de sortie |Type |Description
|---|---|---|
|Nom  |String |Nom de la vue matérialisée.
|SourceTable|String|Table source de la vue matérialisée.
|Requête|String|Requête de vue matérialisée.
|MaterializedTo|DATETIME|Horodateur Max MATERIALIZED ingestion_time () dans la table source. Pour plus d’informations, consultez [fonctionnement des vues matérialisées](materialized-view-overview.md#how-materialized-views-work).
|LastRun|DATETIME |Heure de la dernière exécution de la matérialisation.
|LastRunResult|String|Résultat de la dernière exécution. Retourne `Completed` pour les exécutions réussies, sinon `Failed` .
|IsHealthy|bool|`True` Quand la vue est considérée comme saine ; `False` sinon,. La vue est considérée comme saine si elle a été matérialisée avec succès jusqu’à la dernière heure ( `MaterializedTo` est supérieur à `ago(1h)` ).
|IsEnabled|bool|`True` Lorsque la vue est activée (voir [désactiver ou activer la vue matérialisée](materialized-view-enable-disable.md)).
|Dossier|string|Dossier de la vue matérialisée.
|DocString|string|Chaîne doc de la vue matérialisée.
|AutoUpdateSchema|bool|Indique si la vue est activée pour les mises à jour automatiques.
|EffectiveDateTime|DATETIME|Date et heure d’effet de la vue, déterminées au moment de la création (consultez [`.create materialized-view`](materialized-view-create.md#create-materialized-view) )
