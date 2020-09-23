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
ms.openlocfilehash: 6b06d1807fdfc2ed3edaa06e57436979afce423d
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057158"
---
# <a name="drop-materialized-view"></a>. Drop-vue 

Supprime une vue matérialisée.

Requiert des autorisations d’administrateur [de base de données ou de](../access-control/role-based-authorization.md) vue matérialisée.

## <a name="syntax"></a>Syntaxe

`.drop``materialized-view` *MaterializedViewName*

## <a name="properties"></a>Propriétés

| Propriété | Type| Description |
|----------------|-------|-----|
| MaterializedViewName| Chaîne| Nom de la vue matérialisée.|

## <a name="returns"></a>retourne :

La commande retourne les vues matérialisées restantes dans la base de données, qui est la sortie de la commande [Show MATERIALIZED VIEW](materialized-view-show-commands.md#show-materialized-view) .

## <a name="example"></a>Exemple

```kusto
.drop materialized-view ViewName
```

## <a name="output"></a>Output

|Paramètre de sortie |Type |Description
|---|---|---|
|Nom  |String |Nom de la vue matérialisée.
|SourceTable|Chaîne|Table source de la vue matérialisée.
|Requête|Chaîne|Requête de vue matérialisée.
|MaterializedTo|DATETIME|Horodateur Max MATERIALIZED ingestion_time () dans la table source. Pour plus d’informations, consultez [fonctionnement des vues matérialisées](materialized-view-overview.md#how-materialized-views-work).
|LastRun|DATETIME |Heure de la dernière exécution de la matérialisation.
|LastRunResult|Chaîne|Résultat de la dernière exécution. Retourne `Completed` pour les exécutions réussies, sinon `Failed` .
|IsHealthy|bool|`True` Quand la vue est considérée comme saine ; `False` sinon,. La vue est considérée comme saine si elle a été matérialisée avec succès jusqu’à la dernière heure ( `MaterializedTo` est supérieur à `ago(1h)` ).
|IsEnabled|bool|`True` Lorsque la vue est activée (voir [désactiver ou activer la vue matérialisée](materialized-view-enable-disable.md)).
|Dossier|string|Dossier de la vue matérialisée.
|DocString|string|Chaîne doc de la vue matérialisée.
|AutoUpdateSchema|bool|Indique si la vue est activée pour les mises à jour automatiques.
|EffectiveDateTime|DATETIME|Date et heure d’effet de la vue, déterminées au moment de la création (consultez [. créer une vue matérialisée](materialized-view-create.md#create-materialized-view))
