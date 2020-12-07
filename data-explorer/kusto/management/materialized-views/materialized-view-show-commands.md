---
title: afficher les commandes de vue matérialisée-Azure Explorateur de données
description: Cet article décrit les commandes afficher les vues matérialisées dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 0ee41d8aba05eb9b5bf3bc6db3206524fdb5ec0d
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321299"
---
# <a name="show-materialized-views-commands"></a>. afficher les commandes matérialisées-views

Les commandes Show suivantes affichent des informations sur les [vues matérialisées](materialized-view-overview.md).

## <a name="show-materialized-view"></a>. afficher la vue matérialisée

Affiche des informations sur la définition de la vue matérialisée et son état actuel.

### <a name="syntax"></a>Syntaxe

`.show``materialized-view` *MaterializedViewName*

`.show` `materialized-views`

### <a name="properties"></a>Propriétés

|Propriété|Type|Description
|----------------|-------|---|
|MaterializedViewName|String|Nom de la vue matérialisée.|

### <a name="example"></a> Exemple

```kusto
.show materialized-view ViewName
```

### <a name="output"></a>Output

|Paramètre de sortie |Type |Description
|---|---|---
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
|EffectiveDateTime|DATETIME|Date et heure d’effet de la vue, déterminées au moment de la création (consultez [`.create materialized-view`](materialized-view-create.md#create-materialized-view) ).

## <a name="show-materialized-view-schema"></a>. afficher le schéma de vue matérialisée

Retourne le schéma de la vue matérialisée dans le code CSL/JSON.

### <a name="syntax"></a>Syntaxe

`.show``materialized-view` *MaterializedViewName*`cslschema`

`.show``materialized-view` *MaterializedViewName* `schema` MaterializedViewName `as``json`

`.show``materialized-view` *MaterializedViewName* `schema` MaterializedViewName `as``csl`

### <a name="output-parameters"></a>Paramètres de sortie

| Paramètre de sortie | Type   | Description                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | Nom de la vue matérialisée.                        |
| Schéma           | Chaîne | Schéma de la vue matérialisée CSL                          |
| nom_base_de_données     | String | Base de données à laquelle appartient la vue matérialisée       |
| Dossier           | String | Dossier de la vue matérialisée                                |
| DocString        | String | Docstring de la vue matérialisée                             |

## <a name="show-materialized-view-extents"></a>. afficher les étendues de vue matérialisée

Retourne les étendues de la partie *matérialisée* de la vue matérialisée. Pour obtenir une définition de la partie *matérialisée* , consultez [fonctionnement des vues matérialisées](materialized-view-overview.md#how-materialized-views-work).

Cette commande fournit les mêmes détails que la commande [afficher les étendues de table](../show-extents.md#table-level) .

### <a name="syntax"></a>Syntaxe

`.show``materialized-view` *MaterializedViewName* `extents` [ `hot` ]
 
## <a name="show-materialized-view-failures"></a>. afficher les échecs de vue matérialisée

Retourne les erreurs qui se sont produites dans le cadre du processus de matérialisation de la vue matérialisée.

### <a name="syntax"></a>Syntaxe

`.show``materialized-view` *MaterializedViewName*`failures`

### <a name="properties"></a>Propriétés

|Propriété|Type|Description
|----------------|-------|---|
|MaterializedViewName|String|Nom de la vue matérialisée.|

### <a name="output"></a>Output

|Paramètre de sortie |Type |Description
|---|---|---
|Nom  |Timestamp |Horodateur de l’échec.
|OperationId  |String |ID d’opération de l’exécution qui a échoué.
|Nom|String|Nom de la vue matérialisée.
|LastSuccessRun|DATETIME|Horodateur de la dernière exécution qui s’est terminée avec succès.
|FailureKind|String|Type d’échec.
|Détails|String|Détails de l’échec.

